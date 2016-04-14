---
title: "在RHEL7.2上下载Ceph离线安装包"
date: 2016-04-13 17:20:50
tags: plan
---

## Client PKGs

RHEL从7.1版本（包含7.1）将libceph.ko、ceph.ko、rbd.ko纳入到kernel中，所以如果选择7.1之前的版本需要自己编译相关ko文件


## Cluster PKGs

**步骤：**

#### 更换yum
1. 卸载RHEL yum包
```shell
rpm -qa | grep yum | xargs -e --nodeps
rpm -qa | grep python-iniparse | xargs -e --nodeps
```
2. 安装CentOS yum包
*下载yum包*
```shell
wget http://mirrors.163.com/centos/7.2.1511/os/x86_64/Packages/yum-3.4.3-132.el7.centos.0.1.noarch.rpm
wget http://mirrors.163.com/centos/7.2.1511/os/x86_64/Packages/python-iniparse-0.4-9.el7.noarch.rpm   
wget http://mirrors.163.com/centos/7.2.1511/os/x86_64/Packages/yum-metadata-parser-1.1.4-10.el7.x86_64.rpm 
wget http://mirrors.163.com/centos/7.2.1511/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.31-34.el7.noarch.rpm
```
*安装yum包*
```shell
rpm -ivh *.rpm
```

#### 修改repo
在“/etc/yum.repo.d/”目录下，增加文件rhel7.repo，内容如下。
```shell
[base]
name=CentOS-$releasever - Base
baseurl=http://mirrors.163.com/centos/7.2.1511/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/7.2.1511/os/x86_64/RPM-GPG-KEY-CentOS-7

#released updates
[updates]
name=CentOS-$releasever - Updates
baseurl=http://mirrors.163.com/centos/7.2.1511/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/7.2.1511/os/x86_64/RPM-GPG-KEY-CentOS-7

[extras]
name=CentOS-$releasever - Extras
baseurl=http://mirrors.163.com/centos/7.2.1511/extras//$basearch/
gpgcheck=1
gpgkey=http://mirrors.163.com/centos/7.2.1511/os/x86_64/RPM-GPG-KEY-CentOS-7

[centosplus]
name=CentOS-$releasever - Plus
baseurl=http://mirrors.163.com/centos/7.2.1511/centosplus//$basearch/
gpgcheck=1
enabled=0k
```
在“/etc/yum.repo.d/”目录下，增加文件ceph.repo，内容如下。
```shell
[ceph]
name=Ceph packages for $basearch
baseurl=http://ceph.com/rpm-infernalis/el7/$basearch
enabled=1
priority=2
gpgcheck=1
type=rpm-md
gpgkey=https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/release.asc

[ceph-noarch]
name=Ceph noarch packages
baseurl=http://ceph.com/rpm-infernalis/el7/noarch
enabled=1
priority=2
gpgcheck=1
type=rpm-md
gpgkey=https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/release.asc

[ceph-source]
name=Ceph source packages
baseurl=http://ceph.com/rpm-infernalis/el7/SRPMS
enabled=0
priority=2
gpgcheck=1
type=rpm-md
gpgkey=https://ceph.com/git/?p=ceph.git;a=blob_plain;f=keys/release.asc
```

#### 使用downloadonly下载安装包
```shell
yum install --downloadonly ceph-common --downloaddir=./
yum install --downloadonly ceph --downloaddir=./
yum install --downloadonly snappy leveldb gdisk python-argparse gperftools-libs --downloaddir=./
```
详细见[《yum和rpm使用方法》](http://zhoubofsy.github.io/2016/04/13/yum-rpm-usage-md/)

## 参考&鸣谢
---
* [redhat7.2配置yum源](http://blog.csdn.net/wylfengyujiancheng/article/details/50418930)
* [README](https://github.com/ceph/ceph-kmod-rpm)
* [Install Ceph Storage Cluster](http://docs.ceph.com/docs/infernalis/install/install-storage-cluster/)
