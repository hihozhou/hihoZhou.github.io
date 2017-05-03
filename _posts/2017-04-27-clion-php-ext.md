---
layout: post
title: PHP扩展开发--使用clion配置
date: 2017-04-27
categories: blog
tags: [php,c,php扩展开发]
description: php扩展开发

---


##下载安装clion
到[官网](https://www.jetbrains.com/clion/)下载,并且安装

##使用clion配置开发php扩展
在之前我们已经了解过php扩展的开发流程，但是在实际应用中我们不只是写一个方法，而是要开发很多的文件组成的一个扩展，甚至一个框架。  
这是时候就不能用文本编辑器来编辑源码，而要使用到IDE。

##导入php扩展项目
1.点击clion菜单栏`File` -> `Import Project` 

![import project](http://7xpyze.com1.z0.glb.clouddn.com/php_extension_clion_import_1.png)

2.选择扩展目录 -> 选择项目所要的文件 -> 点击确认 

![import project](http://7xpyze.com1.z0.glb.clouddn.com/php_extension_clion_import_2.png)

因为clion是由CMakeList.txt文件进行，所以当导入后clion会自动生成基本的配置文件

```
#cmake最低要求版本
cmake_minimum_required(VERSION 3.5)
#项目命名
project(my_functions)
#设置C版本为c++ 11
set(CMAKE_CXX_STANDARD 11)
#设置项目源文件，导入项目时候选择的文件
set(SOURCE_FILES
        my_functions.c
        php_my_functions.h)
#添加可执行的目标到生成程序
add_executable(my_functions ${SOURCE_FILES})
```

##修改CMakeList.txt（代码提示）
```
#定义php源码路径，这里根据自己的真实路径来更改
set(PHP_SOURCE /home/hiho/Sources/php-7.0.15)
#引入php需要的扩展源码，这里也是根据自己需要的来更改
include_directories(${PHP_SOURCE}/main)
include_directories(${PHP_SOURCE}/Zend)
include_directories(${PHP_SOURCE}/sapi)
include_directories(${PHP_SOURCE}/pear)
include_directories(${PHP_SOURCE})
```

修改CMakeList.txt后会提示需要reload，按一下`Reload changes`

![import project](http://7xpyze.com1.z0.glb.clouddn.com/php_extension_clion_cmakelist_change.png)

刷新完后，就可以看见错误提示少了很多和很多地方都可以提示了
![import project](http://7xpyze.com1.z0.glb.clouddn.com/php_extension_clion_show.png)

现在你可以使用clion来编写php扩展代码了～

`但是还是有些地方还是报错，暂时不知道什么原因，后续知道再更新`



—End—

##迭代


* 2016年04月18日 11:14 初稿

##参考文章



