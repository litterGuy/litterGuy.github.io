---
layout: post
title:  "minio上传SignatureDoesNotMatch错误"
date:   2023-12-21 08:19:17
categories: 
   - minio
   - go
tags:
   - minio
---

# minio上传SignatureDoesNotMatch错误

问题描述：

    js使用post进行上传，一切正常。

    但是后台使用put代码上传报错
```
The request signature we calculated does not match the signature you provided. Check your key and signing method 
```

开始以为是bucket权限设置有问题，重新创建bucket尝试后、依然存在该问题。

后续经过尝试，发现是nginx的配置有问题导致。
将
```
proxy_set_header host $host;
```
修改为
```
proxy_set_header Host $http_host;
```
解决问题。

参照下官方配置文档[Nginx服务器反向代理MinIO配置](https://minio.org.cn/docs/minio/linux/integrations/setup-nginx-proxy-with-minio.html)

## host、http_host和proxy_host区别

|变量|是否显示端口|值|
|---|---|---|
|$host|不显示端口|浏览器请求的ip，不显示端口|
|$http_host|端口存在则显示|浏览器请求的ip和端口号|
|$proxy_host|默认80端口不显示，其它显示|被代理服务的ip和端口号|