---
title: "File_sysytem"
date: 2021-11-08T09:21:31+08:00
lastmod:
tags: [operating system]
categories: [大三上]
slug: file_system,operating system
draft: true
---
## 文件（File）和目录（Directory）
几个发展趋势

 * 硬盘带宽、消耗正保持指数型增长。（类似于cpu的速度，内存的大小等。）    
 * 查询时间、旋转延迟增长地非常缓慢。（需要移动物理对象，磁盘臂）    
 * 磁盘访问一个巨大的系统处于瓶颈&正在变糟。（带宽的提升让系统提前获取大的内存块和获取小的内存块代价相同，如果你要获得大量的某些东西需要增加延迟以提高带宽。）     
 * 桌面内存空间比以往的工作量增加地更快。（越来越多的工作正适应文件缓存，硬盘交通正在改变：很多的写操作以及新的数据。）    
 * 内存、cpu资源正在增加。（使用内存和cpu来进行更好的判断，复杂的提前获取以支持更多的io模式，延迟数据安置决策减少了杂乱的io。）   
 文件系统并不关心文件的结构和具体内容；FS的所有工作就是永久的存储数据，确保再次请求时，得到原来放在那里的内容,但是其实做到这点并不容易。    
 目录是一种特殊的文件，二者都是线性字节数组，每个文件有自己的低级名称作为索引节点号。目录包含着一串(文件名, 低级名称)对，目录中的每个条目都引用文件或其他目录。   
 创建文件：   
 ```
c
 int fd = open(“foo”, O_CREAT | O_WRONLY | O_TRUNC);
 ```
## VSFS（Very Simple File System）简单文件系统
## Locality and The FFS (Fast File System)
## Crash Consistency: FSCK and Journaling
## LFS日志结构FS (Log-structured File Systems)
## Flash-based SSD
