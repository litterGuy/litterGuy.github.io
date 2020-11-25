---
layout: post
title:  "Random String来打印出”Hello World”"
date:   2019-04-22 18:12:02
categories: 
   - JDK
tags:
   - JDK
---

```
public static String randomString(int i)
{
    Random ran = new Random(i);
    StringBuilder sb = new StringBuilder();
    while (true)
    {
        int k = ran.nextInt(27);
        if (k == 0)
            break;

        sb.append((char)('`' + k));
    }

    return sb.toString();
}
```

如果是一组选择好的随机数，那么事实上他们并不是真正随机的。随机数的算法是根据种子参数来进行计算的，（当前则是 -229985452 或者 -147909649）。每一次当申请一个随机数的时候，它会根据相同的随机数种子来生成一个相同的值 – 打印出”hello world”。

    Random(-229985452).nextInt(27)

前六个随机数是：8，5，12，12，15，0.

    Random(-147909649).nextInt(27)

前六个随机数是:23，15，18，12，4，0

当你将这些数字转换成字符的时候

104 –> h
101 –> e
108 –> l
108 –> l
111 –> o

119 –> w
111 –> o
114 –> r
108 –> l
100 –> d

你将获得hello world


