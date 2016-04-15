---
title: "在RHEL7.2平台上搭建ceph集群"
date: 2016-04-15 09:04:40
tags: ceph
---

# 背景
* OS版本，RHEL 7.2 
* Ceph版本，Infernalis

# 安装
* 使用本地rpm包安装ceph及ceph依赖的软件包
```shell
cd ./cls-PKGs
yum localinstall *.rpm
```

# 部署

## 方案

### MON节点
* MON节点与OSD节点比例为1/4
* MON节点分布在存储压力较小的主机中
* 两两节点不能分布在一台主机中
* MON节点使用万兆网络

### OSD节点
* 一个硬盘对应一个OSD
* OSD节点使用万兆网络

## 方法

### 环境准备
#### 创建并使用ceph用户
* 创建ceph专属用户——ceph
    ```shell
    useradd -Um -d /home/ceph ceph
    passwd ceph
    echo "ceph  ALL=(ALL)   ALL" >> /etc/sudoer
    su - ceph
    ```
    1. 创建ceph用户和ceph用户目录
    2. 修改ceph用户密码
    3. 修改ceph用户sudoer列表
    4. 切换ceph用户
* 使用ceph用户配置环境
    1. 修改主机名称
    2. 修改hosts文件，对应ip地址与主机名
    3. 本地安装ceph rpm包
    ```shell
    sudo yum localinstall *.rpm
    ```
    4. 按官网配置方式配置ceph集群（**确保“/var/lib/ceph/”目录下的owner为ceph用户**）
    详细请见：http://docs.ceph.com/docs/infernalis/install/manual-deployment/
#### SSH无密码访问
* 各个物理节点之间，建立SSH无密码访问



至此基本ceph集群已搭建完成
---
一个华丽的分割之后开始漫长的优化之路

# 优化
Todo...

# 参考&鸣谢
* [打造高性能高可靠块存储系统](https://www.ustack.com/blog/build-block-storage-service/)

