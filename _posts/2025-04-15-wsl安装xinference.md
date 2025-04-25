---
layout: post
title:  "wsl安装xinference"
date:   2025-04-15 15:31:56
categories: 
   - linux
tags:
   - linux
---

# wsl安装xinference

##  安装
```
pip install "xinference[all]"
```

### 报错
1. no module torch
https://pytorch.org/ 去官方下载
```
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu126
```
2. Building wheel for llama-cpp-python (pyproject.toml) ... error
```
# 添加环境变量（临时，永久的话加到~/.bashrc里）
export LD_LIBRARY_PATH=/usr/lib/x86_64-linux-gnu:$LD_LIBRARY_PATH
pip install llama-cpp-python
```
3. wsl端口映射不出来
```
netsh interface portproxy add v4tov4 listenaddress=0.0.0.0 listenport=9997 connectaddress=127.0.0.1 connectport=9997
```

移除和查看
```
netsh interface portproxy delete v4tov4 listenaddress=0.0.0.0 listenport=9997
netsh interface portproxy show all
```

### 注意
Portproxy 依赖 Windows 的 iphlpsvc 服务。
运行以下命令确保它正在运行：
```
Get-Service iphlpsvc
```
如果不是 Running 状态，使用：
```
Start-Service iphlpsvc
```

4. RuntimeError: Encoder not found for codec: mp3

orchaudio 升级到 2.5.0 或更新的版本后，老的 ffmpeg 似乎不支持了。可以用 conda 安装：

```
conda install -c conda-forge "ffmpeg<7"
```

## 启动
```
xinference-local --host 0.0.0.0 --port 9997
```

可以通过访问 http://127.0.0.1:9997/ui 来使用 UI，访问 http://127.0.0.1:9997/docs 来查看 API 文档。


## [oauth2 系统](https://inference.readthedocs.io/zh-cn/latest/user_guide/auth_system.html)

设置一个json文件，用于配置认证信息
```
{
    "auth_config": {
        "algorithm": "HS256",
        "secret_key": "09d25e094faa6ca2556c818166b7a9563b93f7099f6f0f4caa6cf63b88e8d3e7",
        "token_expire_in_minutes": 30
    },
    "user_config": [
        {
            "username": "user1",
            "password": "secret1",
            "permissions": [
                "admin"
            ],
            "api_keys": [
                "sk-72tkvudyGLPMi",
                "sk-ZOTLIY4gt9w11"
            ]
        },
        {
            "username": "user2",
            "password": "secret2",
            "permissions": [
                "models:list",
                "models:read"
            ],
            "api_keys": [
                "sk-35tkasdyGLYMy",
                "sk-ALTbgl6ut981w"
            ]
        }
    ]
}
```

1. auth_config: 这个字段配置与安全相关的信息。
>algorithm: 用于令牌生成与解析的算法。推荐使用 HS 系列算法，例如 HS256，HS384 或者 HS512 算法。
>
>secret_key: 用于令牌生成和解析的密钥。可以使用该命令生成适配 HS 系列算法的密钥：openssl rand -hex 32 。
>
>token_expire_in_minutes: 保留字段，表示令牌失效时间。目前 Xinference 开源版本不会检查令牌过期时间。

2. user_config: 这个字段用来配置用户和权限信息。每个用户信息由以下字段组成：

>username: 字符串，表示用户名
>
>password: 字符串，表示密码
>
>permissions: 字符串列表，表示该用户拥有的权限。权限描述如上权限部分文档所述。
>
>api_keys: 字符串列表，表示该用户拥有的 api-key 。用户可以通过这些 api-key ，无需登录步骤即可访问 xinference 接口。这里的 api_key 组成与 OPENAI_API_KEY 相似，总是以 sk- 开头，后跟 13 个数字、大小写字母。

3.配置好这样一个 JSON 文件后，可以使用 --auth-config 选项启用具有身份验证和授权系统的 Xinference。例如，本地启动的命令如下所示：
```
xinference-local -H 0.0.0.0 --auth-config /path/to/your_json_config_file
```