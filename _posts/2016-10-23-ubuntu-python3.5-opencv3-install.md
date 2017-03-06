---
layout: post
title: ubunut安装opencv3+python3
date: 2016-10-23
categories: blog
tags: [linux,ubuntu,python3.5,opencv3]
description: ubuntu16.04安装python3.5+opencv3。

---

OpenCV 已经发布到了3.0版本，最近Python也发布了3.5，之前的应用都还是基于 OpenCV 2.X 和 Python 2.7。

Ubuntu 14.04/16 64bit

```bash
sudo apt-get install python3-setuptools python3-dev
sudo easy_install3 pip
```

然后是 `numpy`：

```bash
pip3 install numpy
```

安装依赖：

```bash
sudo apt-get install build-essential
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
```

下载 OpenCV 3.0，编译安装：

```bash
wget https://github.com/Itseez/opencv/archive/3.0.0.zip

unzip 3.0.0.zip && cd 3.0.0
mkdir build && cd build

cmake -D CMAKE_BUILD_TYPE=Release \
-D CMAKE_INSTALL_PREFIX=/usr/local  \
PYTHON3_EXECUTABLE=/usr/bin/python3 \
PYTHON_INCLUDE_DIR=/usr/include/python3.4 \
PYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.4m.so \
PYTHON3_NUMPY_INCLUDE_DIRS=/usr/local/lib/python3.4/dist-packages/numpy/core/include ..

make -j4
sudo make install

```

测试一下：

```bash
python3.5
>>> import cv2
>>> cv2.__version__
'3.2.0'
>>> 
```


—End—

##迭代


* 2016年10月23日 22:13  初稿



