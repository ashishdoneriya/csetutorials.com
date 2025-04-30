---
title: Consistent Hashing Design and Techniques
date: 2021-03-21T07:09:36+05:30
description: Key Points regarding implementation of Consistent Hashing
slug: consistent-hashing-design-techniques
categories:
  - System Design
---
Consistent Hashing is one of the very important concept in the field of system designing. Consistent hashing is used mainly in load balancers, key-value store etc.

Lets suppose You have to create a Load Balancer for your WebApp. I presume you know what is a load balancer.

* One way is to distribute the load one by one on each server as the request comes. The problem in this approach is that if the server was storing some information in its cache or session then the cache or the session would become irrelevant if the same reqeust for that client goes to another server.

* The another way is to assign reqeusts to the servers based on the below function
	```server_number = hash_function(request_key) % total_server```

* It has a drawback. If a new server is added or removed then there would a flood of cache misses.

* The solution for the above problem is **Consistent Hashing**.

* In consistent hashing only k/n keys will be affected if a node is added or removed. Here `k` is number of keys and `n` is number of slots (you can say number of machines as for now).

* Chooses any hash function, lets say we choose `SHA-1`. In this case there are 2^160 hash spaces available.

* Join the first slot and the last slot and it forms a ring. Using that hash function we map servers based on their IP Addresses (or machine names) onto the ring.

* We use the same hash function to map request keys to the slots.

* When we have to find the server related to a particular key, then first we find the hash of that key. After that we move forward in the ring untill we found the hash of a server in that ring. That server will be the server for that key.

* There are two drawbacks of this appoach. The first one is in case when a server is dropped then the whole load comes to its next server. The second drawback is it doesnot guarantee same loading in all servers.

* To counter these two problem there is a technique called **Virtual Nodes**.

* A virtual node is actually a real node. Each server is represented by multiple virtual nodes on the ring. Lets say there are 10 servers and each server has 10 virtual nodes. So in this case in the hash ring, 100 nodes are distributes. Therefore it then removes the problem of load partiality.

* To find the server related to a particular key, we will calculate hash of that key and from that hash we'll move forward in clockwise untill a virtual node comes up in that ring.