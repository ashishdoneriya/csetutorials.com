---
title: How to write C or C++ programs on Ubuntu Linux
date: 2018-10-01T07:05:41+05:30
slug: compile-c-programs-on-ubuntu-linux
categories:
  - Ubuntu
tags:
  - programming
  - ubuntu
---
If you are a newbie in the linux world you are the facing the question - how to compile c program in linux or how to compile c++ program in linux. So in this post I'm going to explain the steps using which you can run your c c++ programs on linux.

There are two ways to write and run C and C++ programs on Ubuntu.  
1. Using command line  
2. Using IDE (app)

## Using command line

To run a C or C++ program on linux, there are three phases write code, compile code and run it. First you have to write your code using a text editor eg. gedit (it is just like Notepad++). Then after writing the code you have to manually compile the code using particular commands, the compiler creates an executable file ( just like .exe in windows) for our code and then we run that executable file

For compile C programs you need `gcc` c compiler linux tool which is installed by default on Ubuntu. For running C++ programs you need `g++` tool which is not installed by default on Ubuntu. You have to install it by running command

```bash
sudo apt-get install g++

```

### Running a C program

**NOTE :** `#include<conio.h>` will not work on Ubuntu.

Create a text file named `test.c`
![Writing C program](/wp-content/uploads/2018/10/writing-c-program-on-ubuntu-linux.png)

Now open a terminal and execute the below command

```bash
gcc test.c -o test

```

Here gcc is the tool using which you will compile your C program. `test.c` is the file name of your code (‘.c' is the extention of a C program). `-o` argument is passed to tell the compiler that while making the executable file of my program `test.c`, name it as `test`. You can also use another name other that `test` like `myExecutedFile` or something. Now run your code by executing the command below.

```bash
./test

```

In linux `./` is used to run an executable file. In our case executable file is `test`
![Running C program](/wp-content/uploads/2018/10/running-c-program-on-ubuntu-linux.png)

### Running a C++ program

Create a text file named `test.cc (you can also use test.cpp)`
![Writing C++ program](/wp-content/uploads/2018/10/writing-c-plus-plus-program-on-ubuntu-linux.png)

Now open a terminal and execute the below command

```bash
g++ test.cc -o test
```


Here g++ is the tool using which you will compile your C++ program. `test.cc` is the file name of your code (‘.cc' and .cpp are the extentions of a C++ program). `-o` argument is passed to tell the compiler that while making the executable file of my program `test.cc`, name it as `test`. Now run your code by executing the command below.

```bash
./test
```
![Running C++ program](/wp-content/uploads/2018/10/running-c-plus-plus-program-on-ubuntu-linux.png)

## Using IDE

Using IDE, running a C or C++ program is very easy and productive. Here we are going to use Geany application. So first install geany by using the command below.

```bash
sudo apt-get install geany
```


Now create a new file test.c in geany.  
Compile using `Build Menu > Compile`  
Build using `Build Menu > Build`  
Execute using `Build Menu > Execute`  
![Geany menu bar](/wp-content/uploads/2018/10/geany-menu-bar.png)

You can also compile, build and execute directly using buttons. 
![Geany IDE C programming](/wp-content/uploads/2018/10/geany-ide-c-programming.png)

You can do the same for C++ programs also.