---
title: LevelDB 源码中的设计思路
date: 2019-12-19 00:32:08
tags:
- leveldb
---

关于LevelDB源码的一些设计技巧

<!--more-->

# LevelDB 概览

>先忘记LevelDB是一个存储引擎，我们想象他就是一个KV存储结构好了，如果是KV存储结构，那么必然少不了数据结构关于`MAP`抽象数据结构的描述，当然为了更快一点，让这个`MAP`有序好了，那么参考数据结构上`MAP`和`HEAP`的两个抽象数据结构的接口。

>为了存储大量的数据，我们需要让我们的这个结构可以和磁盘打交道，打交道的方式也比较简单，能读写文件就行了。

>LevelDB将随即写转换为了顺序写，这个操作其实就是LSM算法的思想，`Log Structed Merged` 仔细的品，我们随即写不直接戳磁盘去，直接戳内存，然后呢，在向磁盘归并的时候找到文件中重叠的部分，然后归并掉，很显然这个是一个写放大的过程，因为要读，读完了再归并，LevelDB没有优化，但是TiKV/Titan作为一个RocksDB插件正在优化这个问题，思想来自`Wisckey`。

## LevelDB 架构

我们先大概看一下LevelDB的数据结构

* Memtable ImmutableMemtable 
* Level0 
* Level1 - LevelN

主要的就这三个东西了，当然TableCache和Bloomfiter还没有出现，无非是用空间换时间，从而提升查询效率。

那么既然要戳磁盘去，如何组织文件呢？

* SSTable 文件 有多个，用来存储数据的
* MANIFEST 文件 有多个，用来实现快照隔离的，可以利用这个东西实现数据库的Repeated Read
* CURRENT 文件 存的是当前MANIFEST文件，也就是最新版本的数据
* log 文件 WAL日志文件，用来恢复的，可以认为是一个redo undo 日志合体

LevelDB写数据的操作流动

```
Memtable -> Level0 SSTable -> LevelN SSTables 
```

LevelDB读数据的的流动

```
Memetable -> SSTables 
```

## Iterator 模式 

在之前提到过，我们直接将LevelDB堪称一个 `MAP` + `HEAP` 的组合抽象数据结构，那么既然是数据结构就是要存储数据的，为了从数据结构中拿出数据，需要定义抽象的读接口，Iterator正是不二之选，直接将所有的读操作统统安排进去作为接口。

其中精华的部分，可以用这样一个抽象的问题来解释，给定N个数组，查找其中某个Key的值。

摘取LevelDB中的代码进行分析

```cpp
//merger.cc 
class MergingIterator : public Iterator {
 public:
  MergingIterator(const Comparator* comparator, Iterator** children, int n)
      : comparator_(comparator),
        children_(new IteratorWrapper[n]),
        n_(n),
        current_(nullptr),
        direction_(kForward) {
    for (int i = 0; i < n; i++) {
      children_[i].Set(children[i]); // 依次初始化子迭代器数数组
    }
  }

  void Seek(const Slice& target) override {
    for (int i = 0; i < n_; i++) {
      children_[i].Seek(target); // 在子迭代器中依次寻找
    }
    FindSmallest(); // 因为 Iterator实现了迭代器的取值Traits 因此可以直接拿出值来
    direction_ = kForward;
  }
}
Iterator* NewMergingIterator(const Comparator* comparator, Iterator** children,
                             int n) {
  assert(n >= 0);
  if (n == 0) {
    return NewEmptyIterator();
  } else if (n == 1) {
    return children[0];
  } else {
    return new MergingIterator(comparator, children, n);
  }
}

```

剩下的代码在LevelDB中比较完整了，可以自己看一下

* include/iterator.h
* table/intertor_wrapper.h
* table/merger.h 
* table/merger.cc

LevelDB中的迭代器的功能主要如下：

* Vaild() 迭代器是否已经迭代到结束了
* Next() 正向迭代
* Prev() 反向迭代
* Seek() 查找固定值
* key() 返回当前的Key 
* value() 返回当前的Value 
* SeekToLast() 回到尾部
* SeekToFirst() 回到头部

这个设计的思路很明显，我们可以认为一个存储引擎是一个超级大的KV存储，既然是一个KV存储并且有序，那么可以在上面做的事情就不少了，比如可以在上面实现聚合，统计某个Key范围下的所有的值的和类似的这种聚合操作，当然还有数据库常用的火山模型其实就是迭代器的来回包装搞定的。

LevelDB如何保证Key是有序的呢？是通过Comparetor接口实现的。这个完全可以交给用户实现，如果交给用户实现，那么参考TiDB和TiKV之间的通信协议，完全可以在这个KV存储上构建具有表空间的关系型数据库了，这里就不展开了。

## 读写抽象

LevelDB既然是一个存储引擎，那么重要的工作就是对文件的操作了，这里LevelDB对文件操作做了三个抽象

* SequentialFile
* RandomAccessFile
* WritableFile 

也就把读写抽象的完完整整了，因为LevelDB是专门转换随机写到顺序写的，所以自然不存在随即写的抽象，那么顺序读和随机读两种抽象足够完成我们的需求了，写文件自然是Append就够了。

这一点是非常值得学习的，自己封装一堆读写操作utils来说是非常简单的。

## RAII 应用

直接贴代码吧，在使用锁这里，完全靠作用域的变量管理来搞定。

```cpp
class SCOPED_LOCKABLE MutexLock {
 public:
  explicit MutexLock(port::Mutex* mu) EXCLUSIVE_LOCK_FUNCTION(mu) : mu_(mu) {
    this->mu_->Lock();
  }
  ~MutexLock() UNLOCK_FUNCTION() { this->mu_->Unlock(); }

  MutexLock(const MutexLock&) = delete;
  MutexLock& operator=(const MutexLock&) = delete;

 private:
  port::Mutex* const mu_;
};
```

## 总结

LevelDB是一个非常值得读一读的项目，虽然RocksDB出来后，提供了很多新的Feature，LevelDB也没有用很多c++中的骚操作，但是在一个系统设计上有很多值得思考的点的。

