---
title: My Ubuntu Notes
created: 2017-05-03T00:56:57+05:30
updated: 2022-04-10T18:53:00+05:30
author: ashishdoneriya
permalink: /my-ubuntu-notes
---
1. During Installation, always create swap space and a separate partition for the Home directory. Always select that partition for other Linux distributions also but **do not format it**. And create separate users for different-different distributions like /home/ubuntu for Ubuntu, /home/arch for arch Linux and so on.

2. If your system has SSD then donot create swap space from SSD.

3. Use [Ubuntu Source List generator](https://repogen.simplylinux.ch/).

4. **Run sudo command without password**  
If you don't want to type sudo password again and again then run command `sudo visudo` and append the below line at the end.

```bash
your_username ALL=(ALL) NOPASSWD: ALL
```

Save using `Ctrl + X` and `Shift + y` and press Enter. That’s it.

5. **Create user from Command line**

```bash
useradd --create-home --system --shell /bin/bash --user-group your_username
```

6. XUbuntu is the best Linux distribution (in terms of performance). It has the power to give a new fresh life to slow and dying old pcs.

7. To Generate subtitles of audio and video  
Install

```bash
sudo apt-get install ffmpeg && sudo pip install autosub
```

To generate subtitle :

```bash
autosub -o subtitlefile.srt "/path/to/file.mp4 or .mp3"
```

If you are using Vlc to play the .mp3 file, then you have to select a visualization (Menu > Audio > Visualization > Select any) because Vlc cannot display subtitles without it.

8. Detach a Linux Processes From Controlling Terminal

```bash
command </dev/null &>/dev/null &
```

9. For setting the paths, the file is `/etc/environment`

10. [Reset Mysql Password](https://devanswers.co/how-to-reset-mysql-root-password-ubuntu/)

11. [Auto mount ntfs partitions](https://linuxconfig.org/how-to-mount-partition-with-ntfs-file-system-and-read-write-access)
