---
title: URL Shortener System Design
date: 2021-03-29T10:36:23+05:30
description: URL Shortener system design key points to remember
slug: url-shortener-system-design
categories:
  - System Design
---
### Requirements
* 100 million writes per day ie. 100 million urls would be shortened per day.
* Only alphanumeric characters are allowed in the shortened URL.
* The system should be highly available, scalable and fault tolerance.

### Estimations
* **Write operations :** 100 million. It means 100000000/(24 * 3600) = 1158 writes per second.
* **Read operations :** Lets say the read operations are 10 times of write operations that is 11580 reads per second.
* Lets suppose the service would run for 10 years. It means we would need around 40 TB of storage.

### Components of the system
* Key-Value database
* Webservers
* Load Balancers
* Cache Service

### Data Flow

### Summary
First the user goes to the shortener website and send post request to the website's server. The webserver creates a short code for that url and in the key value database it save the short code as key and the complete url as a val. If we want the complete url for a short url, then in this case, the user fires a get request to the webserver, the webserver fetches the shortcode value from the key-value db. After getting the complete url, the server would redirect the user to the actual url (301 or 302 depends on the scenario.).

#### Details
1. First the user would go to the shortener website. After that he would submit post request to the website for url shortening using some form in the site.
2. Then the request would hit the load balancer. The load balancer would send the request to a webserver.
3. To short a url, there are two techniques. One is directly create a hash using a hash function. For the the hash value length must be atleast of 7 digits to satisfy out requirement (365 billion urls). After that we would store the hash key and full url as a key-value pair in key-value database. In case of collison add some value of url and check again.
4. The another way to generate the hash is first get a unique id using [unique id generator](/unique-id-generator-system-design.html) and then encode using base62. After that save it in key-value database.
5. Both the above approaches have advantage and drawback. In the first approach there could be collison and in the second we need a unique id generator.
6. After generating a shortcode and saving the complete url in the database, we would show the short url to user (eg. https://your-website/short-code)
7. If another user wants to go to the original url using the shortcode, then at first the another user would hit the url to the browser. After that the request would go to the server. Then the server would geneate the key and fetch the value from the key-value database.
8. After that the server would send 301 permanent redirect request to the user containing original url. If we want to store the number of hits a particular url would receive, then the server would send 302 temporary redirect. The difference between 301 and 302 is the the browers save the 301 reponse in the cache but in case of 302 they don't.

#### [Key Value Storage Consistency](/key-value-storage-design-architecture.html)
In this approach, W = N and R = 1. Here W = write quorum, R = read quorum and N = number of replicas. Kindly read my [previous post](/key-value-storage-design-architecture.html) for more details related to this.

#### Analytics
If you want to store the statistics also then you can use the same key-value db for storing analytics data.