---
layout: post
title: PHP扩展下的config.m4
date: 2017-05-12
categories: blog
tags: [php扩展,config.m4]
description: config.m4

---


## config.m4
config.m4是unix下构建系统的配置文件，可以告诉系统configure 选项是支持的，php扩展中就用到config.m4。
请看[php扩展关于config.m4的官方文档](http://php.net/manual/zh/internals2.buildsys.configunix.php)

## 我的config.m4例子
这里我需要用到opencv c++的编译和链接库，我先使用pkg-config查找对应opencv配置，然后在加入到config.m4中



```
dnl $Id$
dnl config.m4 for extension opencv

PHP_ARG_WITH(opencv, for opencv support,
dnl Make sure that the comment is aligned:
[  --with-opencv             Include opencv support])

dnl $PHP_OPENCV=yes
dnl 动态编译的时候总是yes（动态编译是装完php后，在单独编译扩展）
dnl 静态编译要选择--with-opencv/--enable-opencv后为yes（静态编译是安装php时候一起编译安装该扩展）
if test "$PHP_OPENCV" != "no"; then
    dnl 使用c++编译
    PHP_REQUIRE_CXX()
    PHP_ADD_LIBRARY(stdc++, 1, OPENCV_SHARED_LIBADD)
    PHP_SUBST(OPENCV_SHARED_LIBADD)

    dnl 指定编译的文件
    PHP_NEW_EXTENSION(opencv, opencv.cc car.cc, $ext_shared,, -DZEND_ENABLE_STATIC_TSRMLS_CACHE=1)

    dnl 检测pkg-config
    AC_MSG_CHECKING(for pkg-config)
    if test ! -f "$PKG_CONFIG"; then
          dnl 如何没有找到则用命令`which pkg-config`的结果赋值给PKG_CONFIG
          PKG_CONFIG=`which pkg-config`
    fi

    dnl 再次检测pkg-config命令路径
    if test -f "$PKG_CONFIG"; then
          AC_MSG_RESULT(found)
          AC_MSG_CHECKING(for opencv)
          dnl 使用命令pkg-config --exists opencv检测是否存在opencv
          if $PKG_CONFIG --exists opencv; then
              if $PKG_CONFIG --atleast-version=3.2.0 opencv; then
                  opencv_version=`$PKG_CONFIG --modversion opencv`
                  AC_MSG_RESULT([found $opencv_version])
                  OPENCV_LIBS=`$PKG_CONFIG --libs opencv`
                  OPENCV_INCS=`$PKG_CONFIG --cflags opencv`
                  PHP_EVAL_INCLINE($OPENCV_INCS)
                  PHP_EVAL_LIBLINE($OPENCV_LIBS, OPENCV_SHARED_LIBADD)
                  AC_DEFINE(HAVE_OPENCV, 1, [whether opencv exists in the system])
              else
                  dnl opencv版本太旧
                  AC_MSG_RESULT(too old)
                  AC_MSG_ERROR(Ooops ! You need at least opencv 3.2.0)
              fi
          else
              dnl 系统找不到opencv
              AC_MSG_RESULT(not found)
              AC_MSG_ERROR(Ooops ! no opencv detected in the system)
          fi
    else
          dnl 找不到则报错退出
          AC_MSG_RESULT(not found)
          AC_MSG_ERROR(Ooops ! no pkg-config found .... )
    fi



fi

```

原理就是和编译是添加参数一样

```bash
g++ main.cpp -o main `pkg-config --cflags --libs opencv`
```


## php用config.m4编译的坑处
如果你的php扩展需要用到其他动态库进行编译或链接，那么这个时候需要用到config.m4进行配置。  
但是需要注意，要修改一下扩展的代码，make才会重新对配置重新加载编译。  
不然还是会未定义符号报错`php: symbol lookup error: /path/to/modules/myext.so: undefined symbol: otherlib_some_function`




—End—


## 迭代

* 2017年05月12日 18:03 初稿

## 参考文章
[《与 UNIX 构建系统交互: config.m4(php官方手册)》](http://php.net/manual/zh/internals2.buildsys.configunix.php)


