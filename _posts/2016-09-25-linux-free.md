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





—End—



##迭代


* 2016年06月24日 17:30:00 初稿
