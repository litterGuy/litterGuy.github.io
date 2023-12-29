---
layout: post
title:  "windows11开启Copilot(预览版)"
date:   2023-12-29 15:41:33
categories: 
   - windows
tags:
   - windows
---

# windows11开启Copilot(预览版)

## Windows Copilot 是什么

Microsoft Copilot 将大型语言模型 (LLM) 的强大功能与您组织的数据相结合——一切都在工作流程中——将您的语言变成地球上最强大的生产力工具之一。它可与流行的 Microsoft 365 应用程序（例如 Word、Excel、PowerPoint、Outlook、Teams 等）配合使用。 Copilot提供实时智能帮助，使用户能够增强创造力、生产力和技能。

省流版就是 Windows 系统级别的智能助手，在屏幕右侧开启

## 步骤

1. 升级 Windows 11 至 23H2
把系统升级到最新即可.
2. 下载Vive
到github下载版本包[https://github.com/thebookisclosed/ViVe](https://github.com/thebookisclosed/ViVe)
3. 打开cmd
- 解压包
- "以管理员身份运行"打开cmd命令行
- 跳转到ViVe的解压目录
4. 执行
```
vivetool /enable /id:44774629,44776738,44850061,42105254,41655236
```
5. 重启电脑
6. 打开设置 – 个性化 – 任务栏 – 启用Copilot(预览版)

## 问题
打开copilot后，可能碰到以下错误
```
该服务在你所在的地区不可用
```

需要做两步操作：
1. 开启TUN模式
![1][1]
2. 浏览器访问[https://www.bing.com/](https://www.bing.com/)时，看到的是新版bing


[1]: /images/clashx-tun.png