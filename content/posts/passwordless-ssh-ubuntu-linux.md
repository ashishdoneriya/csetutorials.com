---
title: Passwordless SSH on Ubuntu Linux
date: 2020-05-15T09:34:10+05:30
description: Steps to setup passwordless ssh on your ubuntu linux machine.
slug: passwordless-ssh-ubuntu-linux
categories:
  - Ubuntu
tags:
  - linux
  - ssh
  - ubuntu
---
These steps are verified and tested several times and works flawlessly.

1. Install openssh server 
	```bash
	sudo apt-get install openssh-server
	```

2. Remove previous .ssh directory (from home directory) 
	```bash
	rm -r ~/.ssh/
	```


3. Now Execute the below command. Leave all the field empty when asked. 
	```bash
	ssh-keygen -t rsa
	```


4. Execute the following commands after that 
	```bash
	cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
	sudo chmod go-w ~/
	chmod 700 ~/.ssh
	chmod 600 ~/.ssh/authorized_keys
	exec ssh-agent bash
	ssh-add
	```


That's it.

You can test on your machine.

```bash
ssh-copy-id localhost
ssh localhost
```

When you ssh localhost, it will not ask you password again.