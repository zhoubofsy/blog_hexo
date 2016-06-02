---
title: ceph J版与Xenserver6.5集成
date: 2016-05-04 10:20:43
tags: ceph
---

# 集成原理
```shell
  +-----------+
  |           |
  |           |
  |           |                          +-- ISCSI SR ----+
  | XenServer |         +---------+      |                |     +------------+ 
  |           | ------> |SR Driver| -----+-- NFS SR   ----+-----| XenCenter  |
  |           |         +---------+      |                |     +------------+ 
  |           |                          +-- RBD SR   ----+
  +-----------+                           ( Fork ISCSI SR)
```
* RBDSR通过KRBD映射ceph快设备（详细请见[不使用ceph-common 进行内核映射 rbd 块设备](http://zhoubofsy.github.io/2016/04/27/20160427-map-rbd-kernel-without-ceph-common-md/)）
* XenCenter在添加ISCSI设备界面中通过指定端口为“6789”来区别使用ISCSISR，还是使用RBDSR

# 步骤
## 1. 搭建ceph J版集群

详细请见： 
* [ceph Jewel 10.2.0 部署测试](zhoubofsy.github.io/2016/04/29/20160429-jewel-of-ceph-deploy-test/)
* [在RHEL7.2平台上搭建ceph集群](http://zhoubofsy.github.io/2016/04/15/20160415_ceph-install-deploy-config-on-rhel7-md/)

## 2. 调整ceph集群crush tunables与XenServer内核匹配
针对XenServer6.5，需要调整crush tunables 为 bobtail
```shell
ceph osd crush tunables bobtail
```

查看更改
```shell
ceph osd crush show-tunables
```

## 3. 创建ceph rbd块设备

* 为了保证快设备可映射、可快照、可克隆，必须使用format 2
* ceph J版必须指定feature 为 layering
* 快设备名称必须使用uuid（不带“-”分隔）

```shell
uuidgen | sed 's/-//g'     # 生成无“-”分隔符的rbd名称
rbd create --size {size} {rbd名称} --image-feature layering
```


## 4. XenServer节点安装RBDSR patch

详细请见：
[RBDSR README](https://github.com/zhoubofsy/rbdsr/tree/1.0.0)

## 5. 使用XenCenter创建SR

同上

# 注意

* ceph J版 开始抛弃KRBD
* ceph J版 tunables会引发集群告警“crush map has legacy tunables (require bobtail, min is firefly)”（可忽略） 
* 创建rbd快设备必须使用format 2 格式，否则不能正常映射（map）
* ceph J版 rbd格式feature中只能包含layering，不能包含其他feature，否则不能正常映射（map）

# 参考&鸣谢

* [mstarikov/rbdsr](https://github.com/mstarikov/rbdsr)
