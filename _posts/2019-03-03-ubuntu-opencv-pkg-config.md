---
layout: post
title: Ubuntu下编译安装OpenCV无法使用pkg-config检查
date: 2019-03-03
categories: blog
tags: [ubuntu,opencv,pkg-config]
description: Ubuntu下编译安装OpenCV无法使用pkg-config检查

---

## 问题

在Ubuntu18.04下采用编译安装opencv4，在`make`和`make install`后想用`pkg-config`查找opencv发现提示没有找到

```bash
~ pkg-config --libs opencv4
Package opencv4 was not found in the pkg-config search path.
Perhaps you should add the directory containing `opencv4.pc'
to the PKG_CONFIG_PATH environment variable
No package 'opencv4' found
```

后来查找资料发现在`cmake`编译配置时候需要添加`-D OPENCV_GENERATE_PKGCONFIG=YES`

```bash
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_TBB=ON -D BUILD_NEW_PYTHON_SUPPORT=ON -D WITH_V4L=ON -D INSTALL_C_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON -D BUILD_EXAMPLES=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D OPENCV_GENERATE_PKGCONFIG=YES -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules ..

```


这样编译就可以使用pkg-config检测

—End—

## 迭代

* 2019年03月03日 22：55 初稿

## 参考

- [《4.0.0 does not produce pkgconfig file》](https://github.com/opencv/opencv/issues/13154#issuecomment-456652297)

