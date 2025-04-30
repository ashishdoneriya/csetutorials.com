---
title: Influxdb Tutorial
date: 2016-07-06T07:55:18+05:30
lastmod: 2020-03-22T02:26:00+05:30
description: A Simple tutorial for influx time series database.
slug: influxdb-tutorial
categories:
  - Database
tags:
  - InfluxDB
---
Influxdata is a platform for storing, collecting, visualizing and managing time-series data. Currently Influxdb is the most famous time series database. This article is for version 1.x only.

The following topics that I am going to cover in this article.

1. Influxdb Installation
2. Databases
3. Inserting Data
4. Querying Data
5. Tags in influxdb
6. Group by time Query
7. Deleting measurement or data from measurement
8. Retention Policies

In influxdb the combination of timestamp and tags is composite keys. So lets start.

## Install Influxdb

First of all install influxdb from its [official download page](https://portal.influxdata.com/downloads/). If you donot have admin rights then you can follow my article on how to install influxdb without root access.
[/install-influxdb-without-root-access-linux.html](/install-influxdb-without-root-access-linux.html)

Now open another terminal and start the CLI using command `influx`

```bash

$ influx
Connected to http://localhost:8086 version 0.13.x
InfluxDB shell 0.13.x
>
```


## Databases

**To create a database**

```bash
create database mydb
```


**To view the list of all databases**

```bash
show databases
name: databases
---------------
name
_internal
mydb


```


**To use a particular database**

```bash
use mydb
```


**To delete a particular database**

```bash
drop database mydb
```


## Inserting Data

In influxdb we call tables as measurements and columns as fields.

We don't need to define measurements(tables) and fields(columns). It will create measurements and add columns automatically when we insert data. It is also like a NoSQL database

Format for writing data is

```bash
measurementName field1=value1,field2=value2,field3=value3 timestamp
```


* Position of spaces and comma matters a lot.
* The timestamp is in nanoseconds. If we don't provide timestamp it will assign the local current timestamp (in nanoseconds).
* By default it assumes all the numbers as doubles. For integer value we have to append `i` at the end. 
```bash
> insert measurementName field4=12i
```


* String values should be in double quotes. 
```bash
> insert measurementName field5="qwqw"
```


* For boolean values use t, T, true, True, or TRUE for TRUE, and f, F, false, False, or FALSE for FALSE 
```bash
> insert measurementName field6=T
```


* We can use `\` character for escaping comma, space, equal and other special character in field (field) value

**Note :** Once you write a particular field in a particular datatype, you cannot change it back.

For more details refer official [documentation](https://docs.influxdata.com/influxdb/v0.13/write_protocols/write_syntax/)

## Querying Data

To select all fields from measurement

```bash
select * from measurementName
```


To select particular fields

```bash
select field1, field2 from measurement
```


**Note :** If your mesurement name or field name contains characters such as `.` `#` or `=`, then use double quotes

```bash
select "field1.name", "field2.name" from "measurement.name"
```

I suggest you to always use double quotes because some times influxdb doesn't show you values if you donot use double quotes.

#### Where clause

A typical usage of where clause

```bash
select * from measurement where field1 > 12 and field2 = 'sparta' and time > now() - 1d
```


We can also use `or` logic using separaters `(` and `)`.

Supported comaparaters in influxdb are

* `=` equal to
* `<>` not equal to
* `!=` not equal to
* `>` greater than
* `<` less than
* `=~` matches against
* `!~` doesn’t match against

## Tags in influxdb

In the starting of the article I told you that in influxdb time is the primary key. This is wrong. The truth is In influxdb the combination of timestamp + tags is the primary key (better say composition key).

**What is a tag?**
According to influxdb official documentation tags are used to store metadata. Let me explain in my words. 

Once I had to run a group by query based on a particular field. Then I came to know that you cannot just run group by query on any field. To use group by on a particular field, there are special type of fields known as tags. Its better not to call them fields. Its just for your understanding. 

There can be different values of a field (column) but for a tag, its values should be limited. Lets say I have a measurement called students. Its fields are name, age, height and its tags are gender, grade.

**Format for inserting row having tags in it**

```bash
measurementName,tagKey1=tagValue1,tagKey2=tagValue2 field1=value1,field2=value2,field3=value3 timestamp
```


Example &#8211; 

```bash
insert students,gender=male,grade=B age=23i,name="Ashish",height=11.2
```


**Group by tag query example** 

```bash
select * from students group by grade, gender
```


Output - 

```
name: students
tags: gender=male, grade=B
time			age	height	name
----			---	------	----
1584820680760917555	23	11.2	Ashish
```


## Group by time Query

Suppose you are inserting room temperature value in influxdb for the last 24 hours at regular interval of 1 minute. Now you want to check the behaviour of room temperature that either it is increasing or decreasing.

One way is directly hitting the query `select temperature from stats where time > now() - 24h` and check all the 1440 rows ( 24 * 60).

Another way is getting the average teperature of every hour for all 24 hours. In this way we have to analyze only 24 points. So for getting the type of result we use group by time()

```bash
select mean("temperature") from stats where time > now() - 24h group by time(1h)
```


You can change the aggregate function mean. There are various aggregate functions like mean, max, min, median, count, mode, distinct, sum, stddev, first, last, difference.

You can read about queries in details from [its website](https://docs.influxdata.com/influxdb/v0.13/query_language/).

## Deleting measurement or data from measurement

**To delete a whole measurement**
Syntax : 

```bash
drop measurement measurementName
```


Example -

```bash
drop measurement students
```


**To delete a specific row**
In influxdb you can delete data from a measurement only on the basis on time or tags. You cannot specify field name.
Eg.

```bash
delete from students where time = 1584822445492464254
```


```bash
delete from students where "grade"='B'
```


Here double quotes and single quote matters.

## Retention Policies

Lets say if you want to remove data older than 1 month automatically by influxdb then retention policies are used.

## Tips

If you want Influx cli to show result in column or json format use these commands

```bash
format csv
```


```bash
format json
```
