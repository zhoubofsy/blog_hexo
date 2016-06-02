---
title: "在RHEL7.2平台上搭建ceph集群"
date: 2016-04-15 09:04:40
tags: ceph
---

# 背景
ceph 分布式统一存储

## ceph 架构


## 版本
* OS版本，RHEL 7.2 
* Ceph版本，Infernalis

# 安装
* 使用本地rpm包安装ceph及ceph依赖的软件包
```shell
yum localinstall *.rpm
```

# 部署

## 方案

### 概述
```shell



                 +------------------------------------------------+
                 |                 Client Requests                |
                 +------------------------------------------------+
                         /\                             /\
              Admin Ops /||\                           /||\
                         ||                             ||
              ClusterMap ||                             ||  Data I/O
                        \||/                           \||/
                         \/                             \/
   +-----+       +----------------+            +------------------+
   |     |------>|  MON (Active)  | <------->  |                  |
   |     |       +----------------+            |                  |
   |     |                                     |                  |
   |     |       +----------------+            |                  |
   | NTP |------>|  MON (Standby) | <------->  |       OSDs       |
   |     |       +----------------+            |                  |
   |     |              .                      |                  |
   |     |              .                      |                  |
   |     |              .                      |                  |
   |     |       +----------------+            |                  |
   |     |------>|  MON (Standby) | <------->  |                  |
   +-----+       +----------------+            +------------------+




```
ceph集群包括两类节点，mon节点、OSD节点

### MON节点
* MON节点与OSD节点比例为1/4
* MON节点分布在存储压力较小的主机中
* 两两节点不能分布在一台主机中
* MON节点使用万兆网络
* MON节点通过NTP同步时钟

### OSD节点
```shell



   +------+  +-------------+        +-----------+          +-------+        +------------+
   |      |  |             |------> |    Data   | ------>  |  XFS  | ---->  | SAS Device |
   |      |  |             |        +-----------+          +-------+        +------------+
   |      |  |             |
   |      |  | OSD1 Daemon |
   | One  |  |             |        +-----------+          +-------+        +------------+
   |      |  |             |------> |  Journal  | -----+   |       |        |            |
   |      |  +-------------+        +-----------+      |   |       |        |            |
   | host |                                            +-> |       |        |            |
   |      |                                            |   |  XFS  | ---->  | SSD Device |
   |      |  +-------------+        +-----------+      |   |       |        |            |
   |      |  |             |------> |  Journal  | -----+   |       |        |            |
   |      |  |             |        +-----------+          +-------+        +------------+
   |      |  |             |
   |      |  | OSDn Daemon |
   |      |  |             |        +-----------+          +-------+        +------------+
   |      |  |             |------> |    Data   | ------>  |  XFS  | ---->  | SAS Device |
   +------+  +-------------+        +-----------+          +-------+        +------------+




```
* 一个硬盘对应一个OSD Daemon
* OSD节点使用万兆网络
* 数据与日志分开，日志存放在SSD硬盘中
数据写入先写入日志，再回写入数据，提高日志写入性能，可以提高性能
详细请见：`http://docs.ceph.com/docs/jewel/rados/configuration/osd-config-ref/`
* OSD使用XFS文件系统
生产环境使用XFS文件系统用于存储数据和日志
详细请见：`http://docs.ceph.com/docs/jewel/rados/configuration/filesystem-recommendations/`

### 网络
* public Network
采用万兆网，主要负责处理用户I/O请求
* Cluster Network
采用万兆网络， 主要负责拷贝数据副本和传递心跳信息

详细请见：`http://docs.ceph.com/docs/jewel/rados/configuration/network-config-ref/`

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
##### MON节点搭建步骤
1. fsid生成
2. 修改ceph.conf中的MON信息
3. 创建并倒入admin keyring
4. 创建MON数据目录
5. 初始化MON节点
6. 启动MON服务

##### OSD节点搭建步骤
1. 在集群中创建OSD
2. 创建OSD数据目录
3. 格式化硬盘文件系统为XFS
4. 挂载硬盘到数据目录
5. 初始化OSD节点
6. 创建OSD访问需要的keyring
7. 在Crushmap中添加OSD节点
8. 修改ceph.conf中的OSD息
9. 启动OSD服务

详细请见：http://docs.ceph.com/docs/jewel/install/manual-deployment/

**注意：**
* 按官网配置方式配置ceph集群（确保“/var/lib/ceph/”目录下的owner为ceph用户）
* 如果集群名称不是默认名称（默认名称为：ceph），需要修改/etc/sysconfig/ceph，增加 CLUSTER="新集群名称"(详细请见，参考5)
* 根据集群情况结合本机需要启动的服务，在“/etc/sysconfig/ceph”中增加 MON_ID="MON的ID" 或 OSD_ID="OSD的ID"(详细请见，参考5)
* 修改/etc/fstab，增加需要挂载的磁盘信息
* 修改systemd中ceph服务为enable，保证开机启动
```shell
systemctl enable ceph-mon@{MON ID}
systemctl enable ceph-osd@{OSD ID}
systemctl enable ceph-mon.target
systemctl enable ceph-osd.target
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
