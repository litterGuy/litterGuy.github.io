---
layout: post
title:  "oracle在docker下的安装配置"
date:   2021-02-24 10:54:41
categories: 
   - oracle
tags:
   - oracle
   - docker
---

# oracle在docker下的安装配置

## 安装

1. 拉取镜像：使用的是truevoly/oracle-12c的镜像

```
docker pull truevoly/oracle-12c
```

2. 启动
   
```
docker run -p 1521:1521 --name oracle_12c -d --restart=always truevoly/oracle-12c
```

3. 查看是否启动

```
docker ps -a
```

## 进入oracle

1. 通过以下命令进入容器

```
docker exec -it oracle_12c /bin/bash
```

2. oracle默认是安装在oracle用户下的
   
```
su oracle
```

3. 找到sqlplus位置

```
cd /u01/app/oracle/product/12.1.0/xe/
```

4. 登录
   
```
./sqlplus / as sysdba
```

## 修改dba密码

```
alter user sys identified by newpassword;
alter user system identified by newpassword;
alter profile default limit PASSWORD_LIFE_TIME UNLIMITED [设置密码时效，可以不管]
```

然后就可以通过dba帐号进行登陆了， 也可以使用工具进行登陆，默认SID :XE

## 创建用户授权

一般给别人不可能给DBA帐号，所以必须为其创建帐号

1. create user xxx identified by password;
2. 创建后你会发现你不能登陆，是因为没有授权，所以需要授权登陆权限：grant create session to xxx
3. 登陆后你发现你没法建表、也没有表目录（使用管理工具的看不到你建的表的列表）等，还是权限不够，所以继续授权
   
 ```
grant create table,unlimited tablespace to xxx;
grant select any table to xxx;
GRANT UPDATE ANY TABLE,DROP ANY TABLE,INSERT ANY TABLE TO xxx
 ```  