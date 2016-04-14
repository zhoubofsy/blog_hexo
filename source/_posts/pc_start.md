title: x86计算机启动过程
date: 2015-12-19 09:56
tags: storage
---

# 启动流程
1. BIOS读取MBR(Master Boot record)
2. MBR引导Bootloader(NTLOADER or GRUB or LILO...)
3. Bootloader加载kernel
4. init进程初始化

# MBR结构
* MBR只有512字节大小，存储在硬盘的０道０面１扇区
* 引导代码（BootCode），446 bytes
* 分区表（Partition table），64 bytes
* Magic Number(55AA)，2 bytes

```vim
        -+-  +-------------------+
         |   |                   |
         |   |                   |
         |   |                   |
         |   |                   |
   446 B |   |     Boot Code     |
         |   |                   |
         |   |                   |
         |   |                   |                                              
         |   |                   |                                              
        -+-  +-------------------+                                              
         |   |                   |                   +-------------------------+
    64 B |   |     Partition     |                   |        Partition 1      |
         |   |       Table       |   --------->      +-------------------------+
         |   |                   |                   |        Partition 2      | 
        -+-  +-------------------+                   +-------------------------+
         |   |                   |                   |        Partition 3      |
     2 B |   |   Magic Number    |                   +-------------------------+
         |   |                   |                   |        Partition 4      |
        -+-  +-------------------+                   +-------------------------+
                                                                   |
                                                                   |
                                                                   v
                                                                    
              +----------------+-----------+----------------+---------+-----------+------+
              | Partition flag | Start CHS | Partition byte | End CHS | Start LBA | Size |                                  
              +----------------+-----------+----------------+---------+-----------+------+
```

# 参考文献
* [Linux启动过程详解_MBR和GRUB概述](http://wenku.baidu.com/link?url=fGCk68YwJL97k5hfvXe_5FD018-q5dQvhy8emq4E7HybyPJzd9kJHMShz793BXiPT_bvETchHKxyNgBdbwvnN46In1ORPa872kbxSGwGjeO)
