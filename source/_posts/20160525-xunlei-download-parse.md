---
title: 迅雷下载解析（上）
date: 2016-05-25 14:39:03
tags: net
---

# 需求
所有使用非windows的用户在需要下载迅雷资源的时候，大概都有两种方法。其一，使用wine，装一个xunlei，然后下载资源；其二，利用离线下载，先下载到离线服务器上，然后利用http或ftp协议下载到本地。

为啥迅雷不开发一个linux版呢！（个人猜测，估计是怕linux里面的license吧）相信为啥没有linux版本，只有迅雷知道。

为了能让linux下载迅雷资源，做了如下调研。。。

# thunder 地址解码
通常能看到的 thunder 地址，都是通过base64 encode 过的 http 或 ftp 地址。所以解码，也需要用base64

```shell
# base64 加密后的http 或 ftp 地址
thunder://QUFmdHA6Ly9keWdvZDE6ZHlnb2QxQGQxMzEuZHlnb2QuY246MzA0OS/pnZ7or5rli7/mibBEVkRzY3IvW+eUteW9seWkqeWggnd3dy5keWdvZC5jbl3pnZ7or5rli7/mibBjZDEucm12Ylpa

# 经过 base64 -d decode 之后
echo QUFmdHA6Ly9keWdvZDE6ZHlnb2QxQGQxMzEuZHlnb2QuY246MzA0OS/pnZ7or5rli7/mibBEVkRzY3IvW+eUteW9seWkqeWggnd3dy5keWdvZC5jbl3pnZ7or5rli7/mibBjZDEucm12Ylpa | base64 -d

AAftp://dygod1:dygod1@d131.dygod.cn:3049/非诚勿扰DVDscr/[电影天堂www.dygod.cn]非诚勿扰cd1.rmvbZZ%
```

看到了吧，去掉开头的“AA”，去掉结尾的“ZZ%”，就剩下了一个ftp地址，这个ftp地址，就是迅雷下载时使用的ftp地址。

# 迅雷资源下载

使用 wget 下载上文中的ftp资源
```shell
$ wget -v ftp://i:i@d3.dl1234.com:6958/\[电影天堂www.dy2018.com\]神战权力之眼BD中英双字.rmvb
--2016-05-25 14:58:52--  ftp://i:*password*@d3.dl1234.com:6958/[%E7%94%B5%E5%BD%B1%E5%A4%A9%E5%A0%82www.dy2018.com]%E7%A5%9E%E6%88%98%E6%9D%83%E5%8A%9B%E4%B9%8B%E7%9C%BCBD%E4%B8%AD%E8%8B%B1%E5%8F%8C%E5%AD%97.rmvb
           => ‘.listing’
Resolving d3.dl1234.com (d3.dl1234.com)... 218.93.205.139
Connecting to d3.dl1234.com (d3.dl1234.com)|218.93.205.139|:6958... failed: Connection refused.
--2016-05-25 14:58:52--  ftp://i:*password*@d3.dl1234.com:6958/[%E7%94%B5%E5%BD%B1%E5%A4%A9%E5%A0%82www.dy2018.com]%E7%A5%9E%E6%88%98%E6%9D%83%E5%8A%9B%E4%B9%8B%E7%9C%BCBD%E4%B8%AD%E8%8B%B1%E5%8F%8C%E5%AD%97.rmvb
           => ‘.listing’
Connecting to d3.dl1234.com (d3.dl1234.com)|218.93.205.139|:6958... failed: Connection refused.
```
结果被无情的 refused 了，后来又用别的ftp客户端尝试下载结果，依然失败。。。

痛定思痛，上网搜索了一下，有片文章的分析我觉得可能是正确的，其文如下：

”（我揣测）迅雷走的是P2SP，也就是先访问它的索引server，找到真实可用的ip。换句话说，这个URL仅仅起到一个资源标识符的作用，本身也许未必能直接用于下载。 所以说这个东西，不逆向官方客户端恐怕是没法搞的，因为不知道它和服务器之间的协议。“

# 结论
根据网友的分析，后续可以下载一个迅雷软件分析看看

# 参考&鸣谢
* [能支持迅雷下载地址么？（类似thunder://）](https://github.com/soimort/you-get/issues/929)
