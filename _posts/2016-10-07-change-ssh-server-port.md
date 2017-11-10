---
layout: post
title: linux更改ssh服务器默认端口
date: 2016-10-07
categories: blog
tags: [linux,ssh]
description: linux修改ssh服务器默认端口

---

注意:操作系统为ubuntu14.04,不同系统修改的配置文件以及对应命令可能会有所不同.


[TOC]

### 修改配置文件

```bash
vim /etc/ssh/sshd_config
```

### 找到Port 22，然后将端口22改为你想要的端口号:

```
...

# What ports, IPs and protocols we listen for
Port 22
#Port 8384

...

```

### 重启ssh服务器

```
ubuntu@VM-29-177-ubuntu:~$ sudo service ssh restart 
ssh stop/waiting
ssh start/running, process 19578

```

### 测试

```bash
ssh localhost -p 端口
```

### 链接命令

```
ssh -l ubuntu IP -p 端口
```


—End—



## 迭代


* 2016年10月07日 16:27:00 初稿


