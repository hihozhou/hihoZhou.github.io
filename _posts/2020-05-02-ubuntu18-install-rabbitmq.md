---
layout: post
title: ubuntu18安装RabbitMQ
date: 2020-05-02
categories: blog
tags: [ubuntu,rabbitmq]
description: ubuntu18下安装rabbitmq

---

## 1、安装erlang

由于rabbitMq需要erlang语言的支持，在安装rabbitMq之前需要安装erlang

```bash
sudo apt-get install erlang-nox
```

## 2、安装RabbitMQ

更apt新源

```bash
sudo apt-get update
```

安装rabbitmq服务端

```bash
sudo apt-get install rabbitmq-server
```

启动、停止、重启、状态rabbitMq命令

```bash
sudo service rabbitmq-server start
sudo service rabbitmq-server stop
sudo service rabbitmq-server restart
sudo service rabbitmq-server status
sudo rabbitmqctl status
```


## 3、添加admin，并赋予administrator权限

添加admin用户，密码设置为admin

```bash
sudo rabbitmqctl add_user  admin  admin
```

赋予权限

```bash
sudo rabbitmqctl set_user_tags admin administrator 
```

赋予virtual host中所有资源的配置、写、读权限以便管理其中的资源

```bash
sudo rabbitmqctl  set_permissions -p / admin '.*' '.*' '.*'
```

![1.png](/source/images/ubuntu18-install-rabbitmq/1.png)

## 4、Web管理器连接

启用rabbitmq-management插件

```bash
sudo rabbitmq-plugins enable rabbitmq_management
```

![2.png](/source/images/ubuntu18-install-rabbitmq/2.png)

浏览器访问http://127.0.0.1:15672

![3.png](/source/images/ubuntu18-install-rabbitmq/3.png)

`使用刚刚创建的admin就可以登录，密码也为admin`

![4.png](/source/images/ubuntu18-install-rabbitmq/4.png)

到这里，基本安装就完成了。

—End—

## 迭代

* 2020年05月02日 18：32 初稿

## 参考

