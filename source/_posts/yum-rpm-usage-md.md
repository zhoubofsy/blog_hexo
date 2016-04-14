---
title: "yum 和 rpm 使用方法总结"
date: 2016-04-13 16:42:43
tags: tools
---


## yum 
---
### 安装
```shell
yum install {软件名称}
```

### 查询
```shell
yum search {关键字s}
```

### 下载(不安装)
```shell
yum install --downloadonly {需要下载的软件名，可多个} --downloaddir={下载位置，目录名}
```

### 按依赖关系安装本地rpm包
```shell
yum localinstall *.rpm
```


## rpm
---
* 每个rpm包上记录着它所依赖的rpm包

### 安装
```shell
rpm -ivh {xxx.rpm}
```

### 卸载
```shell
rpm -e --nodeps {软件名称}
```
* 卸载软件包，但不卸载依赖包

### 解压
```shell
rpm2cpio {xxx.rpm} | cpio -div
```

### 查看包依赖关系
```shell
rpm -qpR {软件名称}
```


## 参考&鸣谢
---
* [如何使用yum来下载RPM包而不进行安装](https://linux.cn/article-5100-1.html)
* [实用小命令:解压RPM](http://www.linuxeden.com/html/softuse/20090703/66563.html)
