---
layout: post
title:  "windows使用choco包管理工具"
date:   2025-01-27 08:25:45
categories: 
   - windows
tags:
   - windows
   - docker
---

# windows使用choco包管理工具

## 安装choco

1. 使用管理员方式打开powershell
2. 执行策略更改
```powershell
Set-ExecutionPolicy RemoteSigned
```
3. 安装choco
```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
```
4. 验证安装
```powershell
choco -v
```

## 安装docker

```
choco install docker-desktop --version=4.24.2
```

> 去这个站点查找对应的版本号[chocolatey](https://community.chocolatey.org/packages/docker-desktop/4.24.2)