---
title: Linux Programming File operation
date: 2018-05-21 21:40:09
categories:
- Linux Programming
tags:
- system call
---

# Linux Programming File Operation 

File this concept in the linux system is very common , and then we can use the file to describe everything in the linux system .

The processes will record in a directory and also some user and anything can be save or via file working . such as the socket , socket is a file describtor in linux in nature . So we can use the common operation in abstract to operate the socket , we can send some bytes and read some bytes to exchange the informations.

And the process is also described in file , we record some base information for the process and we can also set some page and segment in use the file interface 

<!--more-->

# Linux Design Philosophy

Every thing in the unix are files or process .

![UNIX SYS](http://ww1.sinaimg.cn/mw690/0079qc5ply1frkz09q41xj31ww1cr0xj.jpg)

In this picture we can see that the system directory and file will be origanized in a tree ,we call this tree as file tree , and we can mount any other file system on this tree , such we can use usb and the system may be mount this device on the system tree , /dev/xxx , so in this way we can create many mount point to mount many device not only a single usb.

# I/O system 

The I/O system will be devided into two types ,one we call is the block device and another we also call them as character device . But what is the difference between the two device to record ?

Block Device , the all data will be devided into some block to save ,so the block device we can infer like CD-ROM ,HARD-DISK , the data is saved in block methods , and we need some time to find the block first , after that we can access data randomly in this block .

Character Device , maybe ,I think the stream device will be more suitable , just like the water stream from the pipe , the character device read data from one to one , they don't know if there no characters next time ,but in some protocal we can use EOF to present the end of file ,and the character is none ,the read function can return 0 , but we can't access data randomly but in order .

## Base I/O

Linux/Unix have the same abstraction in file system , so they have already provide some effective and common functions for us to do the base file I/O . We can use these file operation functions to operate any file descriptor , well , the all files are represent by the file descriptor .

We can use the function to get a file descriptor and read or write ,after the all operation achive to the ending , we just need the close to close the file ,and release the all resorces.

* open file 

```c
#include <sys/stat.h>
#include <fcntl.h>

int fd = open("path",int flags ,... )
```

this system call can open a new file descriptor and use the flags to define the open mode , write only read only and wronly ,and so on .

* read file 

```c
#include <unistd.h>

char buff[1024];

int read(fd,buff,1024)
```

this system call can read data into buffer and then return the read counts . If don't read enough this function can reaturn the count in actual.

* write file

```c
#include <unistd.h>

int write(fd,buffer,size)
``` 

It's same as the read but this function is write behavior , so we can use the same describtion to describe the write system call .

* close file  

```c
#include <unistd.h>

close(fd)
```

close a file descriptor .

## fcntl

This system call can set some attributes fo an open file , and we can change some attribute in a file which open before .

