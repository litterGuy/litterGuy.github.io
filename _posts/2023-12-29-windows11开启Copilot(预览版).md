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


## 系统更新后，图标消失

1. 修改注册表

Win+R打开"运行"对话框，输入“regedit”，打开注册表编辑器，找到路径：

```
\HKEY_CURRENT_USER\Software\Microsoft\Windows\Shell\Copilot\BingChat\IsUserEligible
```

双击编辑键值，将数值数据由默认的“0”修改为“1”，点击“确定”保存

**注意**: 在“BingChat”文件夹处单击右键，选择“导出”， 命名为“Copilot恢复显示”并保存为reg注册表文件，方便下次再重启消失时（推测之前消失也是Edge检测到了系统位置的缘故）双击直接导入注册表使用。

2. 修改任务栏设置

修改任务栏设置，以启用Copilot任务栏项。任务栏处单击右键，选择“任务栏设置”，然后你会看到Copilot选项已经是开启状态，但是任务栏仍然看不到，此时别着急，重新点击一下，再次打开，就可以在任务栏看到Copilot图标了。

重新点击开关

[1]: /images/clashx-tun.png