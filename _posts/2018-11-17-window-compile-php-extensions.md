---
layout: post
title: Window下编写php扩展和编译扩展成dll
date: 2018-09-27
categories: blog
tags: [php扩展,window,dll]
description: window下编写php以及编译php扩展dll

---

## 概述

我平常开发php扩展是在linux系统上，而扩展都是采用编译安装方式添加扩展，
但是由于开发php-opencv扩展的的时候有扩展的使用者问我能不能提供window的扩展dll引用文件。
所以最近在考虑支持window平台，总结一下window下开发php扩展和编译成dll的经验，

## 官方文档
其实php官方是有提供扩展编译成window的dll的[官方文档](https://wiki.php.net/internals/windows/stepbystepbuild_sdk_2)，
当时没细看官方文档，自己白捣鼓了几天。

## 1.环境准备
这里要细说一下环境，在官方文档中说到的环境要求，Visual Studio环境可以直接用Visual Studio ide进行安装，
之前一直以为是指Visual Studio C++ ，所以运行`phpsdk-vc15-x64`会出现`Could not deternine 'vc15' directory`的错误，
php编译成dll的环境可以使用这个ide进行安装

### 1.1安装Visual Studio（IDE）
到[官网下载](https://visualstudio.microsoft.com/)Visual Studio Install

### 1.2 添加编译环境
在`Visual Studio Professional 2017`下选择修改后，选在对应的环境安装
![1.png](/source/images/window-compile-php-extensions/1.png)
这样需要的环境就已经准备好了

## 2.编译前准备，设置构建目录

### 2.1 php-sdk-binary-tools
[php-sdk-binary-tools](https://github.com/Microsoft/php-sdk-binary-tools)是有官方提供的工具，  
1.下载php-sdk-binary-tools

```bash
git clone https://github.com/Microsoft/php-sdk-binary-tools
cd php-sdk-binary-tools
```

2.调用启动脚本，例如我这里是php7.2，对应Visual Studio 2017 64位版本，调用phpsdk-vc15-x64.bat，
运行会发现cmd窗口开头会变成`$`符号。
![2.png](/source/images/window-compile-php-extensions/2.png)

3.运行phpsdk_buildtree批处理脚本，该脚本将创建所需的目录结构：

```bash
phpsdk_buildtree phpdev
```
![3.png](/source/images/window-compile-php-extensions/3.png)
会发现运行后在`php-sdk-binary-tools`中多出一个`phpdev`的目录
![4.png](/source/images/window-compile-php-extensions/4.png)

phpsdk_buildtree脚本将根据当前使用的VC ++版本创建路径，并切换到新创建的目录

4.将PHP源代码解压缩到，其中：`F:\www\php-sdk-binary-tools\phpdev\vc##\x##`，
在解压缩PHP源的同一目录中，有一个deps目录。
- `vc##`是您正在使用的编译器版本（eq vc15）
- `x##`是你的构建的系统位数（x86或x64）
- 例如我这里是： `F:\www\php-sdk-binary-tools\phpdev\vc15\x64\php-7.2.12-src`
![5.png](/source/images/window-compile-php-extensions/5.png)

5.进入解压的php源码目录，运行`phpsdk_deps -u`或`phpsdk_deps --update --branch master`，php会自动下载 需要的依赖包(php7以前需要手动下载php-dsp文件)

![6.png](/source/images/window-compile-php-extensions/6.png)
![7.png](/source/images/window-compile-php-extensions/7.png)

## 3.编译php

### 3.1 构建配置的环境
上面已经将编译前的构建准备环境好了，编译前在php-sdk-binary-tools中调用starter脚本以自动设置所需构建配置的环境`phpsdk-vc15-x64.bat`，

### 3.2 执行buildconf
切换到`php源码根目录`，然后执行`buildconf`命令。
如果提示`输入错误: 没有文件扩展“.js”的脚本引擎。`
![8.png](/source/images/window-compile-php-extensions/8.png)

这里主要原因是：系统安装IDE（Dreamwear、UltraEdit、EditPlus，我之前安装phpstorm修改了）后后修改了.js文件的默认打开方式。  
当想直接执行js脚本时就会出现此错误。  
快捷键`win + r`，然后输入`regedit`,打开注册表编辑器，定位[HKEY_CLASSES_ROOT.js]这一项，双击默认值将其改为“JSFile”即可。如图所示：
![9.png](/source/images/window-compile-php-extensions/9.png)
![10.png](/source/images/window-compile-php-extensions/10.png)
![11.png](/source/images/window-compile-php-extensions/11.png)

然后就可以执行成功了

![12.png](/source/images/window-compile-php-extensions/12.png)



### 3.3 编译配置

执行以下命令进行编译php配置：

```bash
configure --disable-all  --enable-cli
```

注意：可以自己适当调整，参数可以使用`configure --help`查看，如
```bash
configure --disable-all  --enable-cli --enable-debug --with-all-shared
```

执行成功后会提示执行`nmake`
![13.png](/source/images/window-compile-php-extensions/13.png)

执行这一步会在`main`目录下生成`config.w32.h`文件，编译`php扩展`会需要用到这个文件，
所以如果是编译php扩展则执行下一步`nmake`，如果是只是php编译扩展可以不执行`nmake

### 3.4 执行nmake编译
然后执行`nmake` 开始编译

等待数分钟后,在php-src 目录应该够找到能够多了一个`编译位数的目录`（x32或x64），在目录下有 Release_TS (或者 Release_NTS 或者 Debug_TS ) 这样的目录

![14.png](/source/images/window-compile-php-extensions/14.png)

如果编译正常的话 这下面会有 php.exe 文件 证明编译php文件成功.

运行编译好的 `php.exe -v`查看是否正常运行
![15.png](/source/images/window-compile-php-extensions/15.png)


## 4.编译php扩展

### 4.1 创建php扩展
如果我们只是编译某些已经开放好的扩展源码，可以不用运行这一步，这里创建一个demo扩展进行编译。    
使用`git bash`进入到`源码的地址\ext`目录，执行:

```bash
php.exe ext_skel_win32.php --extension=my_function
```  
注：这里为什么要使用git bash工具，是因为git bash自带模拟linux环境，`my_function`为我这里需要创建的扩展，具体可以根据自己更改  
执行工具创建时不是提示`'sh' is not recognized as an internal or external command,`  
![16.png](/source/images/window-compile-php-extensions/16.png)

`git bash`执行结果:
![17.png](/source/images/window-compile-php-extensions/17.png)  

可以看到在源码ext目录下多了一个`my_function`的目录，这个就是我们刚刚使用使用`ext_skel_win32.php`命令出创建的初始扩展代码

![18.png](/source/images/window-compile-php-extensions/18.png)  

### 4.2 修改源码
1.我们编辑`my_function`下的`my_function.c`文件。
添加一个方法my_function_test方法，代码如下：
```c
PHP_FUNCTION(my_function_test) {
   php_printf("This is my function PHP extension! \n");
}

```

2.然后将`PHP_FE(my_function_test, NULL)`添加到`const zend_function_entry my_function_functions[]`中，但要放在`PHP_FE_END`前：
![22.png](/source/images/window-compile-php-extensions/22.png)



### 4.3 编译扩展
切换回window的cmd

1.进入`php-sdk-binary-tools`目录使用`phpsdk-vc15-x64.bat`  
2.切换到php源码目录，运行`buildconf`  
3.在执行完`buildconf`后如果执行`configure`命令，会提示类似以下错误：  

![19.png](/source/images/window-compile-php-extensions/19.png)
  
打开提示的文件，找到对应的行数，发现结尾多出多余的注释符号。这里可能是工具的问题，
![20.png](/source/images/window-compile-php-extensions/20.png)

解决方法：打开扩展源码的`config.w32`，找到`// Otherwise, use ARG_ENABLE`这句注释删除，重新运行`buildconf`就可以了。
![21.png](/source/images/window-compile-php-extensions/21.png)



## 迭代

* 2018年11月17日 15：45 初稿
* 2018年11月20日 22：26 添加最后编译php步骤

## 参考

- [《php官方window编译php手册》](https://wiki.php.net/internals/windows/stepbystepbuild_sdk_2)
- [《windows 下编译php7.2 极其扩展 judy》](https://blog.csdn.net/tangshangkui/article/details/79840748?utm_source=blogxgwz6)
- [《没有文件扩展“.js”的脚本引擎问题解决》](https://blog.csdn.net/ctthuangcheng/article/details/16951361)