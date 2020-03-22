---
title: Bigtable 论文
date: 2019-12-07 20:05:53
categories: [论文]
tags: [BigTable, Google]
---

没事读论文系列——BigTable 

<!--more-->

# Bigtable

个人感觉Google设计这个系统的主要目的是用来解释存储在GFS上的文件，重新定义数据的存储方式。

## 数据模型

Bigtable开源的实现是HBase，也就是一个KV数据库，只不过可能数据量有点大，既然是KV存储，那么重点关注K和V的存储方式。

Bigtable论文中定义，所有的值在Bigtable中存储都以任意大小的字节数组进行存储。

那么Key的结构如下：

```
(row:string, column:string, timestamp:int64) -> string 
```

通过 `row`, `column`, `timestamp` 三个坐标可以唯一确定一个值，再简化一下，看起来有一点像数据库的行结构。我觉得如果只需要通过`row`进行索引，那么在写入的时候各个 `timestamp` 就相当于MVCC的版本号的概念，那么其实是可以实现跨行事务的。

好啦，现在我们假设Bigtable的中KV的数据结构如下：

```cpp
struct RecordMeta{
  string row;
  string column;
  int64_t timestamp;
  void * value;
}
```

那么每一行的每个列都应该是这样的结构。现在有一个问题，如何存储呢？或者说如何写如GFS中呢？

论文中也对这个进行了描述，通常来说将多个Row存储在一个Block中，一个Block的大小一般来说是`64KB`，并且Bigtable保持这些Row的字典顺序。

在底层存储中`column`中的数据是按照字符数组的方式存储的，那么自然涉及到如何序列化和反序列化，论文给出了一个模型，`column families` 就像关系数据库中一样，同一列的数据类型应该是一致的，这样可以很准确的对数据进行存储，但是Bigtable并没有完全的实现关系模型。

在索引了`row`的key之后，自然要索引`column`的key，那么Bigtable对`column` 的key 的定义是 `column:qualifer`，这里有一点没搞懂为什么有了row的key还需要一个column的key，难道是因为HBase列存储的原因？如果是这样的话那就好解释了，首先寻找row的key，通过row的Key进行二次寻址，从而找到column的key，直接把一个Page一个Page地从磁盘中拿出来，然后读取。

而在数据的索引key中加入时间戳的概念，其实是为了简化Bigtable的垃圾回收（其实是文件压缩了）通过归并多个数据存储文件，最后生成一个不可变文件，只提供读的功能，下面会看到关于压缩的过程理解。

## 关于存储

不管是SSD还是传统的HDD，根据局部性原理，基本上我们从磁盘抽出来的数据都是冗余的，尽管我们可能只需要按照某个where条件抽出一条数据（当然有Cache系统，这个事情就好一点）。

Bigtable中数据的组织形式是使用SSTable来组织的，一个SSTable和Blocks的对应关系是一个典型的一对多，一个SSTable中包含了很多的Blocks，每一个Blocks中存储的就是数据啦。

关于这个SSTable的实现可以直接看一下LevelDB的源码。

