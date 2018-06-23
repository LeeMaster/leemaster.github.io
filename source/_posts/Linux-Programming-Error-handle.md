---
title: Linux Programming Error handle
date: 2018-05-21 21:50:30
categories:
- Linux Programming
tags:
- system call
---

# C Error handler

Not same as Java like , C don't have the try-catch block to catch the exception in runtime ,so sometime we should keep the promise that we will input a pointer to point the error variables in use .

<!--more-->

For example 

```c
int get_err(int param,int * err)
{
    if(param < 0){
        err = 1;
        return 0;
    }else{
        err = 0;
        return 1;
    }
}
```

This function we define to charge if the param is gratter than the zero , if it's letter than the zero we throw a exception in the way assign a 0 to err variable pointer.

In call 

```c
int main()
{
    int err = 0;
    get_err(-1,&err);
    if(err){
        printf("less than zero");
    }else{
        printf("gratter than zero");
    }
}
```

Use this promise we can define the function to get an exception .

# Linux Error

But somethime we need know the reason of the exception why the error raise intime ,so we need a global variables to save the error reason or code to get more details .

As we know the global variables can save in the global scope ,so the errno variables can be use in anywhere of the program and we can use the extern keyword to extern and use the variable and then the system call can set the errno to identify the details of an exception.

But now I don't find the location where decalre the errno variable and I can do a fearless assumption that when the system create a process then will declare a int errno in the data segment and then we can use the segment by extern keyword to get the reference of this variable to use in our business code .

And we can use the system call **strerror()** to get more detail information of the error.

For example 

```c
#include <stdio.h>
#include <errno.h>
#include <string.h>

int main()
{
    extern int errno;

    int fd ;

    if((fd = open("path",O_WRONLY)) == -1){
        printf("%d -- %s \n",errno,strrror(errno));
    }else{
        printf("open successfully\n");
        close(fd);
    }

}
```

The errno will be seted in a int type to define the error type , and then use the strerror can get some details in a brefy way.
 
# In Practice

In system programming we can use this method to get the details of an error , and we can decide which we next to do by the errno type , and then we don't need keep a promise that we decalre a function in the way we define before .