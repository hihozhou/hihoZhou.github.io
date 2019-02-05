---
layout: post
title: Window系统上使用C++编写php扩展报LNK2005错误
date: 2019-02-05
categories: blog
tags: [c++,windows,php扩展]
description:  Window系统上使用C++编写php扩展报LNK2005错误

---

## 问题

在window上用c编写扩展，昨天想编写一个测试的exception对象，分别新建了`my_function_exception.h`和`my_function_exception.c`

`my_function_exception.h`内容为：

```
#ifndef MY_FUNCTION_EXCEPTION_H
#define MY_FUNCTION_EXCEPTION_H
#include "php_my_function.h"

extern zend_class_entry *my_function_exception_ce;


extern void my_function_exception_init(int module_number);


extern void my_function_exception_init(int module_number);


void opencv_throw_exception(const char *error_message);


#endif //MY_FUNCTION_EXCEPTION_H

```

`my_function_exception.c`内容为：

```
#include "my_function_exception.h"

zend_class_entry *my_function_exception_ce;

void my_function_exception_init(int module_number)
{
    zend_class_entry ce;
    INIT_NS_CLASS_ENTRY(ce, "MyFunction", "Exception", NULL);//注册CV\Exception 对象
    my_function_exception_ce = zend_register_internal_class_ex(&ce, zend_exception_get_default());
}


void opencv_throw_exception(const char *error_message)
{
    int status = 1;
    zend_throw_exception(my_function_exception_ce, error_message, status);
    return;
}

```




然后进行编译，可以编译成功，但由于我是需要c++支持，所以讲文件换成`.cpp`后缀再重新编译，发现报`LNK2005`和`LNK1169`错误

![1.png](/source/images/window-php-extension-cxx-error/1.png)


## 搜查资料

1.测试发现，只要文件`my_function_exception.h`头部文件引入`my_function.h`文件就会编译错误。  
2.然后询问很多做扩展开发的网友查了很多资料也没有搜索到，有网友所这是重复引用问题，需要理清楚引用，
但是我看很多扩展都这样引用，而且`my_function.h`是直接用工具生成。  
3.翻查了很多github上的扩展，要么就是c，要么是c++编写但不支持window系统，要么就是单个文件。


## 解决

然后尝试了很多方法捣鼓了一整天还是编译失败，后来我试一试`非debug`模式进行编译，居然编译成功了，
然后我查看了编译配置的`configure.js`文件，发现`debug`模式和`非debug`模式编译参数是不同的

![2.png](/source/images/window-php-extension-cxx-error/2.png)

后来测试发现只需要在config.w32文件添加编译配置即可

```
ADD_FLAG("CFLAGS_MY_FUNCTION", "/D ZEND_WIN32_FORCE_INLINE /GF");
```

![3.png](/source/images/window-php-extension-cxx-error/3.png)


然后就可以编译成功了。

—End—

## 迭代

* 2019年02月05日 10：47 初稿

## 参考

