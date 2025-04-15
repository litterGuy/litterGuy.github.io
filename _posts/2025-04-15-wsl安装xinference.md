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

## 启动
```
xinference-local --host 0.0.0.0 --port 9997
```

可以通过访问 http://127.0.0.1:9997/ui 来使用 UI，访问 http://127.0.0.1:9997/docs 来查看 API 文档。