---
title: Rate Limiter Design Techniques with Scenarios and Solutions
date: 2021-03-17T09:35:32+05:30
description: Key Points regarding rate limiter designing techniques. Some common problems and various ways to solve them
slug: rate-limiter-design-techniques
categories:
  - System Design
tags:
  - rate-limiter
  - redis
---
Rate Limiting is a very vast topic. It depends on what type of rate limiter you want, what are your requirements. First lets assume there is a lot of traffic coming every second or minute. So we will do all the calculations according to the rate limiter.

**NOTE :** There should be minimal impact of rate limiter on apis latency.

## How to limit the traffic?

To put a control on traffic or a user who is sending a lot of requests, we have to put a middleware (rate limiter) between the internet traffic (ie. client) and the api servers. This rate limiter will decide whether a request should be allowed to hit the api servers or not based on client' ip address or client's details.

### From where does the rate limiter fetch the rule?

Lets say the rules for every user is stored in a database (RDBMS, NoSQL etc.). The data in the database is stored in the disk and the rate limiter is reading the rules from the database. Any request that comes to the rate limiter, it reads the rules from the database every time which is very slow as we know that reading the data from the disk is very slow.

But if we use some inmemory database like redis for reading the rules then the speed would become much faster. Therefore we'll store rules in inmemory database for faster access (and to reduce latency).

There'll be some worker nodes also who will update the rules on the inmemory database.

### What would happen when the rate limiter rejects the request?
When the user/client exceets his/her quota, the rate limiter sends 429 http error code to notify the user that there are too many request he's sending. The rate limiter also sends other info along with 429 code like when can the client again send the request, how many request it can send in a particular period of time.

## How actually rate limiter works?

There are various types of rate limiter techniques.

1. Token Bucket
2. Leaking Bucket
3. Fixed Window counter
4. Sliding window log
5. Sliding Window counter

### Token Bucket
* There is a bucket filled with tokens.
* When a request comes, it takes one token and proceeds.
* If there is no token in the bucket the request would get dropped.
* At regular interval the bucket gets refill.
* There is an overhead of refilling the bucket

### Leaking Bucket
* In leaking bucket there is a queue (bucket).
* All the requests go through that queue.
* The requests in the queue get processed at a fixed rate.
* If a request comes in and the queue is full then the request will be dropped.
* It is suitable for usecases that a stable outflow rate is needed.
* A Burst of traffic fills up the queue with old requests, and if they are not processed in time, recent requests will be rate limited.
* It allows sudden burst of traffic.

### Fixed Window Counter Algorithm
* A counter is maintained for a fixed interval.
* Every request that comes up increase the counter for that interval.
* If the counter value exceeds the max rate then the request will be dropped.
* There could be sudden burst of traffic at the edge of the window in this algorithm.

### Sliding window log algorithm
* This algorithm solves the problem of sudden spike in traffic at the edge of windows.
* In this algorithm we maintain the timestamp of each incoming request.
* When a request arrives then first outdated timestamps will be removed.
* After that the current timestamp will be added.
* Then all the log entries will be counted. If the total log entries is less than the rate then the request would be allowed otherwise it would be dropped.
* This algorithm consumes a lot of memory because even if a request is rejected, its timestamp might still be stored in memory.

### Sliding window counter algorithm
It is hybrid algorithm  that combines the fixed window counter and sliding window log. Lets say the rate limiter allows 7 requests in a minutes. And there are 5 requests in the previous minutes & 3 in the current minute. For a new request that arrives at a 30% position in the current minute, the number of requests in the rolling window is calculated using the following formula.
* Requests in current window + requests in the previous window * overlap percentage of the rolling window and previous window.
* Using this formula we get 3 + 5 * 0.7% = 6.5 = 6.
Since the rate limiter allows a maxium of 7 requests per minutes, the current request can go through.
This algo is memory efficient. But it only works for not-so-strict look back window. The problem is not so bad as it seems. According to an experiment by cloudflare only 0.003% of requests are wrongly allowed or rate limited among 400 million requests.


## Problem Scenarios

### Problem 1 : Max 500 requests in last 1 hour

We can solve this problem in two ways.
1. Sliding window log algorithm.
2. (Partial) Sliding window counter algorithm.

**Approach 1 - Sliding window log algorithm**

Example : [https://github.com/peterkhayes/rolling-rate-limiter](https://github.com/peterkhayes/rolling-rate-limiter)

 We'll use redis for cache. In **redis** there is a feature using which we can **execute a set of operations atomically so as to prevent race condition**. We get the output of the commands after all the commands get executed. We'll used sorted set in redis to store timestamps. In this approach, in the sorted set we will store key as timestamp and value also as timestamp. We can perform atomic operations in redis using `MULTI` command.

Here are the steps - 
1. Execute the steps from 2 to 4 atomically (as far as I understood we canot add an `if condition` in these steps when executing atomically)
2. Delete all timestamps before the interval (There is a function zremrangebyscore using which we can remove those key-values pairs in a sorted set whose values are specified in the function).
3. Get the total elements currently in the set.
4. Add the current timestamp.
5. If the total elements is less than the applied rate then the request can proceed further otherwise it will be dropped.

Advantages of this approach -
1. Strict max upperbound on requests rate.
2. Can handle sudden spike in traffic at the edges of the windows.

Drawbacks of this approach -
1. We have to store timestamps of all the requests (memory consuming).
2. Dropped requests are also counted in this approach and it will affect the user for the next 1 hour.

**Approach 2 - (Partial) Sliding window counter algorithm**

In this process, instead of directly storing the timestamp in the sorted set, we would round off the timestamp in minutes and we'll store no. of requests processed in that minute as value in the sorted set.

Eg. Lets say the request arrives at 11:03:46:123 PM where 11 is the hour, 3 is the minute, 46 is the seconds and 123 is the milliseconds. So instead of putting time stamp of 11:03:46:123 PM, we'll store timestamp of 11:03:00:000 PM ie. we will take floor value.

In previous approach ( Approach 1), in sorted sets we were storing key and value both as timestamp, but in this approach the key will be floor of timestamp and value will be the count (requests processed in that minute).

1. Add a TTL (expiry time) to the set to autoclean memory.
2. Execute steps 3, 4 and 5 atomically.
3. Remove all keys (timestamps) which are older than 1 hour from the sorted set in redis.
4. Increment the value of floor(timestamp) in sorted set (Redis will autocreate the key).
5. Fetch the sorted set.
6. Now sum up all the values (counters) in the set. During sum check if the counter value is more than the max limit per hour (500 in this case), if yes then decrease the current timestamp value counter by 1 and rewrite the counter value in redis.
7. Iif the total sum of counters is less than 500, then allow the request to proceed further otherwise drop the request.

### Problem 2 : Max 10 requests per minute and there should be a gap of 2 second between each request.

**Approach 1**

[https://engineering.classdojo.com/blog/2015/02/06/rolling-rate-limiter/](https://engineering.classdojo.com/blog/2015/02/06/rolling-rate-limiter/)

1. Each user has a sorted set associated with them.
2. Execute the below steps from 3 to 5 atomically (we will be able to read the output only after execution of all the steps.)
3. When a request arrives, drop all elements of the set which occured before a minute ago.
4. Fetch all elements of the set.
5. Add the current timestamp to the set.
6. Count all the elements in the set when all operations are completed. If it exceeds the limit, we would drop the request.
7. Fetch the latest timestamp with the current timestamp. If they are too close ( less than 2 seconds) then we would drop the request.

Advantages of this approach -
1. Strict max upperbound on requests rate.
2. Can handle sudden spike in traffic at the edges of the windows.

Drawbacks of this approach -
1. We have to store timestamps of all the requests (memory consuming).
2. Dropped requests are also counted in this approach and it will affect the user for the next 1 minute.

**Approach 2**

1. Each user has a sorted set associated with them.
2. Execute the below steps from 3 to 5 atomically (we will be able to read the output only after execution of all the steps.)
3. When a request arrives, drop all elements of the set which occured before 2 seconds ago.
4. Get the total size of set.
5. Add the current timestamp to the set.
6. If the set is empty then go to step 7. If set is not empty then drop request.
7. Execute below steps atomically (8 and 9).
8. In redis get value of key floorFuncMinutes(current request timestamp) ( eg. 11:03:46:123 to 11:03:00:000).
9. Increment the value of key floorFuncMinutes(current request timestamp) and set TTL of 1 minute.
10. If the value is less than max limit ( in this case 10 requests per minute ie. 10) then allow the request to proceed further otherwise drop the request.

### Problem 3 : Max 10 requests per minute.

We can solve this problem much like problem 2. 

Approach 1 : Follow steps from 1 to 6 and skip step 7. Advantages and draw back are the same.

Approach 2 : If we follow approach 2 then there could be sudden spike in traffic at the starting and ending of the windows (minutes). In this case we could use (Partial) Sliding window counter algorithm that is counting based on floor of every second ie. 11:03:46:123 to 11:03:46:000. But I think it would be very (time consuming). Therefore you could use directly sliding window alogrithm. ie (3 + 5 * 0.7% = 6.5 = 6)