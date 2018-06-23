---
title: C Note extern keyword
date: 2018-05-21 22:00:40
categories:
- C
tags:
- program note
---

# extern keyword

I was very puzzle in this keyword in many C language programming . I write this note to record this keyword usage.

<!--more-->

## variable scope

In C programming , the variables have theri scope , function scope and global scope in common , many programming language have this concept to devide the variables to many type to do more effective thing in programming.

As we know that the program have both stack and heap area , but there are some areas we don't know before , their are text area and data area , stack and heap we can understand easily , in the stack area where will save the variables and some tempory data in a structure called **stack frame** and the heap area we touch use the **malloc()** and **free()** functions to attach those varibale .

But we should know the text and the data area , the text area saves the program code , if you had learn the assembly in before learning and you can write the code in the segment called **test** , this segment will save the code and the instructions , then this area can let memory model in the segment-page method to manage the memeoy in practie.

And the data area will save the global varibales , these varibales may use in anywhere , for example the Unix kernel set the **u** to be a global varibale to define some information to record the user and the group information.

For this history design reason ,we can brefily understand the scope of varibales . In my opinion , the programming compiler like gcc will compile the all program files and then linke them to single execute file ,in the execute file , the all global variable will set in the data area and the all instructions will save in the text and divided into some segments and then the stack and the heap will be the support memeory model in runtime .

### Local variables

The local varibales will be define in the function inner scope , and these variables are devided into two types static and auto.

#### auto local varibales

```C
#include<stdio.h>

int main()
{
    int a = 10;
    int b;
    b = a;
    printf("%d\n",a);
    return 0;
}
```

in this program we can belive the a and b are alloc in the stack and the main function will get a stack frame and the all variables both a and b will save into the frame , when the main function return then the frame of this program will release autimaic,so we call the a and b as auto variables;

#### static local variables

```C
#include<stdio.h>

int add_ten(int);

int main()
{
    int a = 1;
    int b ;
    b = add_tem(a);
    printf("%d\n",b);
    return 0;
}

int add_ten(int i)
{
    static int item = 1000;
    return item + i;
}
```

in this program the item is a static variables which will be initialized when the compile period and assignd to the integer 1000 . And everytime the function call the item can't change.

### global variables 

Now we get the next step to know what is the gloabl variables , and what is the static global variables .

I have mentioned before , the global varibales will set into the data area , and these variables will be initialized at the time the program running start point . Then the varibale will be setted.

File global_variable.h

```c
#ifndef __GLOBAL_VARIABLE_H__
#define __GLOBAL_VARIABLE_H__

int equal_tem(int);

#endif
```

File global_varibale.c

```c
#include "global_variable.h"

static int _TEN_VAR_ = 10;
int _OTHER_VAR_ = 100;

int equal_tem(int i)
{
    return i > _TEN_VAR_ ? 1 : 0;
}
```

File global_call.c

```c
#include <stdio.h>

extern int equal_tem(int);

void global_call()
{
    extern int _OTHER_VAR_;

    printf("%d\n",_OTHER_VAR_);
    
    printf("%d\n",equal_tem(_OTHER_VAR_));
}

int main(){
    global_call();
    return 0;
}

```

File makefile

```makefile
test : global_variable.o global_call.o
        gcc -o test global_variable.o global_call.o

global_variable.o : global_variable.c global_variable.h
        gcc -c global_variable.c global_variable.h

global_call.o : global_call.c
        gcc -c  global_call.c
```

After this demo we can infer that , we don't need some declare of variables or functions , we could use the extern keyword to call the function defined in the other file . We can consider that the process of gcc doing is that , when the gcc call then will collect the code form the all code and then integrated them to one file ,and then split the code to the three segment data text and bss , then we can get as the below file .

```c
#include <stdio.h>

static int _TEN_VAR_ = 10;
int _OTHER_VAR_ = 100;

int equal_tem(int i)
{
    return i > _TEN_VAR_ ? 1 : 0;
}

void global_call()
{
    extern int _OTHER_VAR_;

    printf("%d\n",_OTHER_VAR_);
    
    printf("%d\n",equal_tem(_OTHER_VAR_));
}

int main(){
    global_call();
    return 0;
}
```

But this code is not the which gcc work to generate , but we can consider as this file to wrpper the extern keyword.

## how to use 

we can use this keyword to do many thing , some time we don't need the header file ,and use the function local scope static varibales we save some memory in use , and fast the speed of the program .

we can use the extern in CPP to write some code just use in C like we can wrap the system call to use in CPP before the C and use the Cpp class grammer to wrap the system call.
