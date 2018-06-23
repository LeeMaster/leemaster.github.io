---
title: Go Tutorial - programming in senquence
date: 2018-05-19 10:40:45
categories:
- Tutorial
tags:
- Go
---

# Why use Go

Before I was a Java developer in application of web , sometimes had tried Scala and kotlin but not like them as Go .

Recent day I had join the **Meituan-Dianping** cloud computing department , and in the next time I will do something intresting in System Programming , so will use more C and Golang may be will use some Rust to complete the development in my works.

<!--more-->

## Annoying Garbage Collection

Before I was working with C/C++ in my student life at HEBNU , what the fuck when I use the malloc or free and also in new delete , more and more in the programming forget the free the memory then will let the program run out .

## Concurrency difficult in Java 

Although the Java concurrency model is really useful in enterprise development in some action ,the package java.util.concurrency is not easy for developer to catch the key in design the system .

Not only the above , but also this , the java.util.nio epoll bug is not fix up as soon, but use the cpp boost and ACE frame is not easy to do some thing effective and then I want choose a program language not only contain the base abstraction but also own the effective ability in concurrency and socket programming .

let's Go !!!!! 

# Go base process 

## Base Grammer

* package 

the go pragram will in time run in the main package and the package is the base to manage the library like python and java

* import 

use this keyword will let us to use more effictive library from other developers 

* func 

```GO
func main(){
    fmt.Printf("Hello word")
}
```
like this statement will use the function in action and there are more details in the next chapter 

* standar library 

Go standar library is very good in practice and I will introduce them in the next articles

## Compile Enviroment

* go run ***.go

use this run command will tell the go to compile and link and then run the go program

## Variable and Constant

### Variable
In every lanaguage the most important thing is define the variable in use , not only the static but the automatic variable and then will use in many way to sign the variable and other.

Here are some Demo

```GO
var intVal int
var strVal string

var arrVal1 [10]int
var arrVal2 [10]string

var arrSlice []int 

var maoVal map[string]int

var funcVal func(par int) int

var(
    defVal1 int 
    defVal2 int 
)

inferVal1 := 10
inferVal2 := "string"

```
This above code display a base method to define the variable in action .

```GO
func getName() (firstName,midName,lastName string){
    return "leemaster","lee","XiaoYu"
}

_,_,lastName = getName()

fmt.Printf(lastName)
```
anonymity variable like above and then will use **_** to receive then will let the last to get the variable we really need

And then we want know the base variable type such as **int、uint、int32、 int64、float32、float64、complex64、complex128**

### Constant

Constant may be same as the constant in java or other program language

```GO
const Pi float64 = 3.1415926
```

It's easy to use the constant in programming and we can't use the infer in go to get the dynamic type for the constant . like thie below 

```GO
const PI := 3.1415926
```

And Go had set some defined constants such as **true false iota**

```GO
const (
    c0 = iota
    c1 = iota 
    c2 = iota
)

const (
    c0 = iota
    c1 
    c2
)
```
When the compiler find one time iota raised then will let the iota value to be added one and then set the value to the left value(which is the address used to be modified in C defined)

So use the **const** keyword may can be used in the in enumeration to define some constant will be used in the next program 

```GO
const (
    Sunday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
    numberOfDay  // this constant can't be use in the other packages 
)
```

No enum keyword and then also can define the enumeration and then use in the package or other package , because the First capital character word can be expose to other packages.

## Type System

Type in Go is real useful , be more easier to use in action .

### Base Type 

* Boolean Type bool
* Interger Type type int int32 int64 uint uintptr
* Float Type float32 float64
* Complex Type complex32 complex64
* String Type string
* Character Type rune
* Error Type

### Compound Type
* Pointer like the pointer in C
* Array [array_size]type
* Slice []type
* Map map
* Channel chan
* Struct struct 
* Interface 

### Attention

* The Integer type may depend on the platform to compile the Go
* int32 cna't be equal to the int type 
* float type like the C and Java will be init to double type (float64)
* string in travales will in to the rune type
* Array type will be as the value type and then will in copy when the function call or other use operation

#### Slice
Slice maybe more like a pointer in c like **int\*** to point a memory area , so in Go it's same as C language , the slice we can assume as the alias for the pointer of array . And the slice has the ability for dynamic increment and decrement , so we can use the **make()** function to create an empty slice, the slice will have the capacity and the init capacity , use the make to create . And we can use the **len()** to ge the element counts in a slice and use the **cap()** to get the capacity of the slice.

```GO
const ArrayVal [10]int = {1,2,3,4,5,6,7,8,9,10} 

sliceVal1 := []int{1,2,3,4,5}

sliceVal2 = ArrayVal[:5]

sliceVal3 = ArrayVal[2:5]

appendSlice = make([]int , 5, 10)
appendSlice = append(appendSlice , ArrayVal[0:5])
appendSlice = append(appendSlice , []int{1,2,3})
// then the value is 1,2,3,4,5,1,2,3
```

And the **copy()** function will copy the elements from first slice to second slice and if the length of two slice is not equality then will based on the short array.

#### Map 
It's more like the container in Java and C++ , In Java is java.util.Map interface and in C++ it's the std:map ,but in Go it's supported native from the go engine , That is also a reason I use Go to contribute the next project .

There is a simple demo use Map

```GO
package main
import "fmt"

type Person struct{
    ID string
    Name string 
    Age int32
}

func main(){
    var personMap map[string] Person
    personMap = make(map[string] Person)

    personMap["LeeMaster"] = Person{"12345","LeeMaster",23}

    person,ok := personMap["LeeMaster"]

    if ok {
        fmt.Printf("%s,%s,%d",person.Name,person.ID,person.Age)
    } else {
        fmt.Printf("Not found")
    }

    delete(personMap,"LeeMaster")
}
```

It's simple to use map type ,like java , but the native method insert and get is very effective and brefily . And delete the element by the key use the **delete()** function to do the same thing in java . 

## Control Flow

### if statement

```GO
if true {
    // true statements 
} else {
    // false statements 
}

```

But it's difficult from java or other lanaguage , Golang don't support the return statement in the if statement .

In Java : 

```Java
public int judgeElement(int ele){
    if(ele > 5){
        return ele;
    }else{
        return 0;
    }
}
```

In Go :

```GO
func (ele int32) (re int32){
    if ele > 5 {
        re = ele
    } else {
        re = 0
    }   
}
```
more details will show in the next chapter about the functions 

### loop statement

Now in Go we just need the iterator to use !

we just have only keyword **for**

```GO
for{
    fmt.Println("Dead Loop")
}

for i := 0 ; i < 10 ;i++{
     fmt.Println("Ten Loop")
}

var demoMap map[string] string
demoMap = make(map[string] string)

demoMap["Hello"] = "word"
// ....

for k , v := range demoMap{
    fmt.Printf("%s,%s",k,v)
}
```

In the Go loop statement we don't need the iterator and the interface for use , like the vector<int> :: iterator ,and Java Iterator

### jump statement 

Like C goto will use the deep loop to jump 

### switch statement

This statement is like the other language to do so 

```GO
switch i {
    case 1 :
        fmt.Println("1")
    case 2:
        fallthrough
    case 3:
        fmt.Println("3")
    case 4,5,6:
        fmt.Println("4,5,6")
    default:
        fmt.Println("Default")
}
```

**fallthrough** keyword can let the program to execute the next statement to do , like the situation in C we don't add the break in switch . And Go can also to use many switch case like above 4,5,6 

## Function Usage

Function is the base for the stracture program and it can orginaze the all logic code in a brefy way , and can wrapp the all data and the flow to do the same thing in many control flow. In function programming Function will as the base member in the all program and then will use the to be the arguments in other functions and as a return value ,we call these function as a closure.

### Function Define

In Go the function define in the below way .

```GO
func funcNoArgs(){
    fmt.Println("No Return value")
}

func funcNoArgsReturn() int{
    return 1
}

func funcManyReturn()(name string,age int){
    return "LeeMaster" , 23
}

func funcManyReturn2()(string,int){
    return "LeeMaster" , 23
}

func funcManyArgs(args ...int) int{
    var res int
    res = 0
    for _,item := range args{
        res += item
    }
    return res 
}

func funcAnyType(format string,args ...interface{}){

}

```

In the demo code we can see many define methods for Function , we can use indeterminacy arguments and many return value not use the struct like in Java use class or interface to return the wrapper .

And we can use the interface to translate any types to this function and then we can handle the any type to do many things 

### Function Call

we call a function as Java and C but we can use **_** to put away the return value we don't need 

### Function Arguments

Go language support more than one arguments in the same types which we can't ensure before we call the functions the demo is above in the chapter ***Function Define*** 

And Go also support the arguments the type we don't ensure and then we can use the **interface{}** to infer it

### Function Return Value

Function return value is also same as the Java and C ,but we don't need receive the all values return from a function , also we don't need translate the class or other struct type to wrapper the function handle result. We call this character as many return value.

### Anonymous Function and Closure

In Go the function will be seen as a value or pointer to use and translate , we can do as these situation 

```GO
funcAnoy := func(age int,name string){
    fmt.Printf("%s age is %d \n",name,age)
}

func funcCall(){
    funcAnoy(23,"LeeMaster")
}

```

The closure is a method to carry the variable from a function to another function and save the situation and will use in the next function ,then we can use the closure.

And anoymous function is a method to support the closeure 

## Handle Exception

### error interface 

In Go as same as in Java , there is a interface that refer to the error type ,in java we will create our own Exception inplements the Exception interface , but in go we don't need to declare any statement to implements the error interface in expilict , the Go type system support the OOD and OOP use a simple method.

```GO
type error interface{
    Error() string
}
```

This interface will refer to an error and then we need to create our own error to implements the interface method.

for example 

```GO
type OwnError struct{
    Value interface{}
    Op int
    Err error
}

func (o * OwnError) Error() string{
    return o.Value + o.Op + o.Err.Error()
}
```

we don't have any keyword in Go to declare the interface define .

### defer 

In Java and C we need close the file stream after the operation for a file write or read and 
use defer as try finally block do so .

```GO
func copyFunc(dst,src string)(w int64 , err,error){
    srcFile , err := os.Open(src)
    if(err != nil){
        return 
    }
    defer srcFile.Close()
    //...
}
```

after the file open then we need to close the hanlder of this file so we need defer to close the handler 

### panic and recover 

**panic** is not same as the **os.Exit()** panic will print the wrong string and then jump to the defer register handlers and use the LIFO order to handle . panic confirm that the same goruntinue defer will trigger in the LIFO order , but don't do in the other goruntinue.

The panic will stop the process in one goruntinue and then will call the defer handler , the defer must register a function and then will get the error type and then chose the handler function which we defined in the same file.

When a type or struct inner function implements the interface function then we say the type is satisfies the interface that is differnet from the Java or other .

And in the go program the type system say that thing which define the any struct object implement a interface inner function or method then we assume that the struct is the duck type as the interfce implementation then we can use this class in any whare , but the panic will record the error when the class don't define the methods in the interface we should implement in the last time .And we can do this thinking in the Objectve-C in OC we can belive that the class don't have real type system as Java and the functions call most like the message delivery then the message don't find the handler that the inner handler don't throw any wrongs or error in the time just don't do execute this method.

There are some examples :

```GO
package main

import "fmt"

func deferFunc(){
    defer func(){
        if err := recover();err != nil{
            fmt.Println(err)
        }
    }()
    panic("Panic start")
}

func main(){
    deferFunc()
    fmt.Println("Next Trick")
}
```

as this code the panic will use and in the main process the panic will throw the exception and then will let the wrong or error to the defer and then will do the same in the next trick

And we can see the deep thing , that when a exception raise in the program and then we can check the exception type and then we can use the interface charge in type to do thins thing.

```GO

type SomeError interface{
    Handle(err interface{})
}

type PathError struct{
    Id string
    Name string
}

func (p * PathError) Handle(err interface{}){
    fmt.Println(p.Id,p.Name,err)
}

func doBusiness(){
    defer func(){
        if e := recover();e != nil{
            if se,ok := e.(SomeError);ok{
                se.Handle("catch the right wrong type ")
            }else{
                panic(e)
            }
        }
    }()

    doPanic()

    fmt.Println("success in this function")
}

func doPanic(){
    err := &PathError{"12,345","leemaster"}
}

```

We can use this method to handle the all exception , and then we can use the interface query to query the type real parent and then we can use this example to right handle the all panics and then will do gracefully Exception handle

# Project Management

## Project Structure

## Project Unit Test

## GOPATH

## GO Cli
