---
layout: post
title: ubunut安装opencv
date: 2016-10-23
categories: blog
tags: [linux,opencv,oepncv安装]
description: ubuntu opencv安装。

---

注:本系统为ubuntu14.04,linux系统大同小异

软件环境要求:

- GCC 4.4.x or later
- CMake 2.6 or higher
- Git
- GTK+2.x or higher, including headers (libgtk2.0-dev)
- pkg-config
- Python 2.6 or later and Numpy 1.5 or later with developer packages (python-dev, python-numpy)
- ffmpeg or libav development packages: libavcodec-dev, libavformat-dev, libswscale-dev
- \[optional\] libtbb2 libtbb-dev
- [optional] libdc1394 2.x
- [optional] libjpeg-dev, libpng-dev, libtiff-dev, libjasper-dev, libdc1394-22-dev

## 安装依赖库

### 安装需要编译安装opencv的环境

```bash
sudo apt-get install build-essential cmake pkg-config
```

- build-essential(c\c++编译环境)
- cmake (编译工具)

### 安装必需依赖库
1.安装图片I/O库

```bash
sudo apt-get -y install libjpeg62-dev 
sudo apt-get -y install libtiff4-dev libjasper-dev
sudo apt-get -y libpng12-dev
```

2.安装GTK开发库

```bash
sudo apt-get -y install  libgtk2.0-dev
```

3.安装视频I/0库

```bash
sudo apt-get -y install libavcodec-dev libavformat-dev libswscale-dev libv4l-dev
```

### 安装可选依赖库
1.install support for Firewire video cameras

```bash
sudo apt-get -y install libdc1394-22-dev
```

2.install video streaming libraries

```bash
sudo apt-get -y install libxine-dev libgstreamer0.10-dev libgstreamer-plugins-base0.10-dev 
```

3.install the Python development environment and the Python Numerical library

```bash
#默认python2.7
sudo apt-get -y install python-dev python-numpy
```

4.install the parallel code processing library (the Intel tbb library)

```bash
sudo apt-get -y install libtbb-dev libtbb2
```

5.install the Qt dev library
    
```bash
sudo apt-get -y install libqt4-dev
```




## 安装opencv

### 下载opencv
下载地址:[sourceforge](https://sourceforge.net/projects/opencvlibrary/) or [github](https://github.com/opencv/opencv)


### 编译安装

```bash
#解压
unzip opencv-3.1.0.zip
#进入解压目录
cd opencv-3.1.0
mkdir release
cd release
#配置编译
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_TBB=ON -D BUILD_NEW_PYTHON_SUPPORT=ON -D WITH_V4L=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D WITH_IPP=OFF -D PYTHON3_EXECUTABLE=/usr/bin/python3 -D INSTALL_PYTHON_EXAMPLES=ON  ..
#编译
make
#安装
make install
```

运行make install 后会要稍微等一段时间....


OK,见到这个画面时候,恭喜你,opencv安装成功了.
不过我还要安转python调用opencv进行测试


## 安装python-opencv库
```bash
sudo apt-get install python-opencv
sudo apt-get install python-numpy
```

—End—

## 迭代


* 2016年10月23日 22:13 
添加`apt-cache depends` 



