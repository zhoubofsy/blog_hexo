---
title: "ceph & OS 版本选择"
date: 2016-04-12 08:00
tags: plan
---

# 背景介绍
ceph 版本分类包括“开发版”、“稳定版（Stable）”、“长期支持版（LTS）”；
* 开发版，增加修改功能而产生的版本
* 稳定版本，短期维护的版本，周期不固定
* 长期支持版本，长期维护的版本，周期为10个月左右(或更长)

以下列出稳定版和长期支持版本及对应的版本号：

| 版本名称          | 分类          | 版本号            |
|-------------------|:-------------:|------------------:|
|argonaut           | Stable        |  v0.48 ~ v0.48.3  |
|bobtail            | Stable        |  v0.56 ~ v0.56.7  |
|Cuttlefish         | Stable        |  v0.61 ~ v0.61.9  |
|Dumpling           | LTS           |  v0.67 ~ v0.67.12 |
|Emperor            | Stable        |  v0.72 ~ v0.72.3  |
|Firefly            | LTS           |  v0.80 ~ v0.80.11 |
|Giant              | Stable        |  v0.87 ~ v0.87.2  |
|Hammer             | LTS           |  v0.94 ~ v0.94.6  |
|Infernalis         | Stable        |  v9.2.0 ~ v9.2.1  |
|Jewel              | Stable or LTS |  v10.2.0（未发布）|

# 稳定性&功能完整性

* ceph 的“Stable版”和“LTS版”稳定性相对可靠，但LTS版本由社区维护更新，稳定性更佳；
* ceph 的功能在开发版中开发，然后合并到稳定版中维护修改bug，新版本涵盖旧版本功能；
* ceph 只有一条产品路线，所以各个版本中发现的bug，最终都会合并到主线版本上，导致在Stable（或LTS）版本中发现的问题，会在下一个Stable（或LTS）版本中得到修复；

综上所述，可供选择的版本有Hammer 、 Infernalis 和 Jewel

# 性能最优

* BlueStore 提供更优的后端存储性能
    Hammer及以前版本后端存储采用的是XFS文件系统，这样延长了数据落盘的路径；
    在Jewel版本中ceph增加了一种存储方式BlueStore，BlueStore可以直接在裸设备上进行读写操作，缩短落盘路径，性能更优；
* 纠删码（EC）性能优化
    ceph 对数据保护采用的是多副本（三副本）的方式，这种方式简单稳定性能好，但存储成本较贵，由此ceph推出了EC，但其EC性能较差，只能配合cache tier使用，才能发挥功效；
    为此在 Infernalis 及以后版本中对EC性能和稳定性进行了优化；

# 环境依赖

ceph的运行平台众多，包括：CentOS（RHEL）、Debian、Ubuntu、Fedora

CentOS（RHEL）

* Hammer 及以前版本对应平台 CentOS 6
* Infernalis 及以后版本对应平台 CentOS 7 或更高版本

# Stable & LTS时效性

|           |Hammer      |Infernalis   |
|-----------|:----------:|------------:|
|启始日期   | 2015.04    | 2015.11     |
|终止日期   | 2016.11    | 2016.06     |

# 结论

1. ceph 建议采用 Infernalis 版本（若实施前Jewel Stable or LTS版本发出，可直接选择Jewel）
2. OS 建议采用 CentOS 7

原因：
* 从稳定性和功能完整性角度来说，目前可以选择Hammer或者Infernalis
* 从性能最优角度说，最理想的选择是Jewel
* 考虑到日后升级维护，Infernalis的下一个LTS版本（可能是Jewel）会是最好的选择
* 若选择Hammer，日后想升级到Jewel，需要先升级OS，然后再升级ceph版本，改动太大，风险不可控制
* 若选择Infernalis，日后升级，可直接升级ceph版本即可
