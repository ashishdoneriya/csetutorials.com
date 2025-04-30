---
title: How to convert videos in Ubuntu using Vlc
date: 2018-03-04T07:11:21+05:30
description: Convert your very high size smartphone videos using vlc in ubuntu. best free video converter software
slug: convert-videos-ubuntu-using-vlc
categories:
  - Ubuntu
tags:
  - ubuntu
  - vlc
---
In this post I'm going to tell you how to convert videos in Ubuntu using vlc. In my opinion vlc is the best free video converter software. So here are the steps.

1. Install vlc

	```bash
	sudo apt-get install vlc
	```


2. Install other required packages

	```bash
	sudo apt-get install ffmpeg libav-tools gstreamer1.0-libav gstreamer1.0-plugins-bad ubuntu-restricted-extras libde265-dev
	```


3. Open Vlc  

4. Open Menu bar > Media > Convert/Save
	![vlc convert videos](/wp-content/uploads/2018/03/vlc-video-convert.png) 

5. Now in File tab add the file and click on convert/save.
	![vlc convert videos](/wp-content/uploads/2018/03/vlc-video-convert-select-format.png) 

6. Select video format, choose destination file and then click on start. Vlc will start converting your video.

If you want to convert multiple videos files at once then install latest vlc (> 3.0)