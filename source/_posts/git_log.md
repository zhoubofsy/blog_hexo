title: git log 配置
date: 2015-11-25 20:35
tags: git
---

## git log 一般操作
```bash
git log         查看全部提交日志
git log -2      查看最近２次的提交日志
git log -p      查看历史记录以来那些行被修改
git log --stat --summary    查看每个版本变动的档案和行数
```

---
如果你厌倦了黑呼呼的界面，下面让你的git log色彩更丰富一些：
```bash
git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit
```

如果觉得觉得这么长的命令不好记，那么设个“别名"吧：
```bash
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

