---
layout: post
title:  "ConcurrentHashMap"
date:   2019-04-17 14:49:02
categories: 
   - JDK
tags:
   - JDK
---


 1. Base 1.7
> 
ConcurrentHashMap 采用了分段锁技术，其中 Segment 继承于 ReentrantLock。不会像 HashTable 那样不管是 put 还是 get 操作都需要做同步处理，理论上 ConcurrentHashMap 支持 CurrencyLevel (Segment 数组数量)的线程并发。每当一个线程占用锁访问一个 Segment 时，不会影响到其他的 Segment。

 2. Base 1.8
> 
抛弃了原有的 Segment 分段锁，而采用了 CAS + synchronized 来保证并发安全性。
同时将 1.7 中存放数据的 HashEntry 改为 Node
1.8 在 1.7 的数据结构上做了大的改动，采用红黑树之后可以保证查询效率（O(logn)），甚至取消了 ReentrantLock 改为了 synchronized，这样可以看出在新版的 JDK 中对 synchronized 优化是很到位的。