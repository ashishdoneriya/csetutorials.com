---
title: Unique ID generator in distributed systems
date: 2021-03-25T02:30:00+05:30
updated: 2022-12-09T02:30:00+05:30
description: Unique ID geneator system design key points to remember
slug: unique-id-generator-system-design
categories:
  - System Design
tags:
  - snowflake
  - uuid
---
## Key Points
* MySQL auto increment is not sufficient for distributed systems because it is not large enough & would be very challenging generating unique ids across multiple databases with minimal delay;

* Our requirement is the ids should be unique, sortable, max 64 bit (8 bytes ie. could fit in long data type in Java).

## **Three ways using which we can generate**

### UUID
* UUID is 128 bit.
* It has very low probability of collusion.
* It could be generated without coordinated servers.
* It has drawback that is the ids could be non numeric.
* Since our requirement is 64 bits, therefore UUIDs won't be able to satisfy our requirements

### Ticket Server
* [Flickr](https://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/) developed this technique.
* There is a dedicated mysql server database which is used to generate keys.
* All the other servers hit mysql to get the unique keys.
* This approach has drawback. It has single point of failure. In case of that single mysql server machine goes down, the entire system would fail.
* The webservers would hit the below sql command to get the unique key.
	```sql
	REPLACE INTO Tickets64 (stub) VALUES ('a');SELECT id from Tickets64;
	```
	The table creating syntax is -
	```sql
	CREATE TABLE `Tickets64` (
	`id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
	`stub` char(1) NOT NULL DEFAULT '',
	PRIMARY KEY (`id`),
	UNIQUE KEY `stub` (`stub`)
	) ENGINE=InnoDB
	```

### Twitter Snowflake
Twitter snowflake IDs are 64-bit. In this approach the ID is divided into multiple parts - 
* 1 bit - **reserved**
* 41 bit - **for storing timestamp** epoch. Instead of storing milliseconds since 1 January 1970, we will start from 4 Nov 2010 01:42:54 UTC (1288834974657) that is twitter snowflake default epoch.
* 5 bit - **data center id**. Maximum 2^5 = 32 datacenters.
* 5 bit - **machine id**. Maximum 2^5=32 machines per datacenter. This means not every webserver could generate its own key individualy. There has to be a dedicated id generator (distributed key generator).
* 12 bits - **sequence number**. For every request the number gets increased. After every millisecond the number resets to 0.

This id generator would work for around 70 years (if we include that starting 1 bit also). We will put a **load balancer** in this architecture to divide the traffic equally among all servers using round robin. If we want to divide the traffic based on the machine capacity then we could add [virtual nodes](/consistent-hashing-design-techniques.html) also.

### Sonyflake
A Sonyflake ID is composed of - 
* 39 bits for time in units of 10 msec
* 8 bits for a sequence number
* 16 bits for a machine id

As a result, Sonyflake has the following advantages and disadvantages - 
* The lifetime (174 years) is longer than that of Snowflake (69 years)
* It can work in more distributed machines (2^16) than Snowflake (2^10)
* It can generate 2^8 IDs per 10 msec at most in a single machine/thread (slower than Snowflake)

### My Approach

* The key would be of 64-bit.
* We know that there could be 2^40 keys in 64-bit (ie 9 billion billion).
* In this architecture there are two types of key servers - Main Key Servers (very small in number ie less than 10) & Worker Key Servers ( could be many but limited)
* When the request comes to worker key server for getting a key, the worker key server would ask the main key server to allocate the worker key server a range of keys.
* Then the main key server allocates that worker key server 1 million range. Lets say the main key server will tell the worker key server ok you could use keys between 180 million to 181 million minus one keys.
* After giving the key range, the main server will update the range to a persistent db/disk. ie. it would store information that it has allocated 181 keys till now.
* When a new request comes from worker key server to main key server, the main key server would allocate it 181 million to 182 million keys. ie 1 million keys for each new request.
* The main key server(s) would handle the requests in a synchronized way ie. one by one. In case of multiple threads it could ask another set of values for each thread.
* After receiving the keys range from the main key server, the worker key server will distribute the keys in synchronized way. When the limit gets exhausted, the worker key server will again fetch the key ranges from main key server. In case of machine failure the respawned machine will continue after receiving a new set of keys from main server.
* We will put 2 load balances - one for main key servers and another for worker key servers to distribute the traffic.
* This system can be replicated in multiple data centers ie. in every data center there could be a set of main key servers and worker key servers.

## Calculation
* We would assign a set of key range for each data center ( hard coded ).
* Lets suppose there are 200 data centers ( 50 data centers at present, 150 reserved for future).
* So 2^ 40 / 200 = 46116 trillion keys for each data center.
* Since each time a worker take a range of 1 million keys. So it would take around 46 billion requests to exhaust the datacenter keys range which is I think would be sufficient.
* You can change numbers ( ie. no. of data centers, key range etc.) according to your requirements.