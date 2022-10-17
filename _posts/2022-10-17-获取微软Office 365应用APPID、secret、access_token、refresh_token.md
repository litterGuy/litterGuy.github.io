---
layout: post
title:  "获取微软Office 365应用APPID、secret、access_token、refresh_token"
date:   2022-10-17 10:03:46
categories: 
   - office365
tags:
   - office365
---

# 获取微软Office 365应用APPID、secret、access_token、refresh_token

## 获取APPID

1. 使用Office365订阅的管理员账号登陆Azure Portal
2. 点击页面 Azure Active Directory管理 → 左侧 应用注册 → 新注册
![查看][01]
![应用注册][02]
![新注册][03]
3. 根据需要填写相关信息
![注册][04]
4. 记录APPID

## 新建权限

1. 点击页面左侧 API权限 → 添加权限

![api权限][05]
![添加权限][06]

2. 选择所需的权限，如Graph下的所有ReadAll和ReadWrite
```
#先注册azure应用,确保应用有以下权限:
#files:	Files.Read.All、Files.ReadWrite.All、Sites.Read.All、Sites.ReadWrite.All
#user:	User.Read.All、User.ReadWrite.All、Directory.Read.All、Directory.ReadWrite.All
#mail:  Mail.Read、Mail.ReadWrite、MailboxSettings.Read、MailboxSettings.ReadWrite
#注册后一定要再点代表xxx授予管理员同意,否则outlook api无法调用
```
3. 点击 代表XX授予管理员权限 保存权限
![授权][07]


## 新建API密钥
1. 点击页面左侧 证书和密码 → 新客户端密码
![证书和密码][08]
2. 根据需要添加，记录secret

## 获取refresh_token

1. 在Azure控制台，添加身份验证 → 添加平台 → WEB，输入http://localhost:53682/
![身份验证][09]
2. 下载 [Rclone](https://rclone.org/downloads/)，解压
3. 在解压后目录下，按着shift点鼠标右键，选择在此处打开powershell窗口，弹出窗口后执行
```
.\rclone authorize "onedrive" "记录的APPID(client_id)" "记录的API密钥(secret)"
```
4. 在弹出的浏览器窗口登录账号
5. 回到powershell窗口，复制出一段json数据，取到access_token、refresh_token
![执行命令][10]
![获取token][11]

[01]: /images/officee5/1.png
[02]: /images/officee5/2.png
[03]: /images/officee5/3.png
[04]: /images/officee5/4.png
[05]: /images/officee5/5.png
[06]: /images/officee5/6.png
[07]: /images/officee5/7.png
[08]: /images/officee5/8.png
[09]: /images/officee5/9.png
[10]: /images/officee5/10.png
[11]: /images/officee5/11.png