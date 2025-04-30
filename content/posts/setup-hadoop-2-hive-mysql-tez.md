---
title: How to setup Single Node Hive on Tez Hadoop Cluster
date: 2020-04-28T08:11:36+05:30
lsatmod: 2022-03-22T17:50:00+05:30
description: Steps to setup Hadoop 2, Hive 2.1.1, Hive with Mysql, Tez and Tez UI on Single Node
slug: setup-hadoop-2-hive-mysql-tez
categories:
  - Big Data
tags:
  - hadoop
  - hive
  - mysql
  - tez
---
In this tutorial I'm going to show you how setup single node Hadoop cluster with hive and tez. We are going to use the following versions -

Hadoop - 2.7.2  
Hive - 2.1.1  
Tez - 0.9.2

## Preriquisites :
1. **Java 8 must be installed and set `JAVA_HOME` path.**
	If you are using ubuntu then you can install java by using the below command
	```bash
	sudo apt install openjdk-8-jdk
	```
	After that you can set `JAVA_HOME` path by adding the below line in `.bashrc` file.
	```bash
	export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
	```
	The `.bashrc` file is located in your home directory. It is a hidden file. In ubuntu you can view hidden files by using `Ctrl` `+` `H` shortcut.

2. **Passwordless ssh must be set**

	To set up passwordless ssh you can follow my previous article to [setup ssh](/passwordless-ssh-ubuntu-linux.html).

3. **Mysql server must be installed**

	MySql is necessary for hive setup. If you just want to setup hadoop and not hive then you can skip this step. In ubuntu mysql can be setup using the below command
	```bash
	sudo apt install mysql-server
	```

## Setup Hadoop

1. Create a directory called packages in your home directory  
2. Download [Hadoop binary](https://archive.apache.org/dist/hadoop/core/hadoop-2.7.2/hadoop-2.7.2.tar.gz) and extract to packages/hadoop  
3. In `packages/hadoop/etc/hadoop` directory replace `core-site.xml`, `hdfs-site.xml`, `mapred-site.xml` and `yarn-site.xml` with the following content. **Don't forget** to change your hostname and username

	**`core-site.xml`**

	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
	<configuration>
	<property>
		<name>fs.default.name</name>
		<value>hdfs://hostname:9000</value>
	</property>
	<property>
		<name>hadoop.tmp.dir</name>
		<value>/home/username/packages/hadoop/tmp</value>
	</property>
	<property>
		<name>hadoop.http.staticuser.user</name>
		<value>username</value>
	</property>
	<property>
		<name>hadoop.proxyuser.username.groups</name>
		<value>*</value>
	</property>
	<property>
		<name>hadoop.proxyuser.username.hosts</name>
		<value>*</value>
	</property>
	</configuration>
	```


	**`hdfs-site.xml`**
	```xml
	<?xml version="1.0" encoding="UTF-8"?>
	<?xml-stylesheet type="text/xsl" href="configuration.xsl"?>
	<configuration>
	<property>
		<name>dfs.replication</name>
		<value>1</value>
	</property>
	<property>
		<name>dfs.name.dir</name>
		<value>file:///home/username/packages/hadoop/hdfs/namenode</value>
	</property>
	<property>
		<name>dfs.data.dir</name>
		<value>file:///home/username/packages/hadoop/hdfs/datanode</value>
	</property>
	</configuration>
	```


	**`mapred-site.xml`**

	```xml
	<configuration>
	<property>
		<name>mapreduce.framework.name</name>
		<value>yarn</value>
	</property>
	</configuration>
	```


	**`yarn-site.xml`**

	```xml
	<?xml version="1.0" encoding="UTF-8" ?>
	<configuration>
	<property>
		<name>yarn.nodemanager.aux-services</name>
		<value>mapreduce_shuffle</value>
	</property>
	<property>
		<name>yarn.timeline-service.enabled</name>
		<value>true</value>
	</property>
	<property>
		<name>yarn.resourcemanager.system-metrics-publisher.enabled</name>
		<value>true</value>
	</property>
	<property>
		<name>yarn.timeline-service.http-cross-origin.enabled</name>
		<value>true</value>
	</property>
	<property>
		<name>yarn.timeline-service.hostname</name>
		<value>hostname</value>
	</property>
	<property>
		<name>yarn.nodemanager.vmem-check-enabled</name>
		<value>false</value>
	</property>
	<property>
		<name>yarn.nodemanager.vmem-pmem-ratio</name>
		<value>4</value>
	</property>
	<property>
		<name>yarn.acl.enable</name>
		<value>false</value>
	</property>
	<property>
		<name>yarn.resourcemanager.system-metrics-publisher.enabled</name>
		<value>true</value>
	</property>
	<property>
		<name>yarn.timeline-service.generic-application-history.enabled</name>
		<value>true</value>
	</property>
	</configuration>
	```

4. Along with these xml files there is a file hadoop-env.sh in `packages/hadoop/etc/hadoop/` directory. Open that file and find the line that starts with export JAVA_HOME=. Replace that line with the following
	```bash
	export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
	```

5. Add the below content to .bashrc file

	```bash
	export PACKAGES=$HOME/packages
	export HADOOP_HOME=$PACKAGES/hadoop
	export HADOOP_ROOT=$HADOOP_HOME
	export HADOOP_BIN_PATH=$HADOOP_HOME/bin
	export HADOOP_LIBEXEC_DIR=$HADOOP_HOME/libexec
	export HADOOP_MAPRED_HOME=$HADOOP_HOME
	export HADOOP_COMMON_HOME=$HADOOP_HOME
	export PACKAGES=$HOME/packages
	export HADOOP_HDFS_HOME=$HADOOP_HOME
	export YARN_HOME=$HADOOP_HOME
	export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
	export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
	export HADOOP_INSTALL=$HADOOP_HOME
	export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
	```

6. Now execute below command to format namenode

	```bash
	hadoop namenode -format
	```


7. Your hadoop has been set up. To start hadoop services

	```bash
	start-dfs.sh
	start-yarn.sh
	mr-jobhistory-daemon.sh start historyserver
	yarn-daemon.sh start historyserver
	```


8. To test whether it has been setup, you can execute the below commands

	```bash
	hdfs dfs -mkdir /testdir
	hdfs dfs -ls /
	```

9. You can view hdfs ui by opening the url `http://localhost:50070` and can view resource manager ui using `http://localhost:8088`


## Setup Hive

1. Download [Hive binary](https://archive.apache.org/dist/hive/hive-2.1.1/apache-hive-2.1.1-bin.tar.gz) and extract to packages/hive  
2. Now to setup hive for mysql, create file `hive-site.xml` in `packages/hive/conf` directory and put the below content. Don't forget to change the values according to your requirements (hostname, mysql username, mysql password and mysql database).

	If you do not know how to create mysql user or database, then for ubuntu execute the below commands (don't forget to change your name here) -

	```bash
	sudo mysql

	CREATE USER 'ashish'@'%' identified by 'thisismypassword';
	CREATE USER 'ashish'@'localhost' identified by 'thisismypassword';
	CREATE DATABASE hive;
	GRANT ALL PRIVILEGES ON hive.* TO 'ashish'@'%';
	GRANT ALL PRIVILEGES ON hive.* TO 'ashish'@'localhost';
	```

	**`hive-site.xml`**

	```xml
	<configuration>
		<property>
			<name>javax.jdo.option.ConnectionURL</name>
			<value>jdbc:mysql://hostname/hive2</value>
			<description>metadata is stored in a MySQL server</description>
		</property>
		<property>
			<name>javax.jdo.option.ConnectionDriverName</name>
			<value>com.mysql.cj.jdbc.Driver</value>
			<description>MySQL JDBC driver class</description>
		</property>
		<property>
			<name>javax.jdo.option.ConnectionUserName</name>
			<value>ashish</value>
			<description>user name for connecting to mysql server</description>
		</property>
		<property>
			<name>javax.jdo.option.ConnectionPassword</name>
			<value>thisismypassword</value>
			<description>password for connecting to mysql server</description>
		</property>
		<property>
			<name>hive.server2.enable.doAs</name>
			<value>false</value>
		</property>
		<property>
			<name>hive.exec.pre.hooks</name>
			<value>org.apache.hadoop.hive.ql.hooks.ATSHook</value>
		</property>
		<property>
			<name>hive.exec.post.hooks</name>
			<value>org.apache.hadoop.hive.ql.hooks.ATSHook</value>
		</property>
		<property>
			<name>hive.exec.failure.hooks</name>
			<value>org.apache.hadoop.hive.ql.hooks.ATSHook</value>
		</property>
	</configuration>
	```


3. Open mysql (`mysql -uashish -pthisismypassword`) and execute the below commands

	```sql
	use hive
	source /home/username/packages/hive/scripts/metastore/upgrade/mysql/hive-schema-2.1.0.mysql.sql
	source /home/username/packages/hive/scripts/metastore/upgrade/mysql/hive-txn-schema-2.1.0.mysql.sql
	```


4. Execute the below commands for creating hive related directories in hadoop hdfs

	```bash
	hdfs dfs -mkdir /user/
	hdfs dfs -mkdir /user/hive
	hdfs dfs -mkdir /user/hive/warehouse
	hdfs dfs -mkdir /tmp
	hdfs dfs -chmod g+w /tmp
	hdfs dfs -chmod g+w /user/hive/warehouse
	```

5. Add below content to .bashrc

	```bash
	export HIVE_HOME=$PACKAGES/hive
	export PATH=$PATH:$HIVE_HOME/bin
	```

6. Add mysql driver
	```bash
	cd $HOME/packages/hive/lib
	wget https://repo1.maven.org/maven2/mysql/mysql-connector-java/8.0.27/mysql-connector-java-8.0.27.jar
	```
7. To start hive, run hive server

	```bash
	hive --service hiveserver2 --hiveconf hive.server2.thrift.port=10000 --hiveconf hive.server2.thrift.http.port=10001 --hiveconf hive.root.logger=INFO,console
	```


	Now you can open your hive cli by using command `hive`

## Setup tez

1. Download [Tez binary](https://downloads.apache.org/tez/0.9.2/apache-tez-0.9.2-bin.tar.gz) and extract to packages/tez  
2. In packages/tez/conf directory create file tez-site.xml and put the below content

	**`tez-site.xml`**

	```xml
	<?xml version="1.0" ?>
	<?xml-stylesheet type="text/xsl" href="configuration.xsl" ?>
	<configuration>
	<property>
		<name>tez.lib.uris</name>
		<value>hdfs://hostname:9000/user/tez/share/tez.tar.gz</value>
		<type>string</type>
	</property>
	<property>
		<name>tez.tez-ui.history-url.base</name>
		<value>http://hostname:8080</value>
	</property>
	<property>
		<name>yarn.timeline-service.enabled</name>
		<value>true</value>
	</property>
	<property>
		<name>tez.session.am.dag.submit.timeout.secs</name>
		<value>2</value>
	</property>
	<property>
		<name>tez.history.logging.service.class</name>
		<value>org.apache.tez.dag.history.logging.ats.ATSHistoryLoggingService</value>
	</property>
	</configuration>
	```


	Upload tez files in hdfs

	```bash
	hdfs dfs -mkdir /user/tez
	hdfs dfs -chmod g+w /user/tez
	cd $HOME/packages/tez
	hdfs dfs -put * /user/tez
	```


	In `hive-site.xml` add the property

	```xml
	<property>
	<name>hive.execution.engine</name>
	<value>tez</value>
	</property>
	```


	In mapred-site.xml change the property value of `mapreduce.framework.name` to `yarn-tez`

	Add below content to `.bashrc`

	```bash
	export TEZ_HOME=$PACKAGES/tez
	export TEZ_CONF_DIR=$TEZ_HOME/conf
	export TEZ_JARS=$TEZ_HOME

	# For enabling hive to use the Tez engine
	if [ -z "$HIVE_AUX_JARS_PATH" ]; then
	export HIVE_AUX_JARS_PATH="$TEZ_JARS"
	else
	export HIVE_AUX_JARS_PATH="$HIVE_AUX_JARS_PATH:$TEZ_JARS"
	fi

	export HADOOP_CLASSPATH=${TEZ_CONF_DIR}:${TEZ_JARS}/*:${TEZ_JARS}/lib/*
	```


### To setup Tez UI

1. Download jetty runner jar in tez home

	```bash
	cd $TEZ_HOME
	wget https://repo1.maven.org/maven2/org/eclipse/jetty/jetty-runner/9.2.11.v20150529/jetty-runner-9.2.11.v20150529.jar
	```


2. Run ui

	```bash
	java -jar jetty-runner-9.2.11.v20150529.jar tez-ui-0.9.2.war --port 8080
	```
	Your tez Ui will be running on http://hostname:8080

That's it