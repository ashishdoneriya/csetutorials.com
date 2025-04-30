---
title: How to install influxdb without root access on Linux
date: 2018-10-22T09:28:01+05:30
lastmod: 2020-03-04T02:03:00+05:30
description: Steps to setup influxdb time series database on any linux distribution without root access or sudo permissions whether it is aws influxdb or azure
slug: install-influxdb-without-root-access-linux
categories:
  - Database
tags:
  - InfluxDB
  - linux
---
Influxdb time series database is one of the fastest timeseries database. It is current the best aws time series database. Its purely written in go language. It is opensource and is used for recording metrics, events and performing analytics. The steps to install influxdb on various linux distributions (aws influxdb or azure) are available on its [download page](https://portal.influxdata.com/downloads). But there is one problem in install instructions and that is all requires root access. And also there are no instructions to setup influxdb on linux distributions other than Redhat, Debian, Ubuntu and CentOS.

In this post I am going to tell you how to setup influxdb on linux. As of now (Oct 2018) the latest version of influxdb is 1.6.3.

## Steps to setup influxdb

### Download tar.gz file

```bash
wget https://dl.influxdata.com/influxdb/releases/influxdb-1.6.3-static_linux_amd64.tar.gz
```


### Extract the tar.gz file

```bash
tar xvfz influxdb-1.6.3-static_linux_amd64.tar.gz
```


### Go to the extracted folder

```bash
cd influxdb-1.6.3-1/
```


### Remove Unnecessary files

```bash
rm -r usr/
rm influxdb.conf
rm influx_stress
rm influx_tsm
```

If you are going to use `influx_inspect`, `influx_stress` or `influx_tsm` then do not delete these. But if you don't know what are these or don't want to use these then you can delete. In the above commands you can see we have removed influxb.conf file also. This is because we are going to create a new influxdb.conf file in the next step.

### Generate influxdb.conf
Generate a new influxdb.conf file using below commands

```bash
chmod +x influxd
./influxd config > influxdb.conf
```

An influxdb.conf file will be generated in the current directory. This file contains current configuration (like bind ports, data and metadata storage path, enable logs or not etc.) that influxdb is going to use when we start it.

### Change influxdb.conf
Now Open influxdb.conf file with your favourite text editor because we are going to change it.

**(a) Disable anonymous reporting** : Influxdb sends your os stats to its server once in every 24 hours. To disable it set reporting-disabled to false in the first line.

```conf
reporting-disabled = false
```

**(b) Change meta port :** By default influxdb uses 8088 port as meta port. Most of the time this port is occupied by other services (like hadoop). Therefore it is better to change to 9088 or some other free port (in the second line).

```conf
bind-address = "127.0.0.1:9088"
```

**(c) Change http port :** BY defualt influxdb user 8086 port as http port (to expose rest apis) which is also very high demanding port. So if your 8086 port is already used in other services then change this port under http section. Lets change it to 9086 (or any free port)

```conf
[http]
  enabled = true
  bind-address = ":9086"
```


**(d) Disable monitor stats :** Influxdb stores your os stats (like memory, vcores available) in database every 10 second. This is a wastage of space if you are not going to use those stats. To disable it set `store-enabled = false` in monitor section.

```conf
[monitor]
  store-enabled = false
```


**(e) Disable logs :** Personally I don't like influxdb logs very much because they consume disk space. So usually I disable all types of logs. But logs are very useful for the first time because they tell you why influxdb is not able to start. Therefore disable only after when you become familier with influxdb.

**(f) Change data storage paths :** If you want to change the storage path of influxdb for meta data and data then you can change the paths in data field under meta and data sections.

```conf
[meta]
  dir = "/home/ashish/.influxdb/meta"
  retention-autocreate = true
  logging-enabled = true

[data]
  dir = "/home/ashish/.influxdb/data"
```


Save all the changes.

In my case my influxdb.conf look like this

```conf
reporting-disabled = true
bind-address = "127.0.0.1:9088"

[meta]
  dir = "/home/ashish/.influxdb/meta"
  retention-autocreate = true
  logging-enabled = true

[data]
  dir = "/home/ashish/.influxdb/data"
  index-version = "inmem"
  wal-dir = "/home/ashish/.influxdb/wal"
  wal-fsync-delay = "0s"
  query-log-enabled = true
  cache-max-memory-size = 1073741824
  cache-snapshot-memory-size = 26214400
  cache-snapshot-write-cold-duration = "10m0s"
  compact-full-write-cold-duration = "4h0m0s"
  max-series-per-database = 1000000
  max-values-per-tag = 100000
  max-concurrent-compactions = 0
  max-index-log-file-size = 1048576
  trace-logging-enabled = false
  tsm-use-madv-willneed = false

[coordinator]
  write-timeout = "10s"
  max-concurrent-queries = 0
  query-timeout = "0s"
  log-queries-after = "0s"
  max-select-point = 0
  max-select-series = 0
  max-select-buckets = 0

[retention]
  enabled = true
  check-interval = "30m0s"

[shard-precreation]
  enabled = true
  check-interval = "10m0s"
  advance-period = "30m0s"

[monitor]
  store-enabled = false
  store-database = "_internal"
  store-interval = "10s"

[subscriber]
  enabled = true
  http-timeout = "30s"
  insecure-skip-verify = false
  ca-certs = ""
  write-concurrency = 40
  write-buffer-size = 1000

[http]
  enabled = true
  bind-address = ":9086"
  auth-enabled = false
  log-enabled = true
  suppress-write-log = false
  write-tracing = false
  pprof-enabled = true
  debug-pprof-enabled = false
  https-enabled = false
  https-certificate = "/etc/ssl/influxdb.pem"
  https-private-key = ""
  max-row-limit = 0
  max-connection-limit = 0
  shared-secret = ""
  realm = "InfluxDB"
  unix-socket-enabled = false
  unix-socket-permissions = "0777"
  bind-socket = "/var/run/influxdb.sock"
  max-body-size = 25000000
  access-log-path = ""
  max-concurrent-write-limit = 0
  max-enqueued-write-limit = 0
  enqueued-write-timeout = 30000000000

[logging]
  format = "auto"
  level = "info"
  suppress-logo = false

[ifql]
  enabled = false
  log-enabled = true
  bind-address = ":8082"

[[graphite]]
  enabled = false
  bind-address = ":2003"
  database = "graphite"
  retention-policy = ""
  protocol = "tcp"
  batch-size = 5000
  batch-pending = 10
  batch-timeout = "1s"
  consistency-level = "one"
  separator = "."
  udp-read-buffer = 0

[[collectd]]
  enabled = false
  bind-address = ":25826"
  database = "collectd"
  retention-policy = ""
  batch-size = 5000
  batch-pending = 10
  batch-timeout = "10s"
  read-buffer = 0
  typesdb = "/usr/share/collectd/types.db"
  security-level = "none"
  auth-file = "/etc/collectd/auth_file"
  parse-multivalue-plugin = "split"

[[opentsdb]]
  enabled = false
  bind-address = ":4242"
  database = "opentsdb"
  retention-policy = ""
  consistency-level = "one"
  tls-enabled = false
  certificate = "/etc/ssl/influxdb.pem"
  batch-size = 1000
  batch-pending = 5
  batch-timeout = "1s"
  log-point-errors = true

[[udp]]
  enabled = false
  bind-address = ":8089"
  database = "udp"
  retention-policy = ""
  batch-size = 5000
  batch-pending = 10
  read-buffer = 0
  batch-timeout = "1s"
  precision = ""

[continuous_queries]
  log-enabled = true
  enabled = true
  query-stats-enabled = false
  run-interval = "1s"

[tls]
  min-version = ""
  max-version = ""
```


**7. Start influxd service**  
After configuring influxdb its time to start infludb service. start influxd service by using the command below

```bash
./influxd --config influxdb.conf
```


If everything is right, then influxd service will start. In case of any error it will not start and display error.

You can also start influxd service in the background by using the below command

```bash
./influxd --config influxdb.conf /dev/null &
```


**8. Testing**  
Its time to test the service whether it has been properly started or not.  
Execute the command `./influx --port 9086`. Here 9086 is the http port that I have changed in influxdb.conf file before. If you didn't change the file then there is no need to specify port number and you can directly test using `./influx` command

```bash
chmod +x influx
./influx --port 9086
Connected to http://localhost:9086 version 1.6.3
InfluxDB shell version: 1.6.3
```

If you see the message "connected to http..." and a command prompt appears then it means influxdb has been successfully started on your machine. If connection failed message appears then it means either you have specified the wrong port or it means that influxd service has not started yet or some problem occurred while starting influxd service.