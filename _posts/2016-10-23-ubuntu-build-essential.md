---
layout: post
title: ubunut build-essential软件包
date: 2016-10-23
categories: blog
tags: [ubuntu,build-essential]
description: build-essential。

---


##build-essential软件包
Ubuntu缺省情况下，并没有提供C/C++的编译环境，因此还需要手动安装。  
如果单独安装gcc以及g++比较麻烦，幸运的是，为了能够编译Ubuntu的内核，Ubuntu提供了一个build-essential软件包。
查看该软件包的依赖关系，可以看到以下内容：

```
$ apt-cache depends build-essential
build-essential
 |Depends: libc6-dev
  Depends: <libc-dev>
    libc6-dev
  Depends: gcc
  Depends: g++
  Depends: make
    make:i386
  Depends: dpkg-dev
  Conflicts: build-essential:i386

```

　也就是说，安装了该软件包，编译c/c++所需要的软件包也都会被安装。因此如果想在Ubuntu中编译c/c++程序，只需要安装该软件包就可以了。

##安装

```
sudo apt-get install build-essential
```


##总结:
如果我们需要编译c/c++程序前,要先安装编译所需的环境,ubuntu build-essential就是为了方便解决这问题而出现的。


—End—

##迭代


* 2016年10月23日 22:13 初稿



