---
layout: post
title: 在Ubuntu18.04中Android Studio开发环境搭建
date: 2019-09-11
categories: blog
tags: [go]
description:  在Ubuntu18.04中Android Studio开发环境搭建

---

## 第一步，从Android Studio官网，下载应用包。

官网地址： http://www.android-studio.org

![1.png](/source/images/ubuntu-android-studio/1.png)

解压并启动

```bash
tar -zxvf android-studio-ide-183.5522156-linux.tar.gz

```

## 第二步，开始android-studio的安装配置

启动android studio

```bash
cd android-studio
./bin/studio.sh
```

![2.png](/source/images/ubuntu-android-studio/2.png)

剩下的几乎都是选择和next就安装成功了

![3.png](/source/images/ubuntu-android-studio/3.png)
![4.png](/source/images/ubuntu-android-studio/4.png)
![5.png](/source/images/ubuntu-android-studio/5.png)
![6.png](/source/images/ubuntu-android-studio/6.png)
![7.png](/source/images/ubuntu-android-studio/7.png)
![8.png](/source/images/ubuntu-android-studio/8.png)


## android-studio不能使用中文输入法

我这里使用`搜狗输入法（fcitx）`

android studio 的目录打开`bin`目录编辑`studio.sh`,在文件的最上面加入下面三行:

```bash
export XMODIFIERS="@im=fcitx"
export GTK_IM_MODULE="fcitx"
export QT_IM_MODULE="fcitx"
```

然后重新启动android-studio即可

—End—

## 迭代

* 持续更新

## 参考

- [《ubuntu中解决android studio 不能输入中文》](https://blog.csdn.net/asdwkl2584561379/article/details/46375325)


