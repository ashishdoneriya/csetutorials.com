---
title: Web Crawler Design Techniques
date: 2021-04-03T10:37:44+05:30
description: Key Points regarding web crawler designing techniques.
slug: web-crawler-design-techniques
categories:
  - System Design
---
A web crawler can be used in various services such as

* Search Engines
* Archive Storage
* Piracy Detector
* Web Mining

Lets talk about the data flow first. Lets say we have a set of url as a starting point. So first we fetch the content of those urls. After that we parse the content, check if it is duplicate or not. Then we extract urls and after filtering the urls we add those urls to the queue to again crawling. This is the three line summary.

Lets break this whole process into different tasks and assign them to different components. So the various components used are 

1. Seed URLs
2. URL Frontier
3. HTML Downloader & DNS Resolver
4. Content Parser
5. Duplicate Content Checker
6. Link Extractor
7. URL Filter
8. URL Seen?
9. URL Storage

Let me describe their functions one by one

## Components

### Seed URLs
Seed URLs act as starting point. A good seed url can provide as many links as possible.

### URL Frontier
* URL frontier is a component which acts a storage for URLs to be downloaded. You can assume this as a FIFO queue.
* The url frontier would store the data in both disk and memory because memory space is limited and disk speed is limited. We would use buffers.

### HTML Downloader & DNS Resolver
HTML downloader downloads webpages from the internet whose URLs provided by the URL Frontier. The HTML downloader calls the DNS Resolver to get the IP address of the particular URL.
* The DNS Resolver is very slow and is single threaded. Therefore we require a separate component which is responsible for handling dns queries.
* The crawler cannot send bulk queries to a website to download its content because it may cause a ddos attack and that website could blacklist the web crawler.
* Don't forget to read robots.txt first. Can be cache for better speed.
* Maintain timeout.

### Content Parser
After downloading the web page, this component parses the webpage and also check if it is a valid page or malformed page.

### Duplicate Content Checker
This component checks whether the content is duplicate of already save pages. One way to check the duplicate content is compare the character of each page one by one with all the pages available in the crawler. The another is to check the hash code of the content.

### Link Extractor
This component extracts links from the web pages. Relative urls will be converted to absolute urls.

### URL Filter
* This component filters url.
* The crawler can filter urls based on their lengths (because of spider traps).
* We can also filter blacklisted urls or different types of extentions.

### URL Seen
This component checks whether the extracted url is already processed or not. If it is not processed then the crawler will add the url to the URL Frontier. We can also take help of bloom filter here.


## Things to consider

### DFS vs BFS Approach
Usually BFS approach is used in webcrawlers because suppose if there is a site like wikipedia then it will take very long time to crawl that website.

### Role of URL Frontier in Politeness, Priority & Freshness

#### Politeness
Previously I mentioned that a crawler should avoid sending too many requests to the same site within a short interval. Therefore it is better for the crawler to send request one by one and should add a delay between requests.

To achieve this politeness, divide the fifo queue of URL Frontier into **multiple FIFO queues**. Each queue would belong to urls have same host. After that there is **Queue Selector** which takes urls from those queues and pass to the **Worker Threads** and those worker threads downloads web pages one by one from the same host.

#### Priority
We have to maintain the priority of the pages to download eg. a forum question or an index page etc. We have to assign different priorities to different urls in the same website.

To manage URL priority first every url will be passed to prioritizer who will assign priority to each url and according to its priority, add that url to a queue. There would be different queues for different priorities. After that there is a queue selector who will select urls from queues based on their priorities.
**NOTE :** Priority system comes before politeness.

### Distributed Crawling
Crawl jobs can be distributed into multiple servers for high performance. You can also distribute crawlers geographically for faster download time.

### Server side rendering
Now a days a lot of websites use js frameworks and libraries to generate content dynamically. Therefore the crawler could use server side rendering.