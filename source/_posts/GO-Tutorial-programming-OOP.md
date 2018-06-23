---
title: GO Tutorial programming OOP
date: 2018-06-01 10:22:01
categories:
- Tutorial
tags:
- Go
---

# Duck Type 

The one which can take the "GAGA" like a duck then we can think this as a duck . And the duck type thinking is same as the wrod. and the oop design in programming is more important than some thing like the style of the code . We also belive that the duck type thinking is a method to help us to know the all system memory state .

<!--more-->

# Type System 

In many program language we should do this thing, we want to define a class belong to a interface and then we should implement the all methods from the interface ,and then we belive that this class is a interface implementation.

And the all language do this thing, they think the all variable as two types , first is the base variable like int float double long , and the another is the reference type ,like in java we think the array and the class instance as the reference .

We can do this thinking we think the register can save the local variables and we get the eax ax and other registers can do this thing right . but the reference type can do this thing , they use the address pointer to ponint to a memeory area which contain the all data or methods code in binary .

# Interface 

In many language we should do this thing , we should explicit implement a class which implement a interface like java and then should implement the all methods abstract. But in Go
we don't need do this .

the class can be seen as a interface instance when it overide the all method defined in the interface and then we can belive the struct like class implement the all interface characters.

we don't need know the all tree of inheriment of a class or a interface but we should know the class implement a interface all method and then we can use this class to be the interface instance .

```Go

type File struct {

}

func (f *File) Read(buf []byte) (n int, err error)
func (f *File) Write(buf []byte) (n int, err error)
func (f *File) Seek(off int64, whence int) (pos int64, err error) 
func (f *File) Close() error

```

## interface assign 

In java or other oop language we think that the all thing like this , the this pointer point to where and we should know the pointer point to the class instance and then call the method to do the thing. like js api call() apply() 

This character carry a very convince method to do the next thing , we can see the all stack call link chain.

In Go the variables will be delivered to the function by reference or value and then if we delivery a value to a function then we can't get the real memeory address and then if we use some pointer to point the self we can't do so . like this

```Go
type Integer int

func (a Integer) Less (b Integer) bool{
    return a < b
}

func (a * Integer) Add (b Integer){
    *a += b
}

type OtherInteger interface{
    Less(b Integer) bool
    Add(b Integer)
}

//wrong 
var a Integer = 1
var b OtherInteger = a

```

Why do this let the wrong raise , in my thinking , we just delivery an address to an interface but the Go system can do some thing background to check the wether the OtherInteger should implement the all methods of the Interger type extend methods , but in the Add method we delivery a pointer to the function to define the address to check the path to call the next operation and then we just send an instance address but can't excepilct the methods implementation or not .

and the pointer to do as the self object like this we can belive that this method may like the static method , which don't have the instance value or like a readonly attribute but the pointer will do the other thing we can belive that this method can change the instance attribute in the instance .

## interface query 

like the instanceof keyword like in java we can check the type belong to which interface and then we can do the next thing then we can do so as this 

```Go
type Writer interface{
    ReadByte(pos int)byte
    Read(len int)[]byte
}

type Instream interface{
    Read(len int)[]byte
}

func main(){

    var file Writer = ...
    if file2,ok := file.(Instream);ok{
        ...
    }
}

```

we can do like this , and this can do the mulitstate and then we can use this thing to do the same like in the java interface .

## type query 

like the interface query ,but we should use the switch to choose the type we query like a result.


## interface componsite 

we can make some interface to combine as a new interface and then we can do the next thing , we like copy the methods redefine in the new interface 



