---
layout: post
title: linux下创建用户，并添加sudo权限
date: 2014-07-15
categories: blog
tags: [linux,adduser,sudo]
description: 

---




[TOC]

## 1、linux添加用户

命令：`adduser [你创建的用户名]`
```bash
# adduser hiho
Enter new UNIX password: 新帐号密码
Retype new UNIX password: 确认密码
passwd: password updated successfully
Changing the user information for hihozhou
Enter the new value, or press ENTER for the default
	Full Name []: 可直接回车
	Room Number []: 可直接回车
	Work Phone []: 可直接回车
	Home Phone []: 直接回车
	Other []: 直接回去


```



## 2、为用户添加sudo权限

- chmod u+w /etc/sudoers（为文件添加写权限）
- 找到`root ALL=(ALL)ALL`，换一行添加`新用户名 ALL=(ALL)ALL`
- chmod u-w /etc/sudoers（恢复权限）




—End—

## 迭代


* 2014年7月15日 19:09:17 初稿



