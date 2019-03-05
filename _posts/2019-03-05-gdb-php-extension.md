---
layout: post
title: 使用gdb调试php扩展
date: 2019-03-05
categories: blog
tags: [php,php扩展,gdb]
description: 使用gdb调试php扩展

---

## 问题
我们在开发php扩展时候，有时候会出现code dump的情况

```bash
segmentation fault (core dumped)
```

## 1.编译debug版的php
我们首先需要生成`core`文件，一般在运行的的目录下，只有`debug版`的php执行才会生成`core`文件


## 2.ulimit -c unlimited
在执行php脚本前，想要生成core，首先要在终端执行命令`ulimit -c unlimited`

## 3.生成core文件
在1和2条件都准备好后，再次执行发生`core dumped`的php程序，就会在执行目录下生成core文件

![1.png](/source/images/gdb-php-extension/1.png)

## 4.调试

### 4.1 `gdb php core` 
使用`gdb php core`命令调试core文件，注意：php的版本一定要对应生成core文件的php版本
例如我环境下：

```bash
gdb /etc/php/7.2-debug/bin/php core
```

### 4.2 `bt`
使用bt命令可以输出具体错误信息

![2.png](/source/images/gdb-php-extension/2.png)
可以看到这里我是源码`php-opencv/source/opencv2/opencv_imgproc.cc`第1464行出现问题

然后发现其实是方法在获取输入参数solveMethod时候少了`&`。
![3.png](/source/images/gdb-php-extension/3.png)


—End—

## 迭代

* 2019年03月05日 13：03 初稿

## 参考


