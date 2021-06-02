---
layout: post
title:  "Red Hat下chia安装和启动"
date:   2021-06-02 08:26:27
categories: 
   - 节点
tags:
   - 节点
---

# 更新库，安装python

> 前几步按照官方提供命令，没有出现什么问题

```
sudo yum install epel-release -y
sudo yum update -y

# Compiling python 3.7 is generally required on CentOS 7.7 and newer
sudo yum install gcc openssl-devel bzip2-devel zlib-devel libffi libffi-devel -y
sudo yum install libsqlite3x-devel -y
# possible that on some RHEL based you also need to install
sudo yum groupinstall "Development Tools" -y
sudo yum install python3-devel gmp-devel  boost-devel libsodium-devel -y

sudo yum install wget -y
sudo wget https://www.python.org/ftp/python/3.7.7/Python-3.7.7.tgz
sudo tar -zxvf Python-3.7.7.tgz ; cd Python-3.7.7
./configure --enable-optimizations; sudo make -j$(nproc) altinstall; cd ..

# Download and install the source version
git clone https://github.com/Chia-Network/chia-blockchain.git -b latest
cd chia-blockchain
```

### 修改pip配置文件（没碰见问题可忽略）

> 因为碰见section 'global' already exists错误

修改/root/.pip/pip.conf文件
```
[global]
index-url=http://mirrors.aliyun.com/pypi/simple/
extra-index-url=
	https://pypi.tuna.tsinghua.edu.cn/simple

[install]
trusted-host=mirrors.aliyun.com
```

## 编译chia

```
sh install.sh
. ./activate
```

### 设置系统默认python（没碰见问题可忽略）

> 如上一步报错，类似：No matching distribution found for **==**
> 
> 可能为系统默认python不为python3.7需要修改

```
python -V //查看版本号
which python //确定使用的哪个
```

使用ln -s 创建软连,保证python python3.7都指向上几步安装的python3.7.7


## 生成钱包

```
chia keys generate
```

### 修改配置chia配置文件（没碰见问题可忽略）

> 启动钱包是如碰见以下错误 error while attempting to bind on address ('::1', 55400, 0, 0): cannot assign requested address

```
vim ~/.chia/mainnet/config/config.yaml

self_hostname: localhost
替换成
self_hostname: 127.0.0.1
```

## 启动钱包和节点

```
chia start wallet
```

## 查看同步情况

```
chia show -s
```

## 查看钱包
```
chia wallet show
```

第一次执行该命令会出现以下交互

```
No online backup file found, 
 Press S to skip restore from backup 
 Press F to use your own backup file:
```

> 根据需求选择
>
> 不执行该命令，钱包不会同步节点