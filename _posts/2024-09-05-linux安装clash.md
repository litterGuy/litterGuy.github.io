---
layout: post
title:  "linux 安装clash"
date:   2024-09-05 17:39:40
categories: 
   - linux
tags:
   - linux
---

# linux 安装clash

1. 创建文件夹
```
mkdir -p /home/clash
mkdir -p /home/clash/ui
cd /home/clash
```
2. 下载
```
wget https://github.com/DustinWin/clash_singbox-tools/releases/download/Clash-Premium/clashpremium-nightly-linux-amd64.tar.gz
```
3. 解压并重命名
```
tar -zxvf clashpremium-nightly-linux-amd64.tar.gz
mv CrashCore clash
```
4. 启动生成配置文件
```
./clash
```
5. 配置文件修改
```
cd ~/.config/clash/
vi config.yaml
```
配置文件的内容主要增加
```
# 控制面板端口
external-controller: 0.0.0.0:9091
# 控制面板路径
external-ui: /home/download/clash-permium/ui

proxy-providers:
  provider1:
    type: http
    url: "这里填写订阅的地址"
    interval: 3600
    path: ./subscribe.yaml
    health-check:
      enable: true
      interval: 600
      url: http://www.gstatic.com/generate_204
```
6. 下载ui
```
cd /home/clash/ui
wget https://cdn.jsdelivr.net/gh/DustinWin/clash_singbox-tools@Dashboard/yacd.tar.gz
tar zxvf yacd.tar.gz
```
7. 重启 clash
```
./clash
```
8. 访问控制面板
```
http://ip:9091/ui
```

## 后续
可将clash设置为系统服务，方便使用