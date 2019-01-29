---
layout: post
title: Window系统上使用C++编写php扩展
date: 2019-01-12
categories: blog
tags: [c++,windows,php扩展]
description: Window系统上使用C++编写php扩展

---

## 配置config.w32
如果直接在原来为c的扩展中直接引用C++语法会报错：
```
fatal error C1189: #error: The C++ Standard Library forbids macroizing keywords. Enable warning C4005 to find the forbidden macro.
```
![1.png](/source/images/window-php-extension-cxx/1.png)

这里我们配置`config.w32`文件，将

```
EXTENSION("my_function", "my_function.cc");
```

改为：

```
EXTENSION("my_function", "my_function.cc",null,"/D _XKEYCHECK_H /D /EHsc");
```


## 修改扩展源码
1.找到`my_function.cc`文件将`#include "php.h"`用`extern "C" {}`包住：

```
#ifdef HAVE_CONFIG_H
#include "config.h"
#endif
extern "C" {
    #include "php.h"
}
#include "php_ini.h"
#include "ext/standard/info.h"
#include "php_my_function.h"

```

2.在`php_my_function.h`添加`#include <iostream>`
![2.png](/source/images/window-php-extension-cxx/2.png)

3.添加测试方法
![3.png](/source/images/window-php-extension-cxx/3.png)

## 测试

重新编译扩展后，运行结果：
![4.png](/source/images/window-php-extension-cxx/4.png)
—End—

## 迭代

* 2019年01月28日 13：11 初稿

## 参考

