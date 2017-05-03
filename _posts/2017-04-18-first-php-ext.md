---
layout: post
title: PHP扩展开发--第一扩展（扩展开发流程）
date: 2017-04-18
categories: blog
tags: [php,c,php扩展开发]
description: php扩展开发

---


##想要实现内容
编写一个扩展，里面还有一个`hello_world`方法，传入一个数值n，调用后返回n个`Hello World！`字符串的拼接字符串。

##函数定义文件
为扩展建立函数的第一步是写一个函数定义文件，该函数定义文件定义了扩展对外提供的函数原形。该例中，定义函数只有一行函数原形

```
string hello_world(int n)
```

函数定义文件的一般格式是一个函数一行。你可以定义可选参数和使用大量的PHP类型，包括: bool, float, int, array等。
保存为`my_functions.def`文件至PHP原代码目录树下。

##利用官方工具ext_skel生成扩展框架

```
./ext_skel --extname=my_functions --proto=my_functions.def
```

运行后结果：
![ext_skel](http://7xpyze.com1.z0.glb.clouddn.com/php-extension-ext_skel.png)

生成的my_functions目录：
![my_functions_dir](http://7xpyze.com1.z0.glb.clouddn.com/php-extension-my-functions-files.png)

##PHP扩展动态编译与静态编译
###1.动态编译
动态编译就是指已经安装了系统已经安装了php，但是要有些扩展是后面才加上的，但有不想重新安装php（如自己编写的php扩展）。  
最后编译成`.so`文件，然后加到`php.ini`文件上
###2.静态编译
静态编译也就安装php的时候连带安装php扩展

##--enable-×××和--with-×××
- `--enable-×××`配置开关到PHP编译系统里
- `--with-×××` 开关用于那些需要用户指定相关C库路径的扩展

然后编辑`my_functions/config.m4`

```
PHP_ARG_WITH(my_functions, for my_functions support,
dnl Make sure that the comment is aligned:
[  --with-my_functions             Include my_functions support])

```




##修改扩展内容

##编译扩展

```bash
cd my_functions 
phpize
./configure --with-php-config=/usr/bin/php-config
make#已经编译生成.so扩展文件到modules目录下
make install#主要复制modules到系统扩展目录上/usr/lib/php/20151012

```

##添加编译后的扩展
make install后添加到 php.ini

```
extension="/usr/lib/php/20151012/myfunctions.so"
```



—End—

##迭代


* 2016年04月18日 11:14 初稿

##参考文章
- [《PHP扩展开发:第一个扩展》](http://kimi.it/496.html)
- [《用C/C++扩展你的PHP》](http://www.laruence.com/2009/04/28/719.html)



