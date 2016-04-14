---
title: "linux 配置 http 全局代理"
date: 2016-04-14 11:21:25
tags: tools
---

## 背景

使用"XX-Net-2.9.2“做代理服务端（XX-Net-2.9.2在github上可以找到）

## Linux系统配置

```shell
export http_proxy=http://127.0.0.1:8087
export https_proxy=$http_proxy
export ftp_proxy=$http_proxy
export rsync_proxy=$http_proxy
```

