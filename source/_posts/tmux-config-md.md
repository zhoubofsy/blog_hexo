---
title: "tmux 配置"
date: 2016-04-14 11:05:29
tags: tools
---

## tmux 配置如下(~/.tmux.conf)：

```shell
# 状态栏

# 颜色
set -g status-bg black
set -g status-fg white

# 对齐
set-option -g status-justify centre

# 左下角
set-option -g status-left '#[bg=black,fg=green][#[fg=cyan]#S#[fg=green]]'
set-option -g status-left-length 20

# 窗口列表
set -g automatic-rename on
set-window-option -g window-status-format '#[dim]#I:#[default]#W#[fg=grey,dim]'
set-window-option -g window-status-current-format '#[fg=cyan,bold]#I:#W#[fg=blue]'

# 右下角
set -g status-right '#[fg=green][#[fg=cyan]%Y-%m-%d#[fg=green]]'

# 复制模式
set-window-option -g utf8 on
set-window-option -g mode-keys vi
```
