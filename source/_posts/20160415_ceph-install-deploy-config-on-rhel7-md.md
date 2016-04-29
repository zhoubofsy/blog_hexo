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
* 数据与日志分开，日志存放在SSD硬盘中

## 方法

### 环境准备
#### 创建并使用ceph用户
* 创建ceph专属用户——ceph
    ```shell
    useradd -Um -d /home/ceph ceph
    passwd ceph
    echo "ceph  ALL=(ALL)   ALL" >> /etc/sudoers
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
#### SSH无密码访问
* 各个物理节点之间，建立SSH无密码访问

#### 集群搭建
* 按官网配置方式配置ceph集群（确保“/var/lib/ceph/”目录下的owner为ceph用户）
详细请见：http://docs.ceph.com/docs/infernalis/install/manual-deployment/
在搭建OSD时，不建议使用short form，建议使用long form；
* 如果集群名称不是默认名称（默认名称为：ceph），需要修改/etc/sysconfig/ceph，增加 CLUSTER="新集群名称"(详细请见，参考5)
* 根据集群情况结合本机需要启动的服务，在“/etc/sysconfig/ceph”中增加 MON_ID="MON的ID" 或 OSD_ID="OSD的ID"(详细请见，参考5)
* 修改/etc/fstab，增加需要挂载的磁盘信息
* 修改systemd中ceph服务为enable，保证开机启动
```shell
systemctl enable ceph-mon@{MON ID}
systemctl enable ceph-osd@{OSD ID}
```
大括号中的内容需要根据实际情况填写。


至此基本ceph集群已搭建完成
---
一个华丽的分割之后开始漫长的优化之路

# 优化
Todo...

# 参考&鸣谢
1. [打造高性能高可靠块存储系统](https://www.ustack.com/blog/build-block-storage-service/)
2. [Can't start ceph service](http://stackoverflow.com/questions/32397704/cant-start-ceph-service)
3. [linux命令详解之useradd命令使用方法](http://www.jb51.net/article/45848.htm)
4. [systemd:how do I view journals with journalctl without switching to root on CentOS7?](http://serverfault.com/questions/681632/systemd-how-do-i-view-journals-with-journalctl-without-switching-to-root-on-cen)
5. [Mail Re:Ceph on Fedora](http://permalink.gmane.org/gmane.comp.file-systems.ceph.devel/19988)
