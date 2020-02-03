---
title: STL 源码完全注释——Allocator
date: 2019-11-10 23:24:30
tags:
- C++
- STL 
---

SIG STL V3.3 版本的 Allocator的完全注释

<!--more-->

Allocator 分为三部分，分别是 

* stl_construct.h  构造销毁（主要是逻辑级别的，构造函数和析构函数）
* stl_alloc.h 具体的分配逻辑 （物理级别的空间管理）
* stl_uninitialized.h 全局填充函数

# stl_construct.h 

```cpp

#ifndef __SGI_STL_INTERNAL_CONSTRUCT_H
#define __SGI_STL_INTERNAL_CONSTRUCT_H

#include <new.h>

__STL_BEGIN_NAMESPACE // namespace std{

// 拷贝构造
template <class _T1, class _T2>
inline void _Construct(_T1* __p, const _T2& __value) {
  new ((void*) __p) _T1(__value);
}

// 默认初始化构造
template <class _T1>
inline void _Construct(_T1* __p) {
  new ((void*) __p) _T1();
}

// 销毁
template <class _Tp>
inline void _Destroy(_Tp* __pointer) {
  __pointer->~_Tp();
}

//内部迭代器销毁逻辑 具有 non-trivial 的析构函数
template <class _ForwardIterator>
void
__destroy_aux(_ForwardIterator __first, _ForwardIterator __last, __false_type)
{
  for ( ; __first != __last; ++__first)
    destroy(&*__first);
}

// 内部迭代器销毁 具有traivial析构函数 比如基础类型，这个函数可以加速销毁，不必要调用析构函数
template <class _ForwardIterator> 
inline void __destroy_aux(_ForwardIterator, _ForwardIterator, __true_type) {}

// 迭代器销毁函数
template <class _ForwardIterator, class _Tp>
inline void 
__destroy(_ForwardIterator __first, _ForwardIterator __last, _Tp*)
{
  typedef typename __type_traits<_Tp>::has_trivial_destructor _Trivial_destructor; // 判断是否有 non-trivial 析构函数
  __destroy_aux(__first, __last, _Trivial_destructor()); // 调用内部的销毁逻辑 __destroy_aux
}

// 用于适配 STL标准
template <class _ForwardIterator>
inline void _Destroy(_ForwardIterator __first, _ForwardIterator __last) {
  __destroy(__first, __last, __VALUE_TYPE(__first));
}

// 内置类型的销毁（非迭代器类型）
inline void _Destroy(char*, char*) {}
inline void _Destroy(int*, int*) {}
inline void _Destroy(long*, long*) {}
inline void _Destroy(float*, float*) {}
inline void _Destroy(double*, double*) {}
#ifdef __STL_HAS_WCHAR_T
inline void _Destroy(wchar_t*, wchar_t*) {}
#endif /* __STL_HAS_WCHAR_T */

// 下面是用来支援 HP 标准的STL函数
template <class _T1, class _T2>
inline void construct(_T1* __p, const _T2& __value) {
  _Construct(__p, __value);
}

template <class _T1>
inline void construct(_T1* __p) {
  _Construct(__p);
}

template <class _Tp>
inline void destroy(_Tp* __pointer) {
  _Destroy(__pointer);
}

template <class _ForwardIterator>
inline void destroy(_ForwardIterator __first, _ForwardIterator __last) {
  _Destroy(__first, __last);
}

__STL_END_NAMESPACE // }

#endif /* __SGI_STL_INTERNAL_CONSTRUCT_H */

```

# stl_alloc.h

这个头文件有点麻烦了哦，实际代码估计有六百行左右，首先明确分析的方向。

C++已经支持了多线程，那么有可能会在多线程中进行内存申请释放，所以的话，自然会有一堆多线程的feature来支持。

对于对象的分配，为了避免内存碎片，目测会有一些内存池优化的技巧，而且会超过一个阀值就会触发内存池分配。

所以分成三个部分来思考这个头文件

## 一些条件编译

```cpp
#ifdef __SUNPRO_CC // sunpro c++ 编译器 
#  define __PRIVATE public
#else
#  define __PRIVATE private
#endif

#ifdef __STL_STATIC_TEMPLATE_MEMBER_BUG // 不支持静态成员变量，使用malloc ，这个feature基本对于服务端编程来说没啥用了
#  define __USE_MALLOC
#endif


#ifndef __THROW_BAD_ALLOC // 错误是否会抛出异常，
#  if defined(__STL_NO_BAD_ALLOC) || !defined(__STL_USE_EXCEPTIONS)
#    include <stdio.h>
#    include <stdlib.h>
#    define __THROW_BAD_ALLOC fprintf(stderr, "out of memory\n"); exit(1) // 不支持直接打印异常流
#  else 
#    include <new>
#    define __THROW_BAD_ALLOC throw std::bad_alloc() // 引入C++ 标准的错误异常
#  endif
#endif

#include <stddef.h>
#include <stdlib.h>
#include <string.h>
#include <assert.h>
#ifndef __RESTRICT
#  define __RESTRICT
#endif

#ifdef __STL_THREADS
# include <stl_threads.h>
# define __NODE_ALLOCATOR_THREADS true // 开启线程内存分配的feature
# ifdef __STL_SGI_THREADS

    extern "C" {
      extern int __us_rsthread_malloc;
    }

#   define __NODE_ALLOCATOR_LOCK if (threads && __us_rsthread_malloc) \
                { _S_node_allocator_lock._M_acquire_lock(); }
#   define __NODE_ALLOCATOR_UNLOCK if (threads && __us_rsthread_malloc) \
                { _S_node_allocator_lock._M_release_lock(); }
# else 
#   define __NODE_ALLOCATOR_LOCK \
        { if (threads) _S_node_allocator_lock._M_acquire_lock(); }
#   define __NODE_ALLOCATOR_UNLOCK \
        { if (threads) _S_node_allocator_lock._M_release_lock(); }
# endif
#else
// 不支持线程，那就天生线程安全，不用引入线程处理的 feature了
#   define __NODE_ALLOCATOR_LOCK
#   define __NODE_ALLOCATOR_UNLOCK
#   define __NODE_ALLOCATOR_THREADS false
#endif
```

## class __malloc_alloc_template 

```cpp
template <int __inst> 
/**
 * 类型标记，如何使用类型标记呢？
 * 可以在 debug模式设置为0 release 模式设置为1 
 * 这样，类的具体化行为是一致的，但是可以根据不同的版本来实现不同的逻辑
 */
class __malloc_alloc_template {

private:

  static void* _S_oom_malloc(size_t); // oom 情况下的分配函数
  static void* _S_oom_realloc(void*, size_t); // oom 情况下的 realloc分配逻辑

#ifndef __STL_STATIC_TEMPLATE_MEMBER_BUG
  static void (* __malloc_alloc_oom_handler)(); // 来代替全局函数的
#endif

public:

  // malloc 分配失败 oom分配开始
  static void* allocate(size_t __n)
  {
    void* __result = malloc(__n);
    if (0 == __result) __result = _S_oom_malloc(__n);
    return __result;
  }

  // 直接调用 free
  static void deallocate(void* __p, size_t /* __n */)
  {
    free(__p);
  }

  // 同 malloc 解释
  static void* reallocate(void* __p, size_t /* old_sz */, size_t __new_sz)
  {
    void* __result = realloc(__p, __new_sz);
    if (0 == __result) __result = _S_oom_realloc(__p, __new_sz);
    return __result;
  }

  // 设置用户自定义的oom分配处理逻辑
  static void (* __set_malloc_handler(void (*__f)()))()
  {
    void (* __old)() = __malloc_alloc_oom_handler;
    __malloc_alloc_oom_handler = __f;
    return(__old);
  }

};

// malloc_alloc out-of-memory handling

#ifndef __STL_STATIC_TEMPLATE_MEMBER_BUG
template <int __inst>
void (* __malloc_alloc_template<__inst>::__malloc_alloc_oom_handler)() = 0; // 默认实现为空，这样默认的情况下直接抛异常了要
#endif

template <int __inst>
void*
__malloc_alloc_template<__inst>::_S_oom_malloc(size_t __n)
{
    void (* __my_malloc_handler)();
    void* __result;

    for (;;) {
        __my_malloc_handler = __malloc_alloc_oom_handler;
        if (0 == __my_malloc_handler) { __THROW_BAD_ALLOC; } // 未定义抛异常
        (*__my_malloc_handler)(); // 处理oom的逻辑，可能这个函数用来做系统内存的 swap-mark 之类的工作，清理内存
        __result = malloc(__n);
        if (__result) return(__result);
    }
}

template <int __inst>
void* __malloc_alloc_template<__inst>::_S_oom_realloc(void* __p, size_t __n)
{
    void (* __my_malloc_handler)();
    void* __result;

    for (;;) {
        __my_malloc_handler = __malloc_alloc_oom_handler;
        if (0 == __my_malloc_handler) { __THROW_BAD_ALLOC; }
        (*__my_malloc_handler)();
        __result = realloc(__p, __n);
        if (__result) return(__result);
    }
}

typedef __malloc_alloc_template<0> malloc_alloc; // 这里就是 _inst 的作用，直接定义一个relase版本的malloc_alloc 版本 

```

## class simple_alloc

没啥好说的，直接看源码就好了！

```cpp
template<class _Tp, class _Alloc>
class simple_alloc {

public:
    static _Tp* allocate(size_t __n)
      { return 0 == __n ? 0 : (_Tp*) _Alloc::allocate(__n * sizeof (_Tp)); }
    static _Tp* allocate(void)
      { return (_Tp*) _Alloc::allocate(sizeof (_Tp)); }
    static void deallocate(_Tp* __p, size_t __n)
      { if (0 != __n) _Alloc::deallocate(__p, __n * sizeof (_Tp)); }
    static void deallocate(_Tp* __p)
      { _Alloc::deallocate(__p, sizeof (_Tp)); }
};
```



