---
layout: post
title: ubuntu18.04 首次登录mysql未设置密码或忘记密码解决方法
date: 2019-08-26
categories: blog
tags: [ubuntu,mysql]
description: ubuntu18.04 首次登录mysql未设置密码或忘记密码解决方法

---


## 问题

在ubuntu18下，安装完mysql后，发现安装过程没有需要初始输入账号密码，安装完成后，也没有配置的地方，导致安装后没办法登录本地mysql


## 解决

1.首先输入一下命令

```bash
sudo cat /etc/mysql/debian.cnf
```

![1.png](/source/images/ubuntu18-mysql-reset-password/1.png)


2. 再输入以下指令：

```bash
mysql -u debian-sys-maint -p
//注意! 
//这条指令的密码输入是输入第一条指令获得的信息中的 password = zOBY6nbWUXJNR8kG 得来。
//请根据自己的实际情况填写！

```

运行截图如下：

![2.png](/source/images/ubuntu18-mysql-reset-password/2.png)


3. 修改密码，本篇文章将密码修改成 root , 用户可自行定义。

```bash
use mysql;
// 下面这句命令有点长，请注意。
update mysql.user set authentication_string=password('root') where user='root' and Host ='localhost';
update user set plugin="mysql_native_password"; 
flush privileges;
quit;
```

![3.png](/source/images/ubuntu18-mysql-reset-password/3.png)

4. 重新启动mysql:

```bash
sudo service mysql restart
mysql -u root -p // 启动后输入已经修改好的密码：root
```

![4.png](/source/images/ubuntu18-mysql-reset-password/4.png)


—End—

## 迭代

* 2019年08月26日 15：29 初稿

## 参考

- [《ubuntu18.04 首次登录mysql未设置密码或忘记密码解决方法》](https://blog.csdn.net/qq_38737992/article/details/81090373)

