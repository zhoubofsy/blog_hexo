---
title: "git使用配置"
date: 2016-04-14 11:28:09
tags: git
---

## git配置

配置文件 ”~/.gitconfig“

```text
[user]
    email = zhoubofsy@hotmail.com
    name = zhoubofsy
[alias]
    lg = log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
[http]
    proxy = http://127.0.0.1:8087
    sslverify = false
[https]
    proxy = http://127.0.0.1:8087
```

