---
layout: post
title: php mcrypt
date: 2016-03-13
categories: blog
tags: [php,php mcrypt]
description: php mcrypt

---





[TOC]

## 1、mcrypt简单介绍

　　PHP程序员们在编写代码程序时，除了要保证代码的高性能之外，还有一点是非常重要的，那就是程序的安全性保障。PHP除了自带的几种加密函数外，还有功能更全面的PHP加密扩展库Mcrypt和Mhash。
　　其中，Mcrypt扩展库可以实现加密解密功能，就是既能将明文加密，也可以密文还原。  
　　mcrypt 是 php 里面重要的加密支持扩展库，linux环境下：该库在默认情况下不开启。window环境下：PHP>=5.3,默认开启mcrypt扩展。  
　　Mcrypt库支持20多种加密算法和8种加密模式，具体可以通过函数mcrypt_list_algorithms()和mcrypt_list_modes()来显示

## 2、php mcrypt库依赖
要使用该扩展，必须首先安装mcrypt标准类库，注意的是mcrypt软件依赖libmcrypt和mhash两个库。
![依赖关系图](http://img.my.csdn.net/uploads/201211/20/1353390822_1764.jpg)

所以安装的的顺序是:

- 安装libmcrypt库[(下载地址)](http://sourceforge.net/project/showfiles.php?group_id=87941&package_id=91774&release_id=487459)
- 安装mhash库[(下载地址)](http://sourceforge.net/project/showfiles.php?group_id=87941&package_id=91948&release_id=642101)
- 安装mcrypt库[(下载地址)](http://sourceforge.net/project/showfiles.php?group_id=4286&package_id=4300&release_id=645636)
- 编译php mcrypt扩展库

## 3、安装libmcrypt库

```bash
tar -zxvf libmcrypt-2.5.8.tar.gz
cd libmcrypt-2.5.8/
./configure --prefix=/usr/local/libmcrypt-2.5.8/
make
make && make install
```


## 3、安装mhash库

```bash
tar -zxvf mhash-0.9.9.9.tar.gz
cd mhash-0.9.9.9/
./configure --prefix=/usr/local/mhash-0.9.9.9/
make
make install
```

## 4、安装mcrypt库

```bash
tar -zxvf mcrypt-2.6.8.tar.gz 
cd mcrypt-2.6.8/
LD_LIBRARY_PATH="/usr/local/libmcrypt-2.5.8/lib:/usr/local/mhash-0.9.9.9/lib" LDFLAGS="-L/usr/local/mhash-0.9.9.9/lib -I/usr/local/mhash-0.9.9.9/include" CFLAGS="-I/usr/local/mhash-0.9.9.9/include" ./configure --prefix=/usr/local/mcrypt-2.6.8/ --with-libmcrypt-prefix=/usr/local/libmcrypt-2.5.8
make
make install
```

说明：由于在配置Mcrypt时，会找不到libmcrypt和mhash的链接库，导致无法编译。  
因为Libmcrypt的链接库在`/usr/local/libmcrypt-2.5.8/lib`文件夹下，mhash的链接库在`/usr/local/mhash-0.9.9.9/lib`
  
而LDFLAGS和CFLAGS是为了解决下面问题:

```bash
checking for libmcrypt - version >= 2.5.0... yes
checking for mhash_keygen in -lmhash... yes
checking whether mhash >= 0.8.15... no
configure: error: "You need at least libmhash 0.8.15 to #compile this program. http://mhash.sf.net/"
```

## 5、编译php mcrypt扩展库(动态加载)

```bash
cd ~/Downloads/php-5.5.9/ext/mcrypt/
/usr/local/php5/bin/phpize
./configure --with-php-config=/usr/local/php5/bin/php-config
make && make install
```

编译完后会提示:

```bash
Installing shared extensions:     /usr/local/php5/lib/php/extensions/no-debug-non-zts-20121212/
hiho@hiho-Aspire-E1-471G:~/Downloads/php-5.5.9/ext/mcrypt$ ./configure --with-php-config=/usr/local/php5/bin/php-config
```

复制编译好的mcrypt扩展到php的扩展目录

```bash
cp /usr/local/php5/lib/php/extensions/no-debug-non-zts-20121212/mcrypt.so /usr/local/php5/ext/
```

修改php.ini文件

```
extension=mcrypt.so
```

重启fpm,OK!

—End—

## 迭代


* 2016年3月13日 22:39:12 初稿



