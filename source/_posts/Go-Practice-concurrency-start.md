---
title: Go-Practice-concurrency-start
date: 2018-06-15 12:29:27
categories:
- Practice
tags:
- Go
---

# Concurrency 

As we know Rob Pike and his team has implemented the CSP(Concurrency Senquential Process) to be the model of concurrency for Go , and now I will write some practice about the concurrency programming in Go and then the CSP(paper) will write in the next blog article . 

<!--more-->

parallel and concurrency are always mentioned in the background development, such as the push service which can hold a long link from user device like phone pad or other to the server cluster . And we can send some push information to the user side by the service program . 

In many model like the Java concurrency model there are many problem waiting us to handle , though there are some framwork such as Netty Disruptor and Akka , but this model is just a common model in many concurrency domain .

 Coroutine a new model for us to write the concurrency program in a senquence method do like in Go and the paper describe this model in a paper **oncurrency Senquential Process** writen by S. L. Graham, R. L. Rivest Editors describes the more details in a logical method .

## concurrency essence

In many language we could think that the concurrency or parallel programming are real difficult in action . I ever think the essence of the concurrency in a method to think in the memory model and cpu work way .

### why we need the concurrency ?

Single process or thread program don't raise any wrong in runtime if don't write some program logic wrong or error , but when the mulit core cpu comming ,we should use the all cpu not just use only one as before .

For example  thumbnail will be cut from a full size image , we have amny images from the user upload and then we should handle the all images to thumbnails . If we use the single process the all images will be in a queue and the time will bu the sum of the all images handle time . But when we use the parllel programming then we can at lest reduce a half time cpu handle the all images . we can use the enough ability of the cpu.

### how do the computer work on ?

We should know a concept called context , when a program run on the conputer , they will have a context which contains the all data and the code text , we can also belive that after the compiler working done ,the instructions will be save in the text segment , and the procedure statement will call the specitic instructions to jump and loop .

All the program context will be save to memeory when the program start , and a single process can execute the instruction step by step . But the problem will raise , when we call the mulit core in cpu to execute the instuction , we don't confirm which instruction will access the context area rightly that is the problem from the concurrency , mulitithread will access to the context not in order not only reading but writing .So we don't get the right result as we use the single process model.

And the switch context will waste some time , so if we don't design a mechanism to control the behavior for the threads or process we could track in trouble . Dead Lock and Live Lock will let the all system panic.

### what is the origin of the concurrency problem ?

As I have infered the problem origin is from the access order , if we let the process access in a memeory area -- context then we should know , the time will like the time using single model.

So many scientist of conputer invent many methods to solve the problem of access order , such as CAS , Lock mechanism , share memeory and so on . And the most famous model called consumer and producer model which can solve the produce data and consume data concurrecny problem.

## goroutine and channel

### goroutine

goroutine is a mechanism like CSP , and more like the disruptor in Java , which don't think the thread and process should fight with the cpu , and the processes in the all programm will use the conmmon context and use the message delivery to work with each other in order .

The goroutine is the execution entry in Go and they will be linked by the channel , and use the channel can let the gorutinue have a method to commuicate with each other goroutine, and the all goroutine will hole the same context .

```Go
import(
    "io"
    "log"
    "net"
    "time"
)

func main(){
    listener,err := net.Listen("tcp","localhost:8000")
    if err != nil{
        log.Fatal(err)
    }
    for{
        conn,err := listener.Accept()
        if err != nil{
            log.Print(err)
            continue
        }
        go handleConn(conn)
    }
}
func handleConn(c net.Conn){
    defer c.Close()
    for{
        _,err := io.WriteString(c,time.Now().Format("5:04:05\n"))
        if err != nil{
            return 
        }
        time.Sleep(1 * time.Second)
    }
}
```

When a client link to this server then will every second recive a time string , use go func grammer can start a goroutine .

### concurrency safe 

We design a judge standard to define the concurrecny safe , we can do the same thing use single process and then do the same thing in the concurrency enviroment and the result is same as the concurrency then we call this parllel program is safe.

Because the cpu dispatch program process or thread in noorder , and as we can know that the all process occur the unsfe status from the situation they all access a same memeory area , if they don't do this thing, we should not care of the safe of concurrency.

Keep the safe of parllel program we should use some methods we call procedure communication 

### channel 

As we know we should let the process do in order or let other processes know the status from other process we should link them overall.

```Go
func main(){
    done := make(chan int32)
    go func(){
        time.Sleep(3 * time.Second)
        fmt.Println("some thing")
        done <- 1
    }()
    <- done
}
```

the main function is a goroutine and the go keyword let a new goroutine to run if we don't use the channel to let them commuicate with each other then after the main exit the goroutine could exit right now .

We can also use the channel to contribute a pipe in two goroutine like this 

```Go
func sender(out chan<- int){
    for x:= 0;x < 100 ;x++{
        out <- x
    }
    close(out)
}

func printer(in <-chan int){
    for v := range in{
        fmt.Println(v)
    }
}

func main(){
    pipe := make(chan int)
    go sender(pipe)
    printer(pipe)
}
```

and use the range we can do for-loop if there have data in pipe and now we can use the just one channel to use in the pipe view like unix like system.

And the channel have two type , cache-channel and nocahce-channel , as slice in Go ,the channel have the capacity to contain the data . if the make function don't set the capacity then will be initialized with 1 and in the two side of the channel if any side don't prepare rightly the other side should wait for the side .

And the cache-channel like a queue in concurrency sender send the infromation one side and the other side the receiver will receive the information.

The channel implementation in Go also have implemented a ring-buffer struct which let a buffer in a ring status , and when write a data into the channel we should know that , we check the full state and then write a data in the buffer and set the head pointer to the new position and the read will read the tail data and then set the tail pointer to the next data stoke . And a ring buffer will link just one type reader and writer in this way can let the handle read and write in the same way ,if we register two types of reader and writer then we could in this situation the one reader read too fast so that the other writer don't write and then the other reader will read the other writer write data .

## synchronize communication

We have use the channel to do the sample syschonsization , we should know that when a group of goroutine need do something synchronized then we should let them to commuicate with the each other in the same group.

## select 

Like the Java nio frame then we should know that the channel could have many but the real handler just have only one ,then we should use a selector to select the all channels and then let the channel event to know what is the next time to do in the next time .

```Go
package main

import "time"
import "fmt"

func main() {

    c1 := make(chan string)
    c2 := make(chan string)

    go func() {
        time.Sleep(time.Second * 1)
        c1 <- "one"
    }()
    go func() {
        time.Sleep(time.Second * 2)
        c2 <- "two"
    }()

    for i := 0; i < 2; i++ {
        select {
        case msg1 := <-c1:
            fmt.Println("received", msg1)
        case msg2 := <-c2:
            fmt.Println("received", msg2)
        }
    }
}
```

We could use only one goroutine to select the event and then send the type to the other , and then handle the writer write data . decoupling the reader and the writer then we should use the select to bind many type writer and reader pair .

# examples

## cncurrency directory read 

