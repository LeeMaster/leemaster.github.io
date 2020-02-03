---
title: 时间轮算法
date: 2020-01-04 15:12:31
categories:
- Algorithm Impl
tags:
- Algorithm
---

最近对我司分布式任务调度系统有点好奇，OA上敲了一波相关团队，用时间轮实现的，正好周末就学习和稍微实现一下。

<!--more-->

# 分布式调度

分布式调度，应该也是一个常用的中间件了，主要用来调度一些延时任务，比如我们经常用公司内的调度工具来调度一些小型的ETL，还是蛮实用的。

## 线程池

在日常的开发中，经常的用到线程池，线程池的主要工作是用来将同步转换成异步任务，线程池的抽象基本可以分为三部分：

* 任务队列
* 工作线程
* 调度线程

任务队列可以认为是用来存储任务事件的，一个任务事件会被一个工作线程响应，而调度线程的主要工作是为这些任务事件排序的。在计算机整个体系中，其实很多的时候往往忽略了一个事情，我们整个计算机世界其实是由离散的事件构成的，离散的事件和事件响应函数组成了一个元组，通过不同的元组排列组合以后就形成了更复杂的模型。

### 实现

线程池的目标是用来将同步转换成异步的，那么在一瞬间会有很多的并发事件发生，那么这些并发事件需要被调度线程来排序存入任务队列，实现上可以认为主线程是一个调度线程，排序的方法也很简单，两个并发的事件，谁先拿到互斥量的锁，那么就排在了另外一个事件前。

需要注意的地方：

* 任务队列为空的时候，工作线程可能会空转，所以用条件变量避免线程空转
* 用户提交的任务多种多样，所以需要用模板来实现支持不同的函数
* 直接用future返回，因为不知道什么时候这个任务会完成

```c++
#pragma once 

#include <queue>
#include <thread>
#include <vector>
#include <functional>
#include <future>
#include <mutex>
#include <conditional_variable>

class ThreadPool{
  using Task = std::function<void()>;
  private:
    std::queue<Task> task_queue_;
    std::mutex mutex_;
    std::vector<std::thread> workers_;

  public:
    ThreadPool();
    ~ThreadPool();
    template<typename F, class...Args>
    auto Submit(F&& function, Args&&... args) -> std::future<decltype(function(args))>{
      using ReturnType = decltype(function(args);

    }

    inline void Shutdown(bool force){

    }
};
```

## 时间轮


## 参考

* Modeling and Analysis of Thread-Pools in an Industrial Communication Platform
* Hashed and Hierarchical Timing Wheels Effcient Data Structures for Implementing a Timer Facility