---
layout: post
title:  "ubuntu安装homebrew"
date:   2025-04-12 16:00:44
categories: 
   - linux
tags:
   - linux
---

# ubuntu安装homebrew

##  安装依赖(如果没有)
```
sudo apt update
sudo apt install build-essential procps curl file git
```

##  运行官方安装脚本
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

## 配置环境变量
```
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> ~/.bashrc
eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"
```

## 测试
```
brew doctor
```

## 国内环境安装

国内环境访问不到raw.githubusercontent.com，所以要额外做些处理。

## 单独下载install.sh文件

## 设置镜像源
```
export HOMEBREW_BREW_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git"
export HOMEBREW_CORE_GIT_REMOTE="https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git"
export HOMEBREW_BOTTLE_DOMAIN="https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles"
```

## 执行安装
```
bash install.sh
```

后续跟正常步骤一样，设置环境变量、测试