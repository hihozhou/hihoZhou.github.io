---
layout: post
title: ubuntu截图工具flameshot
date: 2019-08-16
categories: blog
tags: [ubuntu,截图,flameshot]
description: ubuntu截图工具flameshot

---


## Flameshot

Flameshot 是一款开源软件新秀，其操作简单、直观，功能强大，关键是符合很多 Windows 用户的使用习惯。
[github地址](https://github.com/lupoDharkael/flameshot)


## 安装使用Flameshot

```bash
sudo apt-get install flameshot
```

命令行启动

```bash
flameshot gui
```

添加快捷键

我截图习惯`Ctrl`+`Alt`+`A`进行截图

点击系统设置`Setting` --> `Devices` --> `Keyboard`

![1.png](/source/images/ubuntu-flameshot/1.png)

拉到最下面，添加Command：`flameshot gui`，名称和快捷键按个人喜好
![2.png](/source/images/ubuntu-flameshot/2.png)

现在可以`Ctrl`+`Alt`+`A`进行截图了


## Flameshot编辑文字

我在ubuntu18下直接用apt安装flameshot后发现截图没有文字编辑功能，经网上搜索一下得出结果：

flameshot没有插入文字的功能有两种可能：

- 你的版本在0.6.0以下
- 你的版本在0.6.0及以上，但是你之前的时候安装过0.6.0以下的版本


先查看flameshot的版本：

```bash
➜  ~ flameshot -v
Flameshot 0.5.1-1(Debian)
Compiled with QT 5.9.4
```

我这里是0.6.0以下，所以要卸载，直接去github下载

第一步：杀掉flameshot进程和卸载flameshot

```bash
pkill flameshot
sudo apt-get purge flameshot
```

到github仓库下载0.6.0版本以上的安装包进行安装
github地址：https://github.com/lupoDharkael/flameshot

![3.png](/source/images/ubuntu-flameshot/3.png)

![4.png](/source/images/ubuntu-flameshot/4.png)

```bash
sudo dpkg -i flameshot_0.6.0_bionic_x86_64.deb
```

查看版本

```bash
➜  Downloads flameshot -v
Flameshot v0.6.0
Compiled with Qt 5.9.5

```

重启flameshot，然后命令行重启

![5.png](/source/images/ubuntu-flameshot/5.png)

```bash
flameshot config
```

勾选文字选项
![6.png](/source/images/ubuntu-flameshot/6.png)


—End—

## 迭代

* 2019年08月16日 09：55 初稿

## 参考

- [《flameshot github仓库地址》](https://github.com/lupoDharkael/flameshot)
- [《flameshot截图工具没有插入文字的功能》](https://blog.csdn.net/daerzei/article/details/84876170)

