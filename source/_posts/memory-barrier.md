---
title: 内存屏障
date: 2019-12-06 23:21:45
tags:
- C++
---

关于lock-free的底层原理思考

<!--more-->

# 关于CPU和存储

在计算机组成原理中，学习过现代的计算机是以存储为中心的，而内存屏障是用来保证指令顺序的，也就是在CPU流水线执行的时候通过插入一些tricky来保证高层程序逻辑上的顺序一致性。

## CPU流水线



## CPU 多级Cache 

## CPU 指令重排

### 一个小问题

还是先从一个问题开始出发:

如果在一个并行程序中，指令的执行顺序如下，那么执行完成后，x和y的值有几种可能性？

```
	CPU 1		        CPU 2
	===============	===============
	{ A == 1; B == 2 } // RAM中的值
	A = 3;		      x = B;
	B = 4;		      y = A;
```

这个问题提出来会比较抽象一点，抽象的原因是因为没有业务场景，如果有业务场景，那么虽然程序是并行的，那么这两个并行的指令序列执行完成后，结果应该是遵守一个序关系的。

那么可能的指令执行顺序如下：

```
	STORE A=3,	STORE B=4,	y=LOAD A->3,	x=LOAD B->4
	STORE A=3,	STORE B=4,	x=LOAD B->4,	y=LOAD A->3
	STORE A=3,	y=LOAD A->3,	STORE B=4,	x=LOAD B->4
	STORE A=3,	y=LOAD A->3,	x=LOAD B->2,	STORE B=4
	STORE A=3,	x=LOAD B->2,	STORE B=4,	y=LOAD A->3
	STORE A=3,	x=LOAD B->2,	y=LOAD A->3,	STORE B=4
	STORE B=4,	STORE A=3,	y=LOAD A->3,	x=LOAD B->4
	STORE B=4, ...
	...
```

结果为（x，y两个变量，所以一定有这两个变量全排列多个结果）：

```
	x == 2, y == 1
	x == 2, y == 3
	x == 4, y == 1
	x == 4, y == 3
```

### 分析一下指令重排



# 关于屏障

## 并行还是并发

## 内存语义

## 正式介绍屏障

# 参考

[Kernel 内存屏障文档](https://www.kernel.org/doc/Documentation/memory-barriers.txt)