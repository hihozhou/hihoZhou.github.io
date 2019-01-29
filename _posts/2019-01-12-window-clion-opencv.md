---
layout: post
title: Window系统上配置Clion编写OpenCV
date: 2019-01-12
categories: blog
tags: [c++,windows,clion,opencv]
description: Window系统上配置Clion编写OpenCV

---

## clion配置c++编译器
我们编写的opencv程序需要使用编译器进行编译，网上有些教程配置`Cygwin`或`MinGW`,
我这里有个比较简单的方法，直接使用VS安装好对应的编译器，  

### 1.利用VS安装编译环境
在`Visual Studio Professional 2017`下选择修改后，选在对应的环境安装
![1.png](/source/images/window-clion-opencv/1.png)

### 2.配置clion编译器
2.1打开clion，点击右上角`File`->`Setting`，搜索`Toolchains`并选择`Toolchains`，然后选择Visual Studio
![2.png](/source/images/window-clion-opencv/2.png)

如果选择了`x86`就是32位编译器，后面配置cmake则会报错
```bash
-- OpenCV ARCH: x86
-- OpenCV RUNTIME: vc14
-- OpenCV STATIC: OFF
CMake Warning at D:/opencv/build/OpenCVConfig.cmake:156 (message):
  Found OpenCV Windows Pack but it has no binaries compatible with your
  configuration.

  You should manually point CMake variable OpenCV_DIR to your build of OpenCV
  library.
Call Stack (most recent call first):
  CMakeLists.txt:8 (FIND_PACKAGE)


CMake Error at CMakeLists.txt:8 (FIND_PACKAGE):
  Found package configuration file:

    D:/opencv/build/OpenCVConfig.cmake

  but it set OpenCV_FOUND to FALSE so package "OpenCV" is considered to be
  NOT FOUND.


-- Configuring incomplete, errors occurred!
```

2.2 配置CMakeLists.txt文件
打开项目的中的`CMakeLists.txt`，添加配置

```bash
cmake_minimum_required(VERSION 3.13)
project(test_project)

set(CMAKE_CXX_STANDARD 11)

add_executable(test_project main.cpp)
set(OpenCV_DIR D:/opencv/build)
FIND_PACKAGE(OpenCV REQUIRED)
target_link_libraries(test_project ${OpenCV_LIBS})
```

- 1.OpenCV_DIR后对应自己的opencv目录下build，我的opencv安装在D判断所以对应的是`D:/opencv/build`
- 2.test_project为项目的名称，可以对应修改

### 测试
测试代码：

```
#include <iostream>
#include <opencv2/core.hpp>
#include <opencv2/highgui.hpp>

using namespace cv;

int main() {
    Mat mat=cv::imread("C:\\Users\\hiho\\Desktop\\test.jpg");
    imshow("test",mat);
    waitKey(0);
    return 0;
}
```

可以直接run编译运行对应的项目
![3.png](/source/images/window-clion-opencv/3.png)

运行结果：
![4.png](/source/images/window-clion-opencv/4.png)
—End—


## 迭代

* 2019年01月12日 19：58 初稿

## 参考

