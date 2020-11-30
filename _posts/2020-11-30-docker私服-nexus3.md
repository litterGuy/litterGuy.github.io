---
layout: post
title:  "docker私服搭建-nexus3"
date:   2020-11-30 10:39:17
categories: 
   - docker
tags:
   - docker
---

# Nexus 3

使用 Docker 官方的 Registry 创建的仓库面临一些维护问题。比如某些镜像删除以后空间默认是不会回收的，需要一些命令去回收空间然后重启 Registry 程序。在企业中把内部的一些工具包放入 Nexus 中是比较常见的做法，最新版本 Nexus3.x 全面支持 Docker 的私有镜像。所以使用 Nexus3.x 一个软件来管理 Docker , Maven , Yum , PyPI 等是一个明智的选择。

# 启动 Nexus 容器

```
docker run -d --name nexus3 --restart=always -p 8081:8081 -p 8082:8082 --mount src=nexus-data,target=/nexus-data sonatype/nexus3
```

等待 3-5 分钟，如果 nexus3 容器没有异常退出，那么你可以使用浏览器打开 http://YourIP:8081 访问 Nexus 了。

第一次启动 Nexus 的默认帐号是 admin 密码是 admin123 登录以后点击页面上方的齿轮按钮进行设置。

# 创建仓库

>在Nexus中Docker仓库被分为了三种
>
>+ hosted： 托管仓库 ，私有仓库，可以push和pull 
>+ proxy： 代理和缓存远程仓库 ，只能pull
>+ group： 将多个proxy和hosted仓库添加到一个组，只访问一个组地址即可，只能pull

![nexus repository][01]

创建一个私有仓库的方法： Repository->Repositories 点击右边菜单 Create repository 选择 docker (hosted)
- Name: 仓库的名称
- HTTP: 仓库单独的访问端口
- Enable Docker V1 API: 如果需要同时支持 V1 版本请勾选此项（不建议勾选）。
Hosted -> Deployment pollcy: 请选择 Allow redeploy 否则无法上传 Docker 镜像。

其它的仓库创建方法请各位自己摸索，还可以创建一个 docker (proxy) 类型的仓库链接到 DockerHub 上。再创建一个 docker (group) 类型的仓库把刚才的 hosted 与 proxy 添加在一起。主机在访问的时候默认下载私有仓库中的镜像，如果没有将链接到 DockerHub 中下载并缓存到 Nexus 中。

# 添加访问权限

菜单 Security->Realms 把 Docker Bearer Token Realm 移到右边的框中保存。

添加用户规则：菜单 Security->Roles->Create role 在 Privlleges 选项搜索 docker 把相应的规则移动到右边的框中然后保存。

添加用户：菜单 Security->Users->Create local user 在 Roles 选项中选中刚才创建的规则移动到右边的窗口保存。

# Docker配置

配置daemon.json
其中192.168.59.1指安装Nexus的服务器地址

```
#vi /etc/docker/daemon.json
{
  "insecure-registries": [
    "192.168.59.1:8551",
    "192.168.59.1:8552",
    "192.168.59.1:8553"
  ],
  "disable-legacy-registry": true
}
```

- 重启Docker
- Push镜像
  
  ```
  docker tag hifreud 192.168.59.1:8551/freud:latest

  docker login -u admin -p admin123 192.168.59.1:8551

  docker push 192.168.59.1:8551/freud:latest
  ```

[01]: /images/docker/nexus01.png