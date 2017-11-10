---
layout: post
title: php memcached扩展
date: 2015-05-18
categories: blog
tags: [php,memcached]
description: 编译安装使用memcached

---


注意: 本次讲解的系统为linux系统为ubuntu14.0,linux系统大同小异




[TOC]

## 安装memcache服务端(memcached)

可以到[memcached官网](http://memcached.org/downloads)下载 or 直接命令运行下载最新版本:

```bash
wget http://memcached.org/latest
```

安装memcache服务端前,需要先安装libevent

```bash
sudo apt-get install libevent-dev
```

编译安装

```bash
mkdir /usr/local/memcache
tar -zxvf memcache.tar.gz
cd memcached-1.4.25
./configure --prefix=/usr/local/memcached/
make && make install

```

启动memcached

```bash
cd /usr/local/memcached
./bin/memcached  -d -m 1024 -u root -l 127.0.0.1 -p 11211 -P /tmp/memcached.pid
```
检测是否已经启动

```bash
ps -aux | grep memcache
```


## 安装memcached客户端(php memcached扩展)
这个扩展需要» libmemcached客户端库（版本大于等于 1.0.0）。链接memcached服务器时使用SASL认证，需要libmemcached 必须开启SASL选项。
先安装libmemcached

```bash
wget https://launchpadlibrarian.net/165454254/libmemcached-1.0.18.tar.gz
tar -zxvf libmemcached-1.0.18.tar.gz
cd libmemcached-1.0.18/
./configure --prefix=/usr/local/libmemcached
make && make install
```


由于我之前已经编译安装过php了,所以现在只需要在不重新安装php的情况下添加memcache扩展

可以到[pecl官网下载](http://pecl.php.net/package/memcached),or 运行:

```bash
wget http://pecl.php.net/get/memcached-2.2.0.tgz
```

```bash
tar -axvf memcached-2.2.0.tgz
cd memcached-2.2.0/
/usr/local/php5/bin/phpize
--with-php-config=/usr/local/php5/bin/php-config --with-libmemcached-dir=/usr/local/libmemcached
make && make install
```

安装成功后会提示

```bash
----------------------------------------------------------------------
Libraries have been installed in:
   /home/hiho/Downloads/memcached-2.2.0/modules

If you ever happen to want to link against installed libraries
in a given directory, LIBDIR, you must either use libtool, and
specify the full pathname of the library, or use the `-LLIBDIR'
flag during linking and do at least one of the following:
   - add LIBDIR to the `LD_LIBRARY_PATH' environment variable
     during execution
   - add LIBDIR to the `LD_RUN_PATH' environment variable
     during linking
   - use the `-Wl,--rpath -Wl,LIBDIR' linker flag
   - have your system administrator add LIBDIR to `/etc/ld.so.conf'

See any operating system documentation about shared libraries for
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/local/php5/lib/php/extensions/no-debug-non-zts-20121212/
```

复制编译好的扩展文件到你php的扩展库目录

```bash
cp /usr/local/php5/lib/php/extensions/no-debug-non-zts-20121212/memcached.so /usr/local/php5/ext/
```

修改php.ini文件
添加`extension = memcached.so`

重启fpm OK


## php memcached使用例子

```php
$mem  = new Memcached();

$mem->addServer('127.0.0.1',11211);
if( $mem->add("mystr","this is a memcache test!",3600)){
    echo  '原始数据缓存成功!';
}else{
    echo '数据已存在：'.$mem->get("mystr");
}
```

详细请参考[php memcached官方文档](http://php.net/manual/zh/class.memcached.php)

—End—

## 迭代


* 2015年5月18日 08:09:12 初稿



