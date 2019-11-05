---
title: Rust 程序宏
date: 2019-06-27 22:47:23
tags:
- Rust
---

Rust 中的宏是一个很重要的组成部分，Rust中分为两种宏，一种使用*macro_rule!*来定义替换类型的宏，还有一种就是使用程序宏，在编译过程中来动态扩展Rust语言特性的宏。

<!--more-->

比如经常的用法

```rust
#[derive(Debug)]
struct Person{
  age: i32,
  name: str,
}
```

用于对某种类型的对象进行动态实现某种接口时，比如我们经常会为某种类型派生一个Debug特性，在Debug时进行打印适合阅读的字符串。而通过一句话标记了某种过程宏之后的结构，就可以拥有这种过程宏所带来的内部替换，也可以认为这个宏起到一个扩展原有程序的能力。

*值得注意的是，含有程序宏的项目需要声明连接proc-macro，才可以在编译时期使用宏的特性*

# 程序宏的分类

* 函数式宏
* 代码生成宏
* 属性宏

函数宏，顾名思义，这个宏是可以想函数一样使用，简单的来说，就是可以在编译时期，运行的代码，比如我们可以写一个函数，用来读取一些配置文件，读取完成之后将所有的值生成一个map，并且作为一个全局对象来进行存储。

```rust
extern crate proc_macro;
use proc_macro::TokenStream;

#[proc_macro]
pub fn read_from_config(_item TokenStream) -> TokenStream{
  // String config_variant name String file Path 
  //TODO should use the string conact to conact the define 
}
```

代码生成宏，其实是为了方便为某些结构生成一些特性的，比如常见的 *#[derive(Debug,Ord)]*这样的方式。


# 程序宏开发库——proc_marco

TokenTree TokenStream 
