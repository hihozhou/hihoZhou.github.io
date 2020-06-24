---
layout: post
title: mac安装使用ActiveMQ Artemis
date: 2020-06-23
categories: blog
tags: [activemq,artemis,mac]
description: mac安装activemq artemis

---

## 安装

### 下载

到官网http://activemq.apache.org/选择 `ActiveMQ Artemis`->`Download Latest`


![1.png](/source/images/mac-activemq-artermis/1.png)

![2.png](/source/images/mac-activemq-artermis/2.png)

### 解压

```bash
tar -zxvf apache-artemis-2.13.0-bin.tar.gz
```


```bash

//目录结构如下
|___ bin //二进制和脚本
|
|___ examples //各自demo
|      |___ common
|      |___ features
|      |___ perf
|      |___ protocols
|
|___ lib //jar和libraries 
|      |___ client
|
|___ schema //用于校验Artemis配置
|
|___ web //web上下文
       |___ api //api文档
       |___ hacking-guide
       |___ migration-guide //迁移指南
       |___ user-manual //用户手册
```

### 创建Broker实例

```bash
cd apache-artemis-2.13.0
./bin/artemis create mybroker
```

![3.png](/source/images/mac-activemq-artermis/3.png)

### 运行activemq

```bash
cd mybroker/bin
./artemis run 
# 或 后台执行
./artemis-service start
```

![4.png](/source/images/mac-activemq-artermis/4.png)


## 通过浏览器访问activemq控制台

web访问地址为 `http://localhost:8161/` ,接口地址端口是`61616`

![5.png](/source/images/mac-activemq-artermis/5.png)

输入刚刚创建broker实例时候输入的账号密码

![6.png](/source/images/mac-activemq-artermis/6.png)

![7.png](/source/images/mac-activemq-artermis/7.png)

—End—

## 迭代

* 2020年06月23日 12：02 初稿

## 参考

