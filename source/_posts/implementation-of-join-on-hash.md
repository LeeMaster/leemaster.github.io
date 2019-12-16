---
title: Hash索引上的聚合操作算法实现
date: 2019-12-16 00:26:09
mathjax: true
tags:
- Storage
---

刷了刷数据库系统概念中关于聚合算法的一些知识，在公司一直写Java好久没有写CPP了，正好捡起来做一个对千万数据文件的聚合操作的实现。

<!--more-->

# 在Hash上实现聚合

## 问题

在一个文件内有超过千万的数据，写一个程序实现在千万数据量下的聚合操作实现（带Where条件），统计千万数据下的某一列的最大值，最小值。使用SQL描述一下：

```sql
select MAX({column}) from {some_table} where {condition_col} between {start} and {end} 
```

## 关于聚合算法的一些小知识

