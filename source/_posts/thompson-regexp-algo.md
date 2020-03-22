---
title: Thompson 正则表达式搜索算法
date: 2020-03-22 23:27:46
categories: [Compiler]
tags: [rust]
mathjax: true
---

用Rust 实现了 Regular Expression Search Algorithm 这份论文中提到的搜索算法

<!--more-->

这份1968年的论文，现在看起来有点老了，但是论文写的很OK的，主要介绍了一个方法，通过将模式编译成为IBM 7704 机器代码来将一个文本中全部符合模式的字符全部识别出来。

# 论文学习

论文中设计了一个程序，用来将一个正则表达式转换为一段IBM 7094汇编代码，从而可以接受任意的输入文本，并将匹配当前pattern的全部字符串输出出来。那么论文既然是实现了一个编译器用来将正则表达式生成为一段汇编识别自动机器，那么重要的工作就是设计这个编译器的算法。

## 编译器设计 

这个编译器包含了三个并行的阶段。

* 第一阶段语法过滤，这个阶段允许符合正则表达式的语法规范的句子通过，同时插入`.`符号显式标记正则表达式的两部分的连接
* 第二个阶段将正则表达式转换为RPN模式，e.g. `a|b` -> `ab|`，即转换为逆波兰式
* 第三个阶段是生成汇编语言的阶段 

### 逆波兰表达式

对于一个正则表达式`a(b|c)*d` 转换成为逆波兰表达式后 `abc|*.d.` 这里的`.`表达的是连接的意思，简单来说就是将两个链表直接连接到一起。

生成逆波兰表达式的算法如下描述：

1. 申请两个栈`expr`和`operator`其中`expr`保存的是最后输出的表达式，`operator`临时保存运算符号。
2. 从左到右读取字符
  1. 如果当前的字符`x`是操作数的话，直接压入栈`expr`中 -> `if x in [a-z] then push x to expr`
  2. 如果当前的字符`x`是`(`的话，将符号压入`operator`中 -> `if x == '(' then push x to operator`
  3. 如果当前的字符`x`是`)`的话，将`operator`中的符号依次弹出并压入`expr`中丢弃最后的`(` -> `if x == ')' then loop push operator.pop() to expr`
  4. 如果当前的字符`x`是`)`和`(`之外的运算符的话
    1. 如果`operator`栈顶的运算符为`(`的话直接将`x`压入`operator`
    2. 如果`operator`栈顶的运算符不为`(`的话，依次弹出`operator`压入`expr`，直到栈顶的运算符优先级高于`x`或者为`(`再将`x`压入`operator`，如果`operator`为空直接压入`expr`

> 转换RPN的关键就是用两个栈实现了优先级分析，因为RPN表达式自带优先级，通常对于一个运算表达式，完全可以表达成为一颗树，如果是中缀表达式的话，只需要进行一次中序遍历带计算就可以拿到结果，而在深处的表达式子树拥有更高的优先级。

Rust实现一下这个算法

```rust
fn generate_rpn(infix: &str) -> String{
  let mut operators: Vec<char> = Vec::new();
  let mut expr: Vec<char> = Vec::new();
  for ch in infix.chars(){
      match ch{
          '(' => operators.push('('),
          ')' => {
            while let Some(op) = operators.pop(){
              if op == '('{
                  break;
              }else{
                  expr.push(op);
              }
            }
          },
          '|' | '*' => {
            loop{
              if let Some(op) = operators.last(){
                if *op == '('{
                    operators.push(ch);
                    break;
                }else{
                    let op = operators.pop().unwrap();
                    expr.push(op);
                }
              }else{
                expr.push(ch);
                break;
              }
            }
          },
          _ => expr.push(ch)
      }
  }
  operators.iter().for_each(|ch| expr.push(*ch));
  expr.iter().collect()
}

#[test]
fn test_rpn(){
  let res = generate_rpn("a(b|c)*d");
  assert_eq!(res, "abc|*d");
}
```

