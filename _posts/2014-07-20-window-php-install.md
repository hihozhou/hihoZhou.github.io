---
layout: post
title: Window安装php7
date: 2014-07-20
categories: blog
tags: [php安装,window]
description: window操作系统安装php7

---

## 安装环境（Microsoft Visual C++）

### 安装PHP对应的VC
在window中安装php可以首先要安装php需要的运行环境，也就是c,c++环境
到[php window下载官网](https://windows.php.net/download)有对应想要的php和运行环境下载
![1.png](/source/images/window-php-install/1.png)
左边往下拉有对应的VC对应版本的下载链接
![2.png](/source/images/window-php-install/2.png)

### 双击安装
这个环境包很小，只有十几兆，不要和ide混淆
![3.png](/source/images/window-php-install/3.png)

## 安装PHP
下载刚刚上面的PHP包，后解压到指定的目录，我这里解压到`F:\www\php-7.2.12-nts-Win32-VC15-x64`
![4.png](/source/images/window-php-install/4.png)

使用快捷键`win + r`然乎输入`cmd`回车打开CMD窗口
![5.png](/source/images/window-php-install/5.png)
进入PHP解压的目录，运行`php.exe -v`测试
![6.png](/source/images/window-php-install/6.png)

### 配置到全局环境
为了能够全局可以调用php.exe，我们需要把php配置到系统的全局环境变量中，
`我的电脑->右键->系统属性->高级系统设置->环境变量->找到path->点击编辑`
![7.png](/source/images/window-php-install/7.png)

然后加上你的`;php目录的地址`，我这里是`;F:\www\php-7.2.12-nts-Win32-VC15-x64`
![8.png](/source/images/window-php-install/8.png)
然后就可以全局调用php.exe了
![9.png](/source/images/window-php-install/9.png)




—End—

## 迭代


* 2014年7月20日 13:04:17 初稿



