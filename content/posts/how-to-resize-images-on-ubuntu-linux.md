---
title: How to resize images on Ubuntu Linux
date: 2018-10-29T09:23:01+05:30
description: Resize image on Ubuntu using Kolour paint app. free image resizer software
slug: how-to-resize-images-on-ubuntu-linux
categories:
  - Ubuntu
---
Most of the times resizing images on Ubuntu is very difficult. I think some users use Gimp which is a very famous image editor on linux and its like photoshop of Ubuntu. But most of the times I found Gimp very hard to learn. Some people who don't know Gimp like me use online image editors to resize their photos. So here I am going to tell you the alternative of Gimp app to resize your apps.

## Steps

1. **Install Kolour Paint App :** Kolor Paint app is paint program for KDE (a deskoop environment). It is just like MSPaint in Windows. You can do painting, change images and edit icons using it. So install it using below command.
	```bash
	sudo apt-get install kolourpaint4
	```

2. Now open your image using Kolour Paint.  
	![kolour paint app](/wp-content/uploads/2018/10/kolour-paint.png)

3. Let's say my image resolution is 1280 X 720. I want to reduce the image size to half ie. I want to convert the image to 640 X 360 ( You can also increase the image size). Open Image Menu and click on Resize/Scale. Alternatively you can use keyboard shortcut `Ctrl + E`to open resize dialog.
	![kolour paint resize menu](/wp-content/uploads/2018/10/kolour-paint-resize-menu.png)

4. In the resize dialog, you will see three options - Resize, Scale and Smooth Scale. Select **Smooth Scale**. Then change the new width to 640 and make sure that Keep aspect ratio is checked. You can verify these options from the image below.
	![kolour paint resize dialog](/wp-content/uploads/2018/10/kolour-paint-resize-dialog.png)

	Click on `Ok` button in the resize dialog

5. Now save image using  `File Menu > Save` or by using keyboard shortcut `Ctrl + S`
	![kolour paint file save](/wp-content/uploads/2018/10/kolour-paint-file-save.png)
	Click on Save again in the next dialog. If some override message appear, click Yes or Ok.

6. That's it. Your image size has been changed.