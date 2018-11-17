---
layout: post
title: Window安装使用jekyll
date: 2017-07-20
categories: blog
tags: [jekyll,window]
description: window系统安装jekyll

---

以前在ubuntu上用jekyll直接`apt-get install jekyll`就可以直接安装jekyll，并且把相关依赖(ruby,gems等)也安装好。
但木有办法，有时候需要在window上总结检验，所以还是要搭建一下博客环境。

## 1.安装ruby和Ruby DevKit

### 1.1下载ruby
可以到官网下载现在window的安装包：[下载地址](http://rubyinstaller.org/downloads/)。
选择自己对应系统版本下载，我电脑是64位的window8.1系统，下载后就是双击傻瓜式安装就可以了。
直接选择ruby+devkit的版本包，就不用ruby和devkit分开安装和配置
![1.png](/source/images/window-jekyll/1.png)

需要注意的是安装的时候勾选上添加到环境变量中。
![2.png](/source/images/window-jekyll/2.png)
如果这一步忘记添加，请在环境变量中加入你的ruby安装目录即可。

### 1.2安装MSYS2
![3.png](/source/images/window-jekyll/3.png)
选择1后回车就会自动安装完成了
![4.png](/source/images/window-jekyll/4.png)

### 1.3测试ruby
`win + r`然后输入`cmd`回车调出cmd窗口，输入`ruby -v`坚持ruby是否安装成功
![5.png](/source/images/window-jekyll/5.png)

到这里，ruby和Devkit就安装完成了

## 2.安装Jekyll
打开cmd窗口运行`gem install jekyll`就会自动安装jekyll
![6.png](/source/images/window-jekyll/6.png)

然后在博客项目运行`jekyll serve`就可以启动服务了

### 2.2错误
运行`jekyll serve`可能会报错，一般情况是缺少库
![7.png](/source/images/window-jekyll/7.png)
只要`gem install 缺少的库`就可以了,如`gem install tzinfo`

—End—

## 迭代


* 2017年7月20日 13:04:17 初稿



