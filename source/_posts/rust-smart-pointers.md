---
title: Rust 智能指针
date: 2020-03-08 16:42:54
categories:
- Rust
tags:
- Source
---

最近在学习无锁并发，无锁并发的数据结构中queue是最典型的一种数据结构，但是实现queue的时候，因为Rust的生命周期和借用检查器，所以需要使用 *std::boxed::Box* 来进行引用传递，借此机会重新看一下*std::boxed::Box*等一堆智能指针的实现。

<!--more-->

Sized 特性，用来对泛型进行标记，从而指导编译器进行空间分配