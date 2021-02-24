---
layout: post
title:  "navicat oracle字符集问题"
date:   2021-02-24 11:20:53
categories: 
   - oracle
tags:
   - oracle
   - navicat
---

# navicat oracle字符集问题

修改oracle字符集为utf8后，navicat连接oracle失败并报了以下错误:

```
connection to server failed, probable Oracle Net admin error
```

查找原因发现如下解释：

> Oracle的客户端分为两种，一种是标准版，一种是简洁版
> 
> Navicat连接oracle使用的是自身安装时携带的简洁版客户端。
>
> 而简洁版不支持utf8字符集


故去官网下载标准版客户端,地址：https://www.oracle.com/database/technologies/instant-client/microsoft-windows-32-downloads.html

下载后解压，下一步准备替换navicat的客户端

1. Navicat中配置一下，选择工具-选项

![选项][01]

2. 选择驱动

![替换oci][02]

3. 重启Navicat生效

[01]: /images/oracle/oracle.png
[02]: /images/oracle/oracle1.png