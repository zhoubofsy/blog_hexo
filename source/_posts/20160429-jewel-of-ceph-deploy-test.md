---
title: ceph Jewel 10.2.0 部署测试
date: 2016-04-29 16:11:57
tags: ceph
---

# 环境
* RHEL 7.2
* ceph Jewel(10.2.0)

# 部署方法
* 基本部署方式与Infernalis版本相同（[《在RHEL7.2平台上搭建ceph集群》](http://zhoubofsy.github.io/2016/04/15/20160415_ceph-install-deploy-config-on-rhel7-md/)）
* 其中OSD部署时，文件系统建议选择XFS，不能选择EXT4（EXT4，在OSD初始化时会报“File name too lang”错误）

# 参考&鸣谢
* [CEPH BUG #15543](http://tracker.ceph.com/issues/15543)
