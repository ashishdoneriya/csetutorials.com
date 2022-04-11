---
title: How to Auto Mount NTFS Partitions at startup on Ubuntu Linux
created: 2016-08-11T01:49:27+05:30
author: ashishdoneriya
description: Steps to Auto Mount windows NTFS Partitions at startup on Ubuntu Linux
permalink: /auto-mount-ntfs-partitions-startup-ubuntu-linux.html
updated: 2022-04-10T01:21:00+05:30
categories:
  - ubuntu
tags:
  - linux
  - Ubuntu
---

**NOTE :** Doesn't work on Ubuntu 20.10 and above version. You can follow better approach for those versions [https://linuxconfig.org/how-to-mount-partition-with-ntfs-file-system-and-read-write-access](https://linuxconfig.org/how-to-mount-partition-with-ntfs-file-system-and-read-write-access)

Auto Mount NTFS Partitions at startup on Ubuntu Linux.

Lets suppose You have 2 NTFS partition in windows C Drive and D Drive.

If you want Ubuntu to auto mount these drives when you start Ubuntu then do the following.

First create the folders `c_drive`, `d_drive` in your root.

```bash
sudo mkdir /c_drive
sudo mkdir /d_drive
```


Open application called `Disks` (drive partition software).  
[<img loading="lazy" src="/wp-content/uploads/2016/08/opening-disks-app-ubuntu.png" alt="Opening Disks App in Ubuntu" width="820" height="461" class="aligncenter size-full wp-image-454" srcset="/wp-content/uploads/2016/08/opening-disks-app-ubuntu.png 820w, /wp-content/uploads/2016/08/opening-disks-app-ubuntu-500x281.png 500w, /wp-content/uploads/2016/08/opening-disks-app-ubuntu-400x225.png 400w" sizes="(max-width: 820px) 100vw, 820px" />](/wp-content/uploads/2016/08/opening-disks-app-ubuntu.png)

Select your hard disk.  
[<img loading="lazy" src="/wp-content/uploads/2016/08/selecting-hard-disk-from-disks-app-ubuntu.png" alt="Selecting hard disk from disks app in Ubuntu" width="800" height="722" class="aligncenter size-full wp-image-457" srcset="/wp-content/uploads/2016/08/selecting-hard-disk-from-disks-app-ubuntu.png 800w, /wp-content/uploads/2016/08/selecting-hard-disk-from-disks-app-ubuntu-500x451.png 500w, /wp-content/uploads/2016/08/selecting-hard-disk-from-disks-app-ubuntu-400x361.png 400w" sizes="(max-width: 800px) 100vw, 800px" />](/wp-content/uploads/2016/08/selecting-hard-disk-from-disks-app-ubuntu.png)

Select your ntfs partitions (Volumes) and one by one and note down the device name of all. Lets suppose your C Drive partition is 96 GB. Then select the label that is labeled as (File System Partition … 96 GB NTFS). When you click on it, it will display some information related to your partition (volume). Note down the device name in front of label ‘Device' (like `/dev/sda2`). This is path of your C Drive that Ubuntu has specified. Note down the path.  
[<img loading="lazy" src="/wp-content/uploads/2016/08/selecting-c-drive-disks-app-ubuntu.png" alt="Selecting C Drive in Disks App Ubuntu" width="800" height="722" class="aligncenter size-full wp-image-459" srcset="/wp-content/uploads/2016/08/selecting-c-drive-disks-app-ubuntu.png 800w, /wp-content/uploads/2016/08/selecting-c-drive-disks-app-ubuntu-500x451.png 500w, /wp-content/uploads/2016/08/selecting-c-drive-disks-app-ubuntu-400x361.png 400w" sizes="(max-width: 800px) 100vw, 800px" />](/wp-content/uploads/2016/08/selecting-c-drive-disks-app-ubuntu.png)

Similarly find the path for the other drive (D Drive).  
[<img loading="lazy" src="/wp-content/uploads/2016/08/selecting-d-drive-disks-app-ubuntu.png" alt="Selecting D Drive Disks App on Ubuntu" width="800" height="722" class="aligncenter size-full wp-image-460" srcset="/wp-content/uploads/2016/08/selecting-d-drive-disks-app-ubuntu.png 800w, /wp-content/uploads/2016/08/selecting-d-drive-disks-app-ubuntu-500x451.png 500w, /wp-content/uploads/2016/08/selecting-d-drive-disks-app-ubuntu-400x361.png 400w" sizes="(max-width: 800px) 100vw, 800px" />](/wp-content/uploads/2016/08/selecting-d-drive-disks-app-ubuntu.png)

Create a file `/etc/rc.local` and make it executable using below commands

```bash
sudo touch /etc/rc.local
sudo chmod +x /etc/rc.local

```

Now open the file `/etc/rc.local` in a text editor with root permission. My favourite is gedit.

```bash
sudo gedit /etc/rc.local
```

In the file `/etc/rc.local` paste the following lines before ‘exit 0'

```bash
sudo mount /dev/sda2 /c_drive
sudo mount /dev/sda3 /d_drive
```

Basically commands written inside `/etc/rc.local` file will be executed at startup. We can execute sudo commands from here at startup.

NOTE : `/dev/sda2` and `/dev/sda3` are the paths of c drive and d drive in my system. You have to put the paths (that you noted / found in previous steps through `disks` app ) of c drive and d drive of your own system

So our final `/etc/rc.local` will look something like this

```bash
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.

sudo mount /dev/sda2 /c_drive
sudo mount /dev/sda3 /d_drive

exit 0

```


[<img loading="lazy" src="/wp-content/uploads/2016/08/ubuntu-auto-mount-ntfs-partitions.png" alt="Ubuntu auto mount windows ntfs partitions" width="1366" height="768" class="aligncenter size-full wp-image-461" srcset="/wp-content/uploads/2016/08/ubuntu-auto-mount-ntfs-partitions.png 1366w, /wp-content/uploads/2016/08/ubuntu-auto-mount-ntfs-partitions-500x281.png 500w, /wp-content/uploads/2016/08/ubuntu-auto-mount-ntfs-partitions-1024x576.png 1024w, /wp-content/uploads/2016/08/ubuntu-auto-mount-ntfs-partitions-982x552.png 982w, /wp-content/uploads/2016/08/ubuntu-auto-mount-ntfs-partitions-400x225.png 400w" sizes="(max-width: 1366px) 100vw, 1366px" />](/wp-content/uploads/2016/08/ubuntu-auto-mount-ntfs-partitions.png)

Save and exit and restart that's it.

Now you will find your c drive contents inside /c\_drive directory and d drive contents inside /d\_drive directory. You can bookmark these locations in your nautilus (ctrl + D) for faster access. Bookmarks are a great way in Nautilus to directly go to any of your favourite location.

[<img loading="lazy" src="/wp-content/uploads/2016/08/ubuntu-nautilus-bookmarks.png" alt="Ubuntu Nautilus Bookmarks" width="201" height="693" class="aligncenter size-full wp-image-462" srcset="/wp-content/uploads/2016/08/ubuntu-nautilus-bookmarks.png 201w, /wp-content/uploads/2016/08/ubuntu-nautilus-bookmarks-145x500.png 145w, /wp-content/uploads/2016/08/ubuntu-nautilus-bookmarks-116x400.png 116w" sizes="(max-width: 201px) 100vw, 201px" />](/wp-content/uploads/2016/08/ubuntu-nautilus-bookmarks.png)
