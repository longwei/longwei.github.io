---
layout: post
title: C++ and Singleton
---

在单线程模式下，最简单的办法就是只能通过一个method获取instance，在第一次access的时候建立instance


```
class Singleton {
  public:
  static Singleton& instance() {
    static Singleton instance_;
    return instance_;
  }
}
```
但是这个不是thread-safe的，对于if (constructed == false) 是存在race-condition的
那怎么办？加锁咯


```
Singleton* Singleton::instance() {
  Lock lock;
  if (pInstance == 0) {
    pInstance = new Singleton;
  }
    return pInstance;
}
```

这个solution的问题是expensive. 每次access都需要acquire lock. 现实中，我们只需要在初始化的时候加锁。

著名的double checking locking pattern
```
Singleton* Singleton::instance() {
  if (pInstance == 0) {
    Lock lock
    if (pInstance == 0) {
      pInstance = new Singleton;
    }
  }
    return pInstance;
}
```

DCLP也不行啊，因为

Step 1: Allocate memory to hold a Singleton object.
Step 2: Construct a Singleton object in the allocated memory.
Step 3: Make pInstance point to the allocated memory.

这三步有可能不是顺序的，#2 #3compiler优化可能会swap，使得DCLP完全没用


C++11之后

```
If control enters the declaration concurrently while the variable is being initialized, the concurrent execution shall wait for completion of the initialization.
```
所以不用再overengineeering了

```
public：
  static Singleton& get(){
    static Singleton instance;
    return instance;
  }
  Singleton(Singleton const&)       = delete; //no copy & assigment constructor
  void operator=(Singleton const&)  = delete;
private: 
  Singleton() {}
```




