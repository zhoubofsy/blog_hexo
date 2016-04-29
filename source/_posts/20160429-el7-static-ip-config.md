---
title: el7 静态 ip 配置
date: 2016-04-29 10:57:28
tags: other
---


# 查看网卡
```shell
ip link
```

# 修改网卡配置
* 修改配置文件ifcfg-{网卡名称}
eg: /etc/sysconfig/network-scripts/ifcfg-eno16777736

```shell
ONBOOT=yes
IPADDR=192.168.1.234
GATEWAY=192.168.1.1
DNS1=202.96.64.68
DNS2=8.8.8.8
```
* 修改resolv.conf
eg: /etc/resolv.conf
```shell
nameserver 202.96.64.68
nameserver 8.8.8.8
```

# 重启network服务
重启network服务
```shell
service network restart
```

# 参考&鸣谢
* [centos7配置静态IP](http://www.centoscn.com/CentOS/config/2014/0902/3639.html)
