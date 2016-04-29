---
title: 不使用ceph-common 进行内核映射 rbd 块设备
date: 2016-04-27 16:04:57
tags: ceph
---

## 例子

```shell
modprobe rbd
echo "192.168.1.234 name=admin,secret=AQAHzRBX77ORERAAqfW0YAqq8+ETXMqWtPKwkQ== rbd testrbd" > /sys/bus/rbd/add
```

## 解释

* 192.168.1.234 为 Monitor 地址
* name，制定使用的用户，可通过“ceph auth list” 查看
* secret，name制定用户所对应的 key 值
* rbd，制定需要映射的块设备（该快设备必须是已经创建好的）
* 使用客户端内核必须支持rbd.ko这个内核模块


## 参考&鸣谢
* [Map Rbd Kernel Without Install Ceph-common](http://cephnotes.ksperis.com/blog/2014/01/09/map-rbd-kernel-without-install-ceph-common)
