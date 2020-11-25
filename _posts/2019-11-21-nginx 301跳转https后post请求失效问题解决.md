---
layout: post
title:  "nginx 301跳转https后post请求失效问题解决"
date:   2019-11-21 13:27:02
categories: 
   - nginx
tags:
   - nginx
---

app本地请求是http端口,后来升级https强制301跳转,设置如下
```
server {
    listen 80;
    server name www.XXX.com;
    rewrite ^/(.*)$   https://www.XXX.com/$1 permanent;
}
```

**问题描述及原因
强制把http请求跳转到https,结果发现App有部分的功能不能使用,因为App一共设置了4种请求方式,分别是GET,POST,DELETE和OPTIONS方式,设置301跳转后所有的请求方法都变成了GET方式,导致一些功能无法正常使用.**

解决思路也很简单,所有GET请求直接301跳转不用管,非GET请求的用proxy_pass来转发,将参数传递给服务,nginx配置如下.

```
server {
    listen 80;
    server_name  www.XXX.com;
    location / {
        if ($request_method ~ ^(POST|DELETE|OPTIONS)$) {
            proxy_pass https://www.XXX.com;
            break ;
        }
        rewrite ^/(.*)$   https://www.XXX.com/$1 permanent;
    }
 
}
```




