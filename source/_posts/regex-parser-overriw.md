---
title: RegExp Parser 的实现（1）
date: 2020-03-13 23:16:26
categories: [Compiler]
tags: [rust]
mathjax: true
---

用Rust写一个Regex处理引擎，重新捡起Compiler回忆了一下。

<!--more-->

# 概览

正则表达式的主要工作就是验证一个字符串是否符合这个正则表达式的规则，如果是正则表达式的规则，那么接受这个字符串，并为这个字符串生成一个Token表示，生成了以后填入Token流，等待下一步的Parser处理成为AST。

## 识别一个单词

既然是为了识别一个字符串，那么这个字符串可以认为是一个单词，解释这个问题，首先从识别一个简单的字符串*new*开始。

```python 
ch = next_char()
if ch = 'n': # S0
  ch = next_char()
  if ch == 'e': # S1
    ch == next_char();
    if ch == 'w': # S2
      return Token(KEYWORD, 'new', now_position() - 1) # S3
    else:
      return Error() # SE
  else:
    return Error() # SE
else:
  return Error() # SE
```

手写识别一个固定单词的程序是很简单的，因为当前这个程序能接受的字符串只有一个，所以只要一个一个的匹配就行了， 如果我们给这个程序的每个步骤加上一个状态，那么每个*if*分支代笔一个状态。程序中的注解表达这个意思，那么当程序到达状态*S3* 的时候，我们认为匹配成功，当程序返回状态 *SE*的时候我们认为识别失败，这样的话，完全可以一般化这个识别的过程，即从开始匹配开始，一路状态转移，当到达接受状态的时候，停止接受字符串，返回最终接受的字符串。那么如果现在这个程序要识别*new*和*not* 呢？

```python
ch = next_char()
if ch = 'n': # S0
  ch = next_char()
  if ch == 'e': # S1
    ch == next_char();
    if ch == 'w': # S2
      return Token(KEYWORD, 'new', now_position() - 1) # S3
    else:
      return Error() # SE
  else:
    if ch == 'o': # S4
      ch = next_char()
      if ch == 't':
        return Token(KEYWORD, 'new', now_position() - 1) # S5
      else:
        return Error() # SE
    else:
      return Error() # SE
else:
  return Error() # SE
```

程序就变成了上面这个样子，那么随着这个程序能接受的单词越来越多，我们会发现这个程序会变得越来越大，而根据上面的解释，我们发现字符串匹配的过程好像是一个状态转移的过程，而在日常写算法题的过程中，我们发现DP好像也是用一个什么东西来记录状态，然后根据一个事件触发状态转移，从而走到一个新的状态。

阿哈，那么根据这个猜测，我们完全可以通过循环来改造分支处理程序，处理办法就是用一个Map记录上。

```python
trans_map = {
  (S0, 'n') : S1,
  (S1, 'e') : S2,
  (S1, 'o') : S4,
  (S2, 'w') : S3,
  (S4, 't') : S5,
}
accepted = [S3, S5]
state = S0
record = "" 
while(ch = next_char()):
  state = trans_map[(state, ch)]
  if state == None:
    return Error(record, now_position())
  if accepted.contains(state):
    try:
      return Token(KEYWORD, record, now_position() - len(record))
    finally:
      record.clear()
      state = S0
  else:
    append(record, ch)

```

嗯大概就是这个样子了，主要是上面的分支结构在足够深的时候，就是一个典型的DFS而已，只不过我们手写出来的，当然通过Rust之类的宏系统可以自动生成，那么如果我们可以通过一个Map来记录这个状态转移的过程，那么就少了很多的分支，程序更简单了，当然在实现上主要还是通过递归下降的方式来搞定的，所以自然会减少栈的使用，那么根据这个结论，我们觉得识别一个单词的关键点在于如何构造这个Map，从而进行循环匹配。（当然这个程序是有问题的，如果next_char()返回了一个None怎么办？） 

## 形式化识别器的定义

上面我们发现了这个识别器（Scanner）的雏形就是这个样子了，那么如何把这个东西在数学上形式化，从而找到递推关系，写下来程序。

* $S$ 有限的状态集合包含错误状态 $S_e$
* $\sum$ 有限的字符集合
* $\delta \left( s, c \right) $ 状态转移函数集合
* $S_0$ 起始状态
* $S_A$ 接受状态集合

对于这个形式化的定义，可以认为一个 Scanner接受的字符串 $x_1 x_2 x_3 \ldots x_(n-1) x_n$ 可以形式化地表示为 :

$\delta \left( \delta \left( \ldots \delta \left( \delta \left( \delta \left( x_0, x_1 \right), x_2 \right) , x_3\right)  ,\ldots \right) , x_n \right) \in S_A$

一旦出现这个公式，很显然啊，这就是一个递归程序的呀，因为一层套着一层的，所以如何将递归转换为递推其实就是在用记忆化搜索的技巧。

那么有了形式化的定义和分析，在接下来的文章中就研究如何实现这个记忆化表的生成了！

