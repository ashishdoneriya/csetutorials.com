---
title: 'Setting Up Master-Master Replication in MariaDB on Ubuntu: A Comprehensive Guide'
seoTitle: How to Set Up Master-Master Replication in MariaDB on Ubuntu
date: 2024-11-07T21:06:26+05:30
description: Set up Master-Master replication in MariaDB or MySQL on Ubuntu with conflict-free auto-increment for high availability and fault tolerance.
slug: master-master-replication-mariadb-ubuntu-setup
categories:
  - Database
tags:
  - mariadb
  - mysql
  - replication
---
Master-Master replication in MariaDB enables **active-active replication**, allowing both servers to handle reads and writes, and synchronize changes with each other. This setup improves **availability** and **redundancy** but requires careful configuration to avoid conflicts.

This guide will walk you through the full setup, covering **error logging** configuration, **network access**, and **conflict prevention** settings.

## Prerequisites

* **Two Ubuntu Servers** with static IPs:
* **Server A IP**: 91.107.235.82
* **Server B IP**: 188.34.176.130
* **Root or sudo access** on both servers.
* **MariaDB installed** on both servers.

## Steps
### Step 1: Install MariaDB (if not already installed)

First, install MariaDB on both servers.

```bash
sudo apt update
sudo apt install mariadb-server -y
```

After installation, ensure MariaDB is running:

```bash
sudo systemctl status mariadb
```

### Step 2: Configure MariaDB for Replication on Both Servers

To set up Master-Master replication, we need to configure each server to:

* **Enable remote access** (set bind-address).

* **Set unique server IDs**.

* **Configure binary logging** for replication.

* **Enable error logging** for troubleshooting.

* **Set auto-increment settings** to avoid primary key conflicts.

#### Configuration File Setup on Each Server

Edit the main MariaDB configuration file (50-server.cnf) on each server to include all necessary settings. Let’s start with Server A.

**On Server A (**91.107.235.82**):**

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

Update the file as follows, adding the lines under \[mysqld\] if they don’t already exist:

```bash
[mysqld]

# Allow connections from any IP address

bind-address = 0.0.0.0

# Unique server ID (set this to 1 for Server A, 2 for Server B)

server-id = 1

# Enable binary logging for replication

log_bin = /var/log/mysql/mysql-bin.log

# Optional: Specify database to replicate (replace 'your_database_name' with your actual database)

# binlog_do_db = your_database_name

# Error log location for troubleshooting

log_error = /var/log/mysql/error.log

# Auto-increment settings to avoid primary key conflicts

auto-increment-increment = 2

auto-increment-offset = 1
```

Save and close the file.

**On Server B (**188.34.176.130**):**

Edit the configuration file on Server B to include similar settings, with slight adjustments to ensure uniqueness:

```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```

Update the file as follows:

```bash
[mysqld]

# Allow connections from any IP address

bind-address = 0.0.0.0

# Unique server ID (set this to 2 for Server B)

server-id = 2

# Enable binary logging for replication

log_bin = /var/log/mysql/mysql-bin.log

# Optional: Specify database to replicate

# binlog_do_db = your_database_name

# Error log location for troubleshooting

log_error = /var/log/mysql/error.log

# Auto-increment settings to avoid primary key conflicts

auto-increment-increment = 2

auto-increment-offset = 2
```

Save and close the file.

**Explanation of Auto-Increment Settings**

In a Master-Master setup, **auto-increment conflicts** can occur if both servers insert rows with the same auto-incremented primary keys. We avoid this by adjusting two settings:

1. `auto-increment-increment = 2`: This setting controls the **step size** for auto-increment values. Setting it to 2 ensures each server only uses every second number, allowing the other server to fill the gaps.

2. `auto-increment-offset`:

	* **Server A** has auto-increment-offset = 1.

	* **Server B** has auto-increment-offset = 2.

This offset sets the **starting point** for auto-increment values, so:

* **Server A** generates IDs like 1, 3, 5, ...

* **Server B** generates IDs like 2, 4, 6, ...

By staggering these values, we ensure each server produces unique auto-incremented primary keys, avoiding conflicts.

### Step 3: Set Up Error Logging and Permissions

To enable error logging for troubleshooting:

1. **Create the log directory** if it doesn’t exist, and set permissions:

	```bash
	sudo mkdir -p /var/log/mysql

	sudo chown mysql:mysql /var/log/mysql
	sudo chown mysql:mysql /var/lib/mysql
	```

2. **Restart MariaDB** to apply the configuration changes on both servers:

	```bash
	sudo systemctl restart mariadb
	```

	If MariaDB fails to start, check the error log at /var/log/mysql/error.log or use sudo journalctl -xeu mariadb.service for detailed logs.

### Step 4: Create Replication Users

Create a user on each server with permissions for replication.

**On Server A**

Log into MariaDB:

```bash
sudo mysql -u root -p
```

Run the following commands:

```sql
CREATE USER 'replicator'@'%' IDENTIFIED BY 'replicator_password';

GRANT REPLICATION SLAVE ON *.* TO 'replicator'@'%';

FLUSH PRIVILEGES;
```

**On Server B**

Repeat the same commands on **Server B**.

**Step 5: Get Master Log File and Position for Initial Setup**

To configure each server as a slave to the other, you need the current binary log file and position from each server.

**On Server A**

In the MariaDB prompt, run:

```sql
SHOW MASTER STATUS;
```

Take note of the File and Position values. These will be used to configure Server B.

**On Server B**

Run the same command on Server B:

```sql
SHOW MASTER STATUS;
```

Take note of the File and Position values for Server A’s configuration.

### Step 6: Configure Each Server as a Slave of the Other

Now that we have the binary log information, configure each server to replicate from the other.

**On Server B**

In the MariaDB prompt on **Server B**, set up replication from Server A:

```sql
CHANGE MASTER TO

  MASTER_HOST='91.107.235.82',

  MASTER_USER='replicator',

  MASTER_PASSWORD='replicator_password',

  MASTER_LOG_FILE='mysql-bin.000001',  -- Replace with Server A's log file

  MASTER_LOG_POS=12345;                 -- Replace with Server A's position

START SLAVE;
```

**On Server A**

In the MariaDB prompt on **Server A**, set up replication from Server B:

```sql
CHANGE MASTER TO

  MASTER_HOST='188.34.176.130',

  MASTER_USER='replicator',

  MASTER_PASSWORD='replicator_password',

  MASTER_LOG_FILE='mysql-bin.000001',  -- Replace with Server B's log file

  MASTER_LOG_POS=12345;                 -- Replace with Server B's position

START SLAVE;
```

### Step 7: Verify Replication Status

To confirm that replication is functioning, check the slave status on both servers.

In the MariaDB prompt on each server, run:

```sql
SHOW SLAVE STATUS\G
```

Look for:

• **Slave_IO_Running: Yes**

• **Slave_SQL_Running: Yes**

Both should be set to Yes, indicating that replication is active and working correctly.

### Step 8: Test Replication

1. **Create a Test Database or Table**: On either server, create a database or table and insert data.

	```sql
	CREATE DATABASE test_db;
	USE test_db;
	CREATE TABLE test_table (id INT PRIMARY KEY, data VARCHAR(100));
	INSERT INTO test_table VALUES (1, 'Sample Data');
	```

2. **Verify on the Other Server**: Check if the database, table, and data appear on the other server, confirming replication.

### Important Considerations

**Split-Brain Risk**: In Master-Master replication, split-brain can lead to data conflicts if the network connection fails. Monitoring and failover mechanisms are recommended in critical systems.

**Data Conflicts**: Avoid simultaneous writes to the same rows on both servers to minimize the risk of conflicts.

With this setup, you now have a fully operational Master-Master replication configuration in MariaDB on Ubuntu. This allows both servers to act as active nodes for high availability and fault tolerance.