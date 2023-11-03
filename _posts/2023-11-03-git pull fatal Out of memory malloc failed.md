---
layout: post
title:  "git pull fatal: Out of memory, malloc failed (tried to allocate 524288880 bytes)"
date:   2023-11-03 14:40:23
categories: 
   - git
tags:
   - git
---

# git pull fatal: Out of memory, malloc failed (tried to allocate 524288880 bytes)

git 拉取更新时，发现报错如下：

```
fatal: Out of memory, malloc failed (tried to allocate 524288880 bytes)
fatal: Could not read from remote repository .
please make sure you have the correct access rightsand the repository exists.
```

- 查看git配置。可能是'http.postbuffer'设置过大
```
git config --list --show-origin
```

- 修改'http.postbuffer'
```
git config --global  http.postbuffer 128m
```

- 重新执行命令