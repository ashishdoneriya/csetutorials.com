---
title: Ubuntu Apps
date: 2017-06-15T09:39:46+05:30
slug: ubuntu-apps
categories:
  - Ubuntu
---

### Chromium
One of the best open source web browser in the world.

```bash
sudo apt-get install chromium-browser
```


## Unity Tweak Tool
Unity Tweak Tool is a settings manager for the Unity desktop. It provides users with a fast, simple and easy-to-use interface with which to access many useful and little known features and settings of the desktop environment that one may want to configure.

```bash
sudo apt-get install unity-tweak-tool
```


## GDebi
Gdebi is a simple tool to install deb files, simple and much faster than ubuntu software center. It lets you install local deb packages resolving and installing  
its dependencies.

```bash
sudo apt-get install gdebi
```


## Galculator
When you press super key and start typing calculator, the first result that unity shows is 'Libreoffice excel' which is definitely not a calculator. The calculator is in the second place. So you have to press down key and then right key and enter to open the calculator. This process is very long. Instead of that install Galculator and instead of typing 'calculator' start typing gal.. and you will see Galculator in the first place which you can open it by just pressing enter. Galculator is similar to the calculator.

```bash
sudo apt-get install galculator
```


## KolourPaint
On Ubuntu if you miss a paint app like ms paint in windows then install KolourPaint. KolourPaint is a simple painting program. It's like a copy of ms paint.

```bash
sudo apt-get install kolourpaint4
```


## VLC
The simple and best video/audio player in the world. It can also be used to convert video files.

```bash
sudo apt-get install vlc
```


## QBittorrent
Its a torrent downloader. It has integrated torrent search engine. It is very beautiful, simple and has a lot of functionality compared to other torrent downloaders.

```bash
sudo apt-get install qbittorrent
```


## [Ubuntu Cleaner](https://github.com/gerardpuig/ubuntu-cleaner)
Ubuntu Cleaner is a tool that makes it easy to clean your ubuntu system. Ubuntu Cleaner lets you remove App caches (including most major browsers), Apt cache, Thumbnail cache, Old kernels etc. To install it you have to add ubuntu-cleaner ppa using the command below

```bash
sudo add-apt-repository ppa:gerardpuig/ppa
```


Now run the command below to install ubuntu cleaner

```bash
sudo apt update && sudo apt install ubuntu-cleaner
```


## [UNetbootin](http://unetbootin.github.io/)
UNetbootin allows you to create bootable Live USB drives for Ubuntu and other Linux distributions without burning a CD.

```bash
sudo apt-get install unetbootin
```


## [Etcher](https://github.com/resin-io/etcher)
Burn images (linux iso) to SD cards & USB drives, safely and easily.

## [WoeUSB](https://github.com/slacka/WoeUSB)
WoeUSB lets you create bootable USB of most modern Windows releases, including: Windows Vista, Windows 7, Window 8, Windows 10.  
For Ubuntu 16.04

```bash
wget http://ppa.launchpad.net/nilarimogard/webupd8/ubuntu/pool/main/w/woeusb/woeusb_2.1.2-1~webupd8~xenial_amd64.deb
sudo dpkg -i woeusb_2.1.2-1~webupd8~xenial_amd64.deb
```


## [Xtreme Download Manager](http://xdman.sourceforge.net/)
Xtream download manager is one of the best and powerful download managers. It can download videos, resume broken/dead downloads and can also integrates with web browsers like chrome and firefox to save streaming videos from net.

## [Tixati](https://www.tixati.com/)
Tixati is a New and Powerful P2P System and it's a faster torrent downloader than the rest acc. to its website.

## [Visual Studio Code](https://code.visualstudio.com)
Visual Studio Code is a source code editor developed by Microsoft for Windows, Linux and macOS. It includes support for debugging, embedded Git control, syntax highlighting, intelligent code completion, snippets, and code refactoring.

## [Eclipse](http://www.eclipse.org/downloads/eclipse-packages/)
Eclipse is an integrated development environment (IDE) used in computer programming, and is the most widely used Java IDE.

## [Sublime](https://www.sublimetext.com/docs/3/linux_repositories.html#apt)
A sophisticated text editor for code, markup and prose. It is lightweight and very fast. You need a license to use it.

## [Insomnia](https://insomnia.rest)
Insomnia is a beautiful cross-platform application for organizing, running, and debugging HTTP requests. It not completely free.

## **Honourable Mentions**

## [google-drive-ocamlfuse](https://github.com/astrada/google-drive-ocamlfuse)
It lets you mount your Google Drive on Linux.

```bash
sudo add-apt-repository ppa:alessandro-strada/ppa
sudo apt-get update
sudo apt-get install google-drive-ocamlfuse
```

## [Goofys](https://github.com/kahing/goofys)
Goofys allows you to mount an S3 bucket as a file system.

## [Sudocabulary](https://github.com/badarsh2/Sudocabulary)
It's a script that when executed makes every terminal window that you launch thereafter greet you with a new English word along with its meaning.

```bash
curl https://raw.githubusercontent.com/badarsh2/Sudocabulary/master/script.sh | bash
```