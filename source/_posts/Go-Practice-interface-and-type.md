---
title: Go-Practice-interface and type
date: 2018-06-14 17:09:48
categories:
- Practice
tags:
- Go
---

# Go Interface 

In many language to communicate to the computer , we have the interface structure to construct the rule between the people and the computer data structure . What is the interface ? In some time I have do this thinking that the interface is the rule for the all class which implement this interface and then will act as a same role 

<!--more-->

For a sample example in real world . we could know that the student is a role in school not only the perimary school middle schoold and the high school even in university . The student must do their duty to do in school , so we can call them be student role . right ?

And the the all structure programming language had , we should know that the design method we could let the same behaviors to an interface and let the class to implement the interface and we also need some variables to define the base attribute of this class so the easy method let we know how to implement a real world into computer system .

## In Go 

In Go we get this reality like java the interface{} is the Any type like Java Object type. But what is the type or what is the type information ? I think that the interface is a type absolution in action .

The type in programming language just do this work to allocate the memory space and originaze the all pointer . But anything exculd the flow the system don't do like this .

Define an interface in go is really easy 

```Go
type SomeInterface interface{
    SomeMethod()
}
```

We can use this grammer to create an interface and then we just let the struct called class to implement the all methods .

We don't need to do the implementation explicitly like in java use the implement key word , the compiler consider that a class override all method in an interface then it can belive the class is an instance for this interface .

Like in Go standard library the File implement the io.Reader and io.Writer then we can use the file to write and read , and the io.Reader and io.Writer just have method like the interface Read and Write , but don't have any implemention and File will implement the all method to read and write , but how to write the File type don't need to know .

And interface in Go can inherite from other interfaces like 

```Go
type Reader interface{
    Read()([]byte,error)
}

type Writer interface{
    write(b []byte)(int,error)
}

type ReadWriter interface{
    Reader
    Writer
}
```

We can know that the ReadWriter must have two methods and then we have aggregating the two interface we defined before I call this as MixIn character in go .

and the all examples display the method to define a interface and inherite to contruct a new interface .

## polymorphism

The interface instance will be initlize to be nil and nil , and the OOP model in Go most like the OOP in Objective-C , called communication model , the class called as reciver and the methods called selector and the call process called message delivery .

```Go
func main(){
    var buf *bytes.Buffer
    buf.read()
}
```

When define the buf variable then the buf will be nil and nil also be the interface as Reader and then when call , the buf will be nil so the message can be failure .

How polymorphism do in Go I think like java we just assgin a class which implement the interface we mention before , then we can create many class like the class , and then we just use the interface type pointer to point the class then we can use the method in polymorphism

### Interface assignment 

The memory model like many language , the class construct with pointer and constraint pool like java , we can define the const not only const but also the literal value and these variables can be saved in the meemory area in the .css segment and the method can be saved in the memory segment , and different type struct implement the only interface define in the all program will be in many memory segment .

And when the method call then the runtime can find the real address of the method which call in the process . 

T and T* is the different type in Golang like this 

```Go

type SomeInterface interface{
    Call() (string,error)
}

type SomeStruct struct{
    Name string
    Age int32
}

func (self * SomeStruct)Call(string,error){
    return self.Name ,nil
}

func main(){
    var s SomeStruct = SomeStruct{"leemaster",22}
    var i SomeInterface

    i = s // wrong
    i = &s 
}

```

The wrong sign do what let this statement to be wrong , T and T * is two different type , when we use the s to assign to i then we assign the type T to a interface but the T don't implement the method in the interface but the T* implement the methods ,but why ,because the Go hava carried the truth of the OOP to us which save the self or this pointer to the struct then we can get or set the number but we also can let self as the T type but as we know the Go call a function/method just copy the arguments to the list of argument we modify the arguments struct don't do any effect .


# Some example 

# standard-library flag

the flag package which use to handle the commind line arguments 

```Go
package tempconv

import(
	"fmt"
	"flag"
)

type Celsius float64
type Fahrenheit float64

type celsiusFlag struct{Celsius}

const(
	AbsoluteZeroC Celsius = -273.15
	FreezingC Celsius = 0
	BoolingC Celsius = 0
)

func CToF(c Celsius) Fahrenheit{
	return Fahrenheit(c * 9/5 + 32)
}

func FToC(f Fahrenheit) Celsius{
	return Celsius((f - 32)*5/9)
}

func (f *celsiusFlag) Set(s string) error{
	var unit string
	var value float64
	fmt.Sscanf(s,"%f%s",&value,&unit)
	switch unit{
	case "C":
		f.Celsius = Celsius(value)
		return nil
	case "F":
		f.Celsius = FToC(Fahrenheit(value))
		return nil
	}
	return fmt.Errorf("invalid temperature %q",s)
}

func (f * celsiusFlag) String() string{
	return fmt.Sprintf("%f",f.Celsius)
}

func CelsiusFlag(name string,value Celsius,usage string) * Celsius{
	f := celsiusFlag{value}
	flag.CommandLine.Var(&f,name,usage)
	return &f.Celsius
}

```

after we create the type and method and function we should use go install command to install the libray in computer .

```Go
package main

import "leemaster.cn/flag/tempconv"

import "fmt"

import "flag"

var temp = tempconv.CelsiusFlag("temp",20.0,"the temperature")

func main(){

	flag.Parse()

	fmt.Println(*temp)
}
```



# Go Package management

we can see the package inner go file as a moudle to manage , and any module can implement the init() function not only just once 

and the runtime find moudule prcesure like this 

![go-init-process](http://ww1.sinaimg.cn/mw690/0079qc5ply1fsawh4tqkcj312m0hqk17.jpg)
