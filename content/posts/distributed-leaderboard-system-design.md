---
title: Distributed Leaderboard System Design
date: 2022-05-02T07:21:55+05:30
description: Distributed Leaderboard System Design
slug: distributed-leaderboard-system-design
categories:
  - System Design
---
### Requirements
* 100 million requests per second
* Leaderboard should be updated as soon as possible

### Components of the system
* Key-Value database
* Webservers
* Load Balancers
* Cache Service
* Queues

### Data Flow

### Summary
The user's request to update score would go to the webservers. The webserver send the request to the queue. From queue, a set of workers fetch the individual request, fetch the user's current score from the key-value db and adding the increment, updating the new score to the db then send the updated score to the batch queue (combining multiple user's scores). From the batch queue workers would fetch data in batches and send to another queues for batches and the process goes on untill there is just single record. At the end the top 10 record is updated to the cache and db for the webservers to fetch and show it to the ui. 

### Details
1. The user's request would first pass through the load balancer and the load balancer would distribute the requests to the web servers.  
2. The webserver send the request to a queue in async (lets say queue1) and send the success response.  
3. From queue1 there are workers machines which would process each request.  
4. These worker machines will fetch the user's current score from the database (key-value pair because I don't think relational data could scale to that much extent).   
5. It will calculate new score by summing up current score and new points.  
6. After then the new score is updated to the db (These should be a two-phase locking mechanism in case two different worker machines try to update the value, or consistent hashing can be applied by load balancer) and the new score is passed to a queue (lets say queue2).  
7. From queue2, a set of worker machines fetches records, create batch of records and send to another queue3. Then another worker machines fetch those batches in batches process them, calcuate top 10 or 100 and pass to another queue. This process will go on untill it reaches the last queue (at around level 10).  
8. From the last queue the batch will be fetched and saved in the cache and db.  

There is another alternative to this. We can use redis sorted set. For that the steps from **1** to **6** are same. The rest steps are -  
7. From queue2, a set of worker machines fetches records individually and saves them in redis sorted set.  
8. At every fixed interval, a process will remove all record other than top X from redis sorted set, fetch the top X and update to the cache and db. So that the leaderboard could be fetched directly from cache.  

**Source**  
[How we built a Distributed Leaderboard System in a Week](https://engg.glance.com/how-we-built-a-distributed-leaderboard-system-in-a-week-c8b1a63083ed)