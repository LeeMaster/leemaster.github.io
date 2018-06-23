---
title: 'Linux Programming Autotools '
date: 2018-05-22 23:26:00
categories:
- Linux Programming
tags:
- system call
---

# Autotools

We know the GNU-- "GNU not unix" foundation is support the open source software and the autotools is the tool in GNU to create Makefile automatically . The tool package have file children tools , aclocal,autoscan,autoheader,autoconf,automake, they consits the autotools ,and we can use them to contribute a open source code software in C/CPP .

Don't need write makefile any more , until you want to do more effective thing to do .

<!--more-->

# Project Management 

I have used the Maven to manage the Java project and the project directory tree like this 

```text
project
    |---src
    |   |---main
    |   |   |---java
    |   |   |---resources
    |   |---test
    |---pom.xml
```

Also we can belive the C/C++ project also have a directory tree in common.

```text
project
    |---lib
    |---include
    |---src
```

the lib will contain the static library or dynamic library and the include contains the header file , src as you can see contains the source files .


