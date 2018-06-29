---
title: 'Netty architecture and lifecycle '
date: 2018-06-29 16:57:43
categories:
- Practice
tags:
- Java
- Netty
---

# Netty Components 

Netty is a high-performance network framework to use constuct a C/S style application , and it apply the Reactor pattern in I/O concurrency and I think when cooperate with the Disruptor will have the most high performance experience in high concurrency programming .

<!--more-->

## Overview

The architecture of netty will be desplay at below , then we can know these concepts of the I/O in netty and then we should know that the socket will be originazed in the channel and then will be driven by the EventLoop and the EventLoopGroup .

![netty-arch](http://ww1.sinaimg.cn/mw690/0079qc5ply1fss5kao6j4j311v0i5adk.jpg)

As this image to descibe the Netty architecture 

I think the socket will be deescibed in Linux use the file descriptor and then we can use the base operations for file to operate them and then will construct a base absolution, the base to abstract as this situation because the connection not only the stream connection but also the datagram connection they will be familier into write by the method from one side to other side use the concept of stream and then we can use the linux file stream operations to operate them .

The channels will contain the socket and will have the input and output endpoint we can belive this is the abstraction of the socket file descriptor in Java and then we can use the channel to write or read .

But the base concept of Netty is the event-driven , events in my opnion it's a state in the programming in network in the lifecycle of a socket there are many states to describe the channel state , before the channel close there have a time shaft for a connection , we have to open a connection and the connection will have a base abstraction in computer use the file descriptor , and now the connection will be the writeable and readable then the parallelism event handler will be triggered , and the ChannelHandler and the ChannelListener will be event receiver and the different event will be assign to the differnet handler , so we should use a container to originize the ChannelHandler we call this thing as ChannelPipline .

And the netty have do the base abstraction of the transport for many thing, the transoprt will wrap the base methods to use then will send the method to call the ChannelPipeline then the pipeline will transport the event or data flow to the chain of OutboundHandler or InboundHandler

And the ChannelFuture in my guess will use a block to make the implementation for the wait and notify , I can see this thing that the in the control by the pipeline their will have a lock and then will return the ChannelFuture who have the lock instance and then call the get method will trigger the thread now to await and the notify will callback after a triggering of a specific event . but the future have use the spain lock to implement the wait .

![noblock-IO](http://ww1.sinaimg.cn/mw690/0079qc5pgy1fssaedk460j313g0n9442.jpg)

## EventLoopGroup and EventLoop

## Channel

## ChannelHandler

## ChannelPipeline

## Encoder & Decoder

## Bootstrap 


# Transport API 

# Codec 

# Protocal 
