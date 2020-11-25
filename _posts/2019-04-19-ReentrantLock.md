---
layout: post
title:  "ReentrantLock"
date:   2019-04-25 14:12:02
categories: 
   - JDK
tags:
   - JDK
   - 多线程
   - 锁
---

使用 synchronized 来做同步处理时，锁的获取和释放都是隐式的，实现的原理是通过编译后加上不同的机器指令来实现。

而 ReentrantLock 就是一个普通的类，它是基于 AQS(AbstractQueuedSynchronizer)来实现的。

是一个重入锁：一个线程获得了锁之后仍然可以反复的加锁，不会出现自己阻塞自己的情况。

> AQS 是 Java 并发包里实现锁、同步的一个重要的基础框架。

**锁类型**

ReentrantLock 分为公平锁和非公平锁，可以通过构造方法来指定具体类型：
```
    //默认非公平锁
    public ReentrantLock() {
        sync = new NonfairSync();
    }
    //公平锁
    public ReentrantLock(boolean fair) {
        sync = fair ? new FairSync() : new NonfairSync();
    }
```    
    
默认一般使用非公平锁，它的效率和吞吐量都比公平锁高的多(后面会分析具体原因)。

- 获取锁
通常的使用方式如下:
```
    private ReentrantLock lock = new ReentrantLock();
    public void run() {
        lock.lock();
        try {
            //do bussiness
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
```
    
由于公平锁需要关心队列的情况，得按照队列里的先后顺序来获取锁(会造成大量的线程上下文切换)，而非公平锁则没有这个限制。

所以也就能解释非公平锁的效率会被公平锁更高。

