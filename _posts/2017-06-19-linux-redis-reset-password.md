---
layout: post
title: linux redis修改密码
date: 2017-06-19
categories: blog
tags: [linux,redis,redis更改密码]
description: 

---

本文章系统为Ubuntu16.04

## 修改redis.conf
在系统Ubuntu16.04中，redis的配置文件在`/etc/redis/redis.conf`，

```bash
sudo vim /etc/redis/redis.conf

```

配置中密码配置项`requirepass`是注释了，所以默认是不需要密码

```
#requirepass foobared  
```

打开注释（`去掉'#'`）,并且修改为自己的密码  
e.g:

```
requirepass 123456  
```


重启redis

```bash
sudo service redis-server restart

```


登录用`-a`加上密码
```bash
redis-cli -a password
```

## 测试

![test-after-reset-password](/source/images/linux-redis-reset-password/test_on_after_reset_password.png)


## 

—End—


## 迭代

* 2017年06月19日 17:37 初稿

## 参考文章


