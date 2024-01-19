---
layout: post
title:  "pycharm debug模式开启失败"
date:   2024-01-19 13:59:08
categories: 
   - python
tags:
   - python
---

# pycharm debug模式开启失败
使用pycharm开启debug模式时，启动报错
```
Connection to Python debugger failed Timeout waiting for response on 501
```

解决办法为：
Help -> Find Action -> 搜索Registry

![1][1]

将
```
wsl.proxy.connect.localhost
```
勾选后 debug正常启动.

[1]: /images/python/1.png