---
title: 关于线段树
date: 2019-12-07 16:05:55
mathjax: true
categories:
- Datastruct
tags:
- Datastruct
---

线段树的学习和思考

<!--more-->

# 从一个问题开始 

对于一个给定序列 $\left[ a_0, a_1, \cdots, a_n\right]$ 计算一个区间$\left[ l , r \right]$内的和。

朴素的解决方法

```cpp
int sum_range(vector<int>& seq, int l, int r){
  int sum = 0;
  for(auto i = l; i < r; l++){
    sum += seq[i];
  }
  return sum;
}
```

但是这样的话是存在重复计算的，每一次都会进行一次区间累加，那有没有更好的办法呢？

# 线段树

是的，那就用线段树吧，使用线段树的场景当然不仅仅可以用来寻找区间和，理论上来说，所有的序列聚合函数都可以用这种方式来省略计算，比如最大最小值。

## 线段树的定义 

* 每一个叶子节点和序列中的元素一一对应
* 每一个内部节点都代表某子序列的聚合值

## 线段树的结构 

一般来说，直接用堆的思路来构建线段树，那么一个线段树就是一个堆啦。但是和堆的差别就在于，使用数组实现的时候，其内部是有空洞的。

比如对序列: $\{ 1,3,5,7,9,11 \}$ 

构建一个线段树，构建后的状态是:

$\{ 36,9,27,4,5,16,11,1,3,NULL,NULL,7,9,NULL,NULL \}$

虽然使用数组有点浪费，但是这样还是比较简单的，那么使用数组表示线段树的时候，这个数组长度应该是多长呢？

对于线段树的定义，我们可以知道，线段树的叶子节点都是序列中的元素，那么可以知道的是，如果当前序列长度为$n$时，那么内部节点的个数为$n-1$所以节点总数是$2 \times n - 1$，而为了使用堆表示线段树，所以的话，需要开辟一个堆空间能完全容纳这些元素。

根据上面的推导来看，并且线段树使用完全二叉树的方式表示，那么节点数量应该为 $2 \times 2^{\lceil log_2n \rceil} - 1$ 这个数字就是需要开辟的内存空间。

## 线段树的构建

这里我们将聚合函数固定为SUM，也就是求和了。

构建的过程是需要从底层规约上来，那么既然是完全二叉树，但是写代码还是要从上向下来实现。

基于上面说的，首先要计算的就是整个序列的和，那么整个序列的和分为两部分一部分是左半部分的和另外一部分是右半部分的和，两个和相加就是最后的结果了，这样就可以构建出来一个线段树了。

## 线段树的查询

查询一个range的和，可以直接将range拆分为几部分，分别查询，查询后的和就是最终的结果，同样是一个二分查找的过程。

## 实现

```cpp
#include <iostream>
#include <vector>
#include <cmath>

using namespace std;

inline int mid(int l, int r){return (l+r) / 2;}

inline int left(int x){return 2*x+1;}
inline int right(int x){return 2*x+2;}

class SegmentTree{
  int * value;
  vector<int> & origin;
  int build(int,int,int);
  int inner_search(int,int,int,int,int);
public: 
  explicit SegmentTree(vector<int>&);
  int Search(int,int);
};


SegmentTree::SegmentTree(vector<int>& seqenuce):origin(seqenuce){
  int height = (int)ceil(log2(seqenuce.size()));
  int size = 2 * (int)pow(2,height) - 1;
  value = new int[size];
  // 从最顶构建
  build(0,seqenuce.size(),0);
}

int SegmentTree::build(int left, int right, int index){
  // left == right 插入值
  if (left == right){
    value[index] = origin[left];
    return origin[left];
  }

  int mid = ::mid(left,right);
  // 递归构建
  value[index] = build(left,mid,::left(index)) + build(mid+1,right,::right(index));
  return value[index];
}

int SegmentTree::inner_search(int left, int right, int pleft, int pright, int index){
  // 在区间内直接返回
  if (left <= pleft || right >= pright) return value[index];

  // 不在当前区间内返回 0 
  if (left > pright || right < pleft) return 0;

  //向下寻找
  int mid = ::mid(pleft, pright);
  return inner_search(left, right, pleft, mid, ::left(index)) + inner_search(left, right, mid+1, pright, ::right(index));
}

int SegmentTree::Search(int left, int right){
  int len = sizeof(value) / sizeof(int);

  if (left > len || right < 0 || left > right)return 0;

  return inner_search(left,right,0,len-1,0);
}

int main(){
  vector<int> value;
  value.push_back(1);
  value.push_back(3);
  value.push_back(5);
  value.push_back(7);
  value.push_back(9);
  value.push_back(11);

  SegmentTree tree(value);

  cout << tree.Search(0,5) << endl;

  return 0;
}

```