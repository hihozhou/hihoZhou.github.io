---
layout: post
title: window下编译OpenCV（包含opencv_contrib）
date: 2019-03-16
categories: blog
tags: [window,opencv,opencv_contrib,cmake]
description: 使用gdb调试php扩展

---

## 1.环境准备

### 1.1 安装`Visual Studio C++`

我这里直接使用`Visual Studio`安装，版本为`Visual Studio 15 2017`

### 1.2 安装cmake

cmake可以直接到[官网下载](https://cmake.org/download/)，然后无脑下一步就可以，但是记得勾选加入到`path`中，这样可以直接全局调用cmake命令

![1.png](/source/images/window-compile-opencv/1.png)

### 下载opencv和opencv_contrib源码

```bash
git clone https://github.com/opencv/opencv.git --depth 1
git clone https://github.com/opencv/opencv_contrib.git --depth 1

```

我这里分别下载到`F:\www`目录下。

![2.png](/source/images/window-compile-opencv/2.png)


## 编译安装opencv

1.进入到opencv源码根目录，也就是`F:\www\opencv`，在该目录下新建一个`build`的空文件夹。

![3.png](/source/images/window-compile-opencv/3.png)

2.配置编译编译

```bash
cmake -D CMAKE_BUILD_TYPE=RELEASE  -G "Visual Studio 15 2017 Win64" -D INSTALL_C_EXAMPLES=OFF -D INSTALL_PYTHON_EXAMPLES=OFF -D BUILD_EXAMPLES=OFF -D BUILD_JAVA=OFF -D BUILD_TESTS=OFF -D BUILD_DOCS=OFF -D BUILD_opencv_world=ON -D OPENCV_EXTRA_MODULES_PATH=F:\www\opencv_contrib ..
```

- 使用`-G`参数指定使用电脑上的`Visual Studio 15 2017`进行编译
- `-D BUILD_opencv_world=ON` 为了方便项目配置和调试，在生产环境中建议打开BUILD_opencv_world，把各OpenCV模块编译成一个世界模块，这样最后生成的只有一个.lib或者.dll(后者需要打开BUILD_SHARED_LIBS开关)，但在发行版本中建议按需引入相应模块以减少体积(除非你每个模块都有使用到)。
- `OPENCV_EXTRA_MODULES_PATH`为电脑上`opencv_contrib`的源码目录，如果你不需要编译`opencv_contrib`可以不填该参数

![4.png](/source/images/window-compile-opencv/4.png)

![5.png](/source/images/window-compile-opencv/5.png)

3.执行编译安装命令

```bash
cmake --build . --target INSTALL --config Release
```

![6.png](/source/images/window-compile-opencv/6.png)

编译安装过程比较漫长，可以考虑去做点啥事情。 


—End—

## 迭代

* 2019年03月17日 22：50 初稿

## 参考


