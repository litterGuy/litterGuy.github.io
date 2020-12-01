---
layout: post
title:  "docker开启api"
date:   2020-12-01 13:54:14
categories: 
   - docker
tags:
   - docker
---

# Docker常见端口

常见docker端口包括：

> 2375：未加密的docker socket,远程root无密码访问主机
> 
> 2376：tls加密套接字,很可能这是您的CI服务器4243端口作为https 443端口的修改
> 
> 2377：群集模式套接字,适用于群集管理器,不适用于docker客户端
> 
> 5000：docker注册服务
> 
> 4789和7946：覆盖网络

# 开启配置

## 方法一
首先是怎么配置远程访问的API：

```
sudo vim /etc/default/docker
```

加入下面一行
```
DOCKER_OPTS="-H tcp://0.0.0.0:2375"
```

重启docker即可：
```
sudo systemctl restart docker
```

>PS:这是网上给的配置方法，也是这种简单配置让Docker Daemon把服务暴露在tcp的2375端口上，这样就可以在网络上操作Docker了。Docker本身没有身份认证的功能，只要网络上能访问到服务端口，就可以操作Docker。

## 方法二
在/usr/lib/systemd/system/docker.service，配置远程访问。

主要是在[Service]这个部分，加上下面两个参数
```
# vim /usr/lib/systemd/system/docker.service
[Service]
ExecStart=
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
```

重启
```
systemctl daemon-reload
systemctl restart docker
```

## 方法三
下面修改daemon.json的配置
```
vim /etc/docker/daemon.json

{
  "hosts": ["tcp://0.0.0.0:2375", "unix:///var/run/docker.sock"]
}
```

> "unix:///var/run/docker.sock"：unix socket，本地客户端将通过这个来连接 Docker Daemon。
"tcp://0.0.0.0:2375"：tcp socket，表示允许任何远程客户端通过 2375 端口连接 Docker Daemon。


修改配置以后

然后让docker重新读取配置文件,并重启docker服务
```
systemctl daemon-reload
systemctl restart docker
```
查看docker进程：
```
[root@slaver2 ~]# ps -ef|grep docker
root      44221      1  1 18:16 ?        00:00:06 /usr/bin/dockerd -H tcp://0.0.0.0:2375 -H unix://var/run/docker.sock
```
Docker守护进程打开一个HTTP Socket,这样才能实现远程通信

简单使用#
-H为连接目标主机docker服务

查看docker版本
```
[root@slaver2 /]# docker -H tcp://18.16.202.95:2375 version
Client: Docker Engine - Community
 Version:           19.03.0
 API version:       1.40
 Go version:        go1.12.5
 Git commit:        aeac9490dc
 Built:             Wed Jul 17 18:15:40 2019
 OS/Arch:           linux/amd64
 Experimental:      false
Cannot connect to the Docker daemon at tcp://18.16.202.95:2375. Is the docker daemon running?
```
查看镜像包：
```
[root@slaver2 ~]# docker -H tcp://18.16.202.95:2375 images
REPOSITORY                                 TAG                 IMAGE ID            CREATED             SIZE
zookeeper                                  3.5.5               3487af26dee9        4 weeks ago         225MB
k8s.gcr.io/kube-apiserver                  v1.15.1             68c3eb07bfc3        8 weeks ago         207MB
```