---
layout: post
title: Ubuntu下boot分区剩余空间不足解决
date: 2017-10-31
categories: blog
tags: [ubuntu,boot,boot空间不足]
description: Ubuntu下boot分区剩余空间不足解决

---

## 问题

今天在ubuntu下用apt-get安装pip的时候报错

![1.png](/source/images/ubuntu-boot-space/1.png )

这个主要是由于boot空间不足，而导致的，用命令`df -h`查看boot空间
这里安装系统的时候只分配的200M，在ubuntu14.04以前是足够的，但是在16.04之后的版本升级内核，就会出现空间不足问题。
所以在ubuntu16.04以上的版本要分配最少500M以上的空间给boot。
![2.png](/source/images/ubuntu-boot-space/2.png )


## 解决
如果不想重装系统，只能将现有旧版本内核并且不需要的删除来节省空间。

1.dpkg --get-selections |grep linux-image 查看已安装的linux-image各版本
![3.png](/source/images/ubuntu-boot-space/3.png)
2.uname -a 一般使用的都是最新版，如上图的3.8.0-19，但保险起见，确认下
![4.png](/source/images/ubuntu-boot-space/4.png)
这里可以看出，我在使用的是`4.4.0-97-generic`这个内核版本，`4.4.0-93`和`4.4.0-96`是可以删掉的。  

现在删除`linux-image-4.4.0-93-generic`，`sudo apt-get purge linux-image-4.4.0-93-generic`
当你运行删除命令后，系统会自动装上新的 ,在用`uname -a`命令查看。 

—End—


## 迭代

* 2017年10月31日 10：52 初稿

## 参考

- [Ubuntu下boot分区剩余空间不足解决](http://blog.csdn.net/shyanyang/article/details/35996219)
