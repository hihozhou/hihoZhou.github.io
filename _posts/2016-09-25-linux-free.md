---
layout: post
title: linux free命令详解
date: 2016-09-25
categories: blog
tags: [linux命令,free]
description: free命令详解

---


[TOC]

#命令详解

Mem：表示物理内存统计   
-/+ buffers/cached：表示物理内存的缓存统计   
Swap：表示硬盘上交换分区的使用情况，  
 
第1行  Mem：  
total：表示物理内存总量。   
used：表示总计分配给缓存（包含buffers 与cache ）使用的数量，但其中可能部分缓存并未实际使用。   
free：未被分配的内存。   
shared：共享内存，一般系统不会用到，这里也不讨论。   
buffers：系统分配但未被使用的buffers 数量。   
cached：系统分配但未被使用的cache 数量。buffer 与cache 的区别见后面。   
total = used + free    
第2行   -/+ buffers/cached：  
used：也就是第一行中的used - buffers-cached   也是实际使用的内存总量。   
free：未被使用的buffers 与cache 和未被分配的内存之和，这就是系统当前实际可用内存。  

![linux-free.png](http://7xpyze.com1.z0.glb.clouddn.com/linux-free.png)


```
root@VM-29-177-ubuntu:/home/ubuntu# cat /proc/meminfo
MemTotal:        4048292 kB
MemFree:          679300 kB
Buffers:          272464 kB
Cached:          2415960 kB
SwapCached:         3060 kB
Active:          1772892 kB
Inactive:        1274880 kB
Active(anon):     285144 kB
Inactive(anon):    97376 kB
Active(file):    1487748 kB
Inactive(file):  1177504 kB
Unevictable:           0 kB
Mlocked:               0 kB
SwapTotal:       2097148 kB
SwapFree:        2067584 kB
Dirty:                 0 kB
Writeback:             0 kB
AnonPages:        357912 kB
Mapped:            33292 kB
Shmem:             23168 kB
Slab:             270212 kB
SReclaimable:     239804 kB
SUnreclaim:        30408 kB
KernelStack:        1056 kB
PageTables:        13972 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     4121292 kB
Committed_AS:     944608 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       10072 kB
VmallocChunk:   34359682968 kB
HardwareCorrupted:     0 kB
AnonHugePages:     63488 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
DirectMap4k:       28664 kB
DirectMap2M:     4165632 kB


root@VM-29-177-ubuntu:/home/ubuntu# free -m
             total       used       free     shared    buffers     cached
Mem:          3953       3280        672         22        266       2359
-/+ buffers/cache:        655       3298
Swap:         2047         28       2019

=================================
root@VM-29-177-ubuntu:/home/ubuntu# echo 1 > /proc/sys/vm/drop_caches
root@VM-29-177-ubuntu:/home/ubuntu# cat /proc/meminfo
MemTotal:        4048292 kB
MemFree:         3466940 kB
Buffers:            1056 kB
Cached:            48808 kB
SwapCached:         3060 kB
Active:           234912 kB
Inactive:         108644 kB
Active(anon):     214860 kB
Inactive(anon):   102092 kB
Active(file):      20052 kB
Inactive(file):     6552 kB
Unevictable:           0 kB
Mlocked:               0 kB
SwapTotal:       2097148 kB
SwapFree:        2067584 kB
Dirty:                 0 kB
Writeback:             0 kB
AnonPages:        292344 kB
Mapped:            33248 kB
Shmem:             23168 kB
Slab:             188596 kB
SReclaimable:     158452 kB
SUnreclaim:        30144 kB
KernelStack:        1048 kB
PageTables:        13148 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:     4121292 kB
Committed_AS:     860232 kB
VmallocTotal:   34359738367 kB
VmallocUsed:       10072 kB
VmallocChunk:   34359682968 kB
HardwareCorrupted:     0 kB
AnonHugePages:     63488 kB
HugePages_Total:       0
HugePages_Free:        0
HugePages_Rsvd:        0
HugePages_Surp:        0
Hugepagesize:       2048 kB
DirectMap4k:       28664 kB
DirectMap2M:     4165632 kB
root@VM-29-177-ubuntu:/home/ubuntu# free -m
             total       used       free     shared    buffers     cached
Mem:          3953        559       3394         22          1         47
-/+ buffers/cache:        510       3443
Swap:         2047         28       2019


```


—End—



##迭代


* 2016年06月24日 17:30:00 初稿
