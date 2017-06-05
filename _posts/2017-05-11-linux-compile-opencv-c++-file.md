---
layout: post
title: linux下编译C++写的opencv程序
date: 2017-05-11
categories: blog
tags: [C++,opencv,linux,clion]
description: c++ opencv程序编译

---


## C++编译
linux下C++的编译命令是：

```bash
g++ xxx.cpp -o xxx
#e.g:
g++ main.cpp -o main

```


## C++ opencv程序编译出错
我刚装玩opencv3,然后立马写了个测试的程序

```c++
#include <iostream>

#include <opencv2/core/core.hpp>
#include <opencv2/highgui/highgui.hpp>

using namespace cv;

int main() {
    Mat myMat= imread("/home/hiho/Desktop/111.png");
    std::cout << "Hello, World!1" << std::endl;
    return 0;
}
```

运行命令`g++ main.cpp -o main`

发现编译并没有成功，报`undefined reference to cv::imread`错误
![compile fail](/source/images/linux-compile-opencv-c++-file/compile_fail.png)

原来是C++编译的时候没有引入opencv的源码库，所以只需要在编译的时候带上就好了，
命令为`pkg-config --cflags --libs opencv`

- `--cflags` 参数可以给出在编译时所需要的选项
- `--libs` 参数可以给出连接时的选项

把命令加上opencv的编译选项和链接选项就可以编译成功

```bash
g++ main.cpp -o main `pkg-config --cflags --libs opencv`
./main
```


## Clion调试
如果使用Clion IDE的时候，想直接点击`run`就自动引入opencv链接和编译的库

### 配置项目下的`CMakeLists.txt`文件
由于clion使用`CMakeLists.txt`来配置cmake进行编译，我们只需要在`CMakeLists.txt`加上两项配置即可

- `find_package( OpenCV REQUIRED )`找到opencv源
- `target_link_libraries( DisplayImage ${OpenCV_LIBS} )`添加opencv链接选项

例如：

```cmake
cmake_minimum_required(VERSION 3.5)#cmake版本
project( DisplayImage )#项目名称
find_package( OpenCV REQUIRED )#找到opencv源
add_executable( DisplayImage main.cpp )#
target_link_libraries( DisplayImage ${OpenCV_LIBS} )#添加opencv链接选项
```

—End—


## 迭代

* 2017年05月11日 15:14 初稿
* 2017年06月05日 12:22 修改Clion调试

## 参考文章

- [《Using OpenCV with Eclipse (plugin CDT)》](http://docs.opencv.org/3.0-last-rst/doc/tutorials/introduction/linux_eclipse/linux_eclipse.html)
- [《Using OpenCV with gcc and CMake》](http://docs.opencv.org/2.4/doc/tutorials/introduction/linux_gcc_cmake/linux_gcc_cmake.html)


