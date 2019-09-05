---
layout: post
title: Ubuntu18.04 安装 网易云音乐
date: 2019-09-04
categories: blog
tags: [ubuntu,网易云音乐]
description: Ubuntu18.04 安装 网易云音乐

---


## 下载

到[github](https://github.com/innoob/netease-cloud-music/releases)下载deb安装包

![1.png](/source/images/ubuntu18-netease-cloud-music/1.png)

## 安装

进入下载目录，使用dpkg命令安装

```bash
cd ~/Download
sudo dpkg -i netease-cloud-music_1.1.1+rebuild.release-2_amd64.deb
```

```bash
# 如果出现依赖问题
sudo apt-get -f install
# 重新在安装以下 
sudo dpkg -i netease-cloud-music_1.0.0_amd64_ubuntu16.04.deb 
```

命令行使用`netease-cloud-music`会打不开

## 解决打不开问题

```bash
sudo vim /usr/share/applications/netease-cloud-music.desktop
#找到 exec 那一行 ，在 %U 前面加上 --no-sandbox
```

![2.png](/source/images/ubuntu18-netease-cloud-music/2.png)


## 打开网易云音乐

终端输入命令回车

```bash
netease-cloud-music
```

![3.png](/source/images/ubuntu18-netease-cloud-music/3.png)

—End—

## 迭代

* 2019年09月04日 15：29 初稿

## 参考

- [《Ubuntu18.04 安装 网易云音乐 解决 打不开的问题》](https://blog.csdn.net/qq_14824885/article/details/81142409)

