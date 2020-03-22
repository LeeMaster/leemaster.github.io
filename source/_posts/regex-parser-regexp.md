---
title: RegExp Parser 的实现（2）
date: 2020-03-14 02:39:23
categories: [Compiler]
tags: [rust]
mathjax: true
---

聊一聊正则表达式

<!--more-->

在上一篇文章[RegEx Parser 的实现（1）](/2020/03/14/regex-parser-overriw/) 简单地分析了一下如何识别一个单词，并且成功地从一个全是分支地程序一步一步的改造成了一个基于状态转换的循环处理程序。 那么在这篇文章里面呢，主要地工作就是研究一下正则表达式！

# 正则表达式

# Thompson 构造

