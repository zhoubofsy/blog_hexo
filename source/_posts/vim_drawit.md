title: drawit vim画图插件
date: 2015-12-19 09:30
tags: vim
---

# 下载
```bash
wget -O DrawIt.vba.gz http://www.vim.org/scripts/download_script.php?scr_id=8798
```

# 解压
```bash
vim DrawIt.vba.gz
```
```vim
:so %
:q
```

# 安装
将解压后的文件复制到~/.vim/的plug和doc目录下

# 使用

## 启动&退出DrawIt
```vim
\di     ;启动
\ds     ;关闭
```

## 画图
在vim的visual block（可视块）模式下（ctrl+v）,画矩形框、椭圆
```vim
\b      ;矩形框
\e      ;椭圆
```

## 箭头
```vim
^       ;上箭头
v       ;下箭头
>       ;右箭头
<       ;左箭头
```

## 斜线
PgUp 右上斜线
PgDn 右下斜线
End　左下斜线
Home 左上斜线

注：　“\” 为 <leader>
