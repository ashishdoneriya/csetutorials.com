---
title: How to Design a Key Value Database
date: 2021-03-24T09:29:42+05:30
description: Essential points to consider while designing key-value storage or database.
slug: key-value-storage-design-architecture
categories:
  - System Design
tags:
  - key-value-database
  - cassandra
---

It is a non-relational database. The key must be unique, can be plain text or hashed values. The value can be directly fetched through the key.

## Requirements

* The size of a key value pair is small ie. less than 10 KB.
* **Big Data :** It should be able to store very large amount of data.
* **High Availability :** The system should repond to queries even in case of failures.
* **High Scalability :** The system should be able to scale according to the data and the traffic.
* Latency should be very low.

## Architecture

Since Big Data cannot be stored in a single machine, therefore we'll need multiple machines (nodes).

![key value db](https://csetutorials.com/wp-content/uploads/2024/03/key-value-db.png)

There are various problems that we have to solve like -

* How to store key value in these nodes?
* How to fetch value corresponding to a particular node?
* How to replicate data?
* How to handle temporary and permanent failures?

### How to store data?

Steps to fetch the value for a key -

1. First of all we will use [Consistent Hashing](/consistent-hashing-design-techniques.html) technique to identify which key to store in which node or retrieve the value from which node.
2. We will use [SSTable technique](/sstable-architecture) to store the data in the individual node.

### How to fetch data?

We'll use the same concepts Consistent Hashing & SSTable to fetch value for a key.

### How to replicate data?
The replication is useful for preserving the data in a node. For replicating a key-value, we'll fetch next 2 (or 3, depending on replication factor / number of copies) **distinct server nodes from the hash ring** and save the copies into these nodes. It would be much better if the selected nodes would be of another rack or data center.

### How to handle temporary and permanent failures?

The nodes will use gossip protocol to communicate with each other. Using gossip protocol the nodes would get to know about each other's health. The every node in the cluster(distributed system) would know about every other node.

#### Handling temporary failures

There are two things that we could do in case of temporary failure. First is block all requests for that node which is not good. The second is continue to allow read and write operations using the next replicated node. That nodes would handle all read and write requests for the failed node. When failed node becomes active again then the replicated node would push back the changes in the original node. The nodes uses [Merkle Tree](https://en.wikipedia.org/wiki/Merkle_tree) for finding which data has been added / updated.

#### Handling permanent failures

In case of permanent failure, the replicated node would continue to serve the keys.

## Questions ?
1. **Question :** Since there could be very large number of requests for a node or key, so how could a single node handle such large amount of traffic?  
**Answer :** We will (actually the node will) distribute the traffic in the replicated nodes.

2. **Question :** When we ask the node to store a particular key-value, so when does node replicate data to the other nodes, just immediately after storage request or some time later?  
**Answer :** It depends on our requirement. If There is a very famous concept called [CAP Theorem](https://ashishdoneriya.github.io/cap-theorem.html). In the previous answer I've mentioned that you could distribute the traffic in the replicated nodes.

Lets say your requirement is the data that we receive from the replicated nodes for a key, always must be the latest data. In this case when the write request comes to the node then first the node saves data to itself, then it will replicate the data and at last will send the response to the request. During the time of replication all the request (read and write) would be blocked for that key.

If we are ok with the old data then what node would do is when the write request comes, then it will save the data to itself, send the response to the request and then replicate the data. This is called eventual consistency. In this case the data would be available for other requests also (read and write both).

Lets say

N = No. of Replicas

W = write quorum (For a write operation to be considered as successful, data must be written to atleast W nodes)

R = read quorum (For a read operation to be considered as successful, data must be received from atleast R nodes)

W + R > N , strong consistency because these must be at least one node (overlapping) which'll has the latest data.

R =1 and W = N , fast read

R = N and W = 1, fast write

Usually N = 3, W = 2, R = 2.

The above technique is called Sloppy Quorum.

3. **Question :** During data write, lets say two different requests are writing data at the same time for same keys, what   would you do?  
**Answer :** We will use [Vector Clock](https://en.wikipedia.org/wiki/Vector_clock) techique to resolve the conflicts.

4. **Question :** A client won't directly send data to the correct node related to a key because the client won't know the hashing algorithm or the hash ring. So how exactly the client would write or read data (key-value)?  
**Answer :**  First the client will send the request to any node. Then this node would forward the request to the node which would be close the client node. Then this node would act as a Coordinator node and would handle the request (ie. finding the appropriate node related to key using hash ring and sending the request)