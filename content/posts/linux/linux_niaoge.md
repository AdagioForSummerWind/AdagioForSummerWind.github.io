---
title: "鸟哥的Linux私房菜"
date: 2022-07-15T16:59:08+08:00
lastmod: 2022-07-15
tags: [linux]
categories: []
slug: linux base
draft: true
---
# 鸟哥的 Linux 私房菜
## 主机规划与磁盘分区

### 磁盘连接方式与设备文件名之间的关系


个人计算机常见的磁盘接口有两种， 分别是SATA与SAS接口，目前（2015）的主流是SATA接口。

正常的实体机器大概使用的都是 /dev/sd[a-] 的磁盘文件名，至于虚拟机环境下面，为了加速，可能就会使用 /dev/vd[a-p] 这种设备文件名

以SATA接口来说，由于SATA/USB/SAS等磁盘接口都是使用SCSI模块来驱动的， 因此这些接口的磁盘设备文件名都是/dev/sd[a-p]的格式。 所以SATA/USB接口的磁盘根本就没有一定的顺序，那如何决定他的设备文件名呢？ 这个时候就得要根据Linux核心侦测到磁盘的顺序了！

例题：如果你的PC上面有两个SATA磁盘以及一个USB磁盘，而主板上面有六个SATA的插槽。这两个SATA磁盘分别安插在主板上的SATA1, SATA5插槽上， 请问这三个磁盘在Linux中的设备文件名为何？答：由于是使用侦测到的顺序来决定设备文件名，并非与实际插槽代号有关，因此设备的文件名如下：

SATA1插槽上的文件名：/dev/sda
SATA5插槽上的文件名：/dev/sdb
USB磁盘（开机完成后才被系统捉到）：/dev/sdc

















