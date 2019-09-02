---
layout: post
title: linux安装java
date: 2017-04-18
categories: blog
tags: [linux,java]
description: linux安装java

---

## 下载

直接到java官网[下载页面](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载java8 se

![1.png](/source/images/linux-java/1.png)

在下载之前我们要先选中“Accept License Agreement” 然后，才允许我们下载。
等等有好几个linux版本的 JDK，那么应该选择那个呢？我们先来看看他们有什么不同。

x86对应的是32位操作系统下的应用程序，x64对应的是64位操作系统下的应用程序，那我们在下载之前要先确定我的操作系统是32位的还是64位的。

## 安装


### 1.解压

打开终端

```bash
# 进入下载目录
cd ~/Downloads
# 创建目录
sudo mkdir /usr/local/java 
# 解压到目录
sudo tar zxvf jdk-8u221-linux-x64.tar.gz -C /usr/local/java

```


### 2.配置环境变量

```bash
sudo gedit /etc/environment
```

添加一下内容

```bash
# 添加java home环境变量
export JAVA_HOME=/usr/local/java/jdk1.8.0_221
# 添加classpath环境变量
export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
# PATH环境变量添加java可执行文件目录
PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:$JAVA_HOME/bin"
```


保存后关闭配置文件

要使配置文件生效，需要输入一下：

```bash
source /etc/environment
```

### 测试

```bash
java -version 
```

![2.png](/source/images/linux-java/2.png)

—End—

## 迭代


* 2017年04月18日 11:14 初稿

## 参考文章
- [《ubuntu下配置java环境》](https://www.cnblogs.com/fnng/archive/2013/01/30/2883815.html)



