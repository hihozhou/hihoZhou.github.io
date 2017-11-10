---
layout: post
title: php添加opcache
date: 2016-03-02
categories: blog
tags: [php,opcache]

---

注：

- 本人之前已经安装了php5.5.9版本,所以只需要编译添加opcache扩展就可以了
- 如果需要将 » Xdebug 扩展和 OPcache 一起使用，必须在 Xdebug 扩展之前加载 OPcache 扩展。





[TOC]

## 简介
OPcache 通过将 PHP 脚本预编译的字节码存储到共享内存中来提升 PHP 的性能， 存储预编译字节码的好处就是 省去了每次加载和解析 PHP 脚本的开销。

作用概括就是:OpCache 的加速原理是把编译后的 bytecode 存储在内存里面, 避免重复编译 PHP 所造成的资源浪费.

PHP 5.5.0 及后续版本中已经绑定了 OPcache 扩展。 对于 PHP 5.2，5.3 和 5.4 版本可以使用 » PECL 扩展中的 OPcache 库。


在php5.5.0+版本,php中已经自带有opcache扩展,我们只需要编译,然后添加扩展和配置就可以了
如果你第一次安装php,那么你只需要在.Configure 配置的时候添加"--enable-opcache"参数,
而我们今天在不重新编译情况下添加opcache扩展并且配置使用

## 1、添加opcache扩展

编译opcache扩展

```bash
cd ~/Downloads/php-5.5.9/ext/opcache
/usr/local/php5/bin/phpize 
./configure --with-php-config=/usr/local/php5/bin/php-config
make && make install
```

编译完成后会提示

```bash
Installing shared extensions:     /usr/local/php5/lib/php/extensions/no-debug-non-zts-20121212/
```

复制编译好的opcache扩展到php的扩展目录

```bash
cp /usr/local/php5/lib/php/extensions/no-debug-non-zts-20121212/opcache.so /usr/local/php5/ext/
```
修改php.ini文件

```
zend_extension=opcache.so
; 开关打开
opcache.enable=1

; 可用内存, 酌情而定, 单位 megabytes
opcache.memory_consumption=256

; 对多缓存文件限制, 命中率不到 100% 的话, 可以试着提高这个值
opcache.max_accelerated_files=5000

; Opcache 会在一定时间内去检查文件的修改时间, 这里设置检查的时间周期, 默认为 2, 定位为秒
opcache.revalidate_freq=240
```

保存后重启fpm

ab测试实际性能对比：

未启用opcache前

```bash
hiho@hiho-Aspire-E1-471G:~$ ab -n 10000 -c 200 "http://azooo.localhost.com/WechatWeb/Test/test"
This is ApacheBench, Version 2.3 <$Revision: 1528965 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking azooo.localhost.com (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:        nginx/1.6.3
Server Hostname:        azooo.localhost.com
Server Port:            80

Document Path:          /WechatWeb/Test/test
Document Length:        79 bytes

Concurrency Level:      200
Time taken for tests:   131.947 seconds
Complete requests:      10000
Failed requests:        40
   (Connect: 0, Receive: 0, Length: 40, Exceptions: 0)
Non-2xx responses:      40
Total transferred:      4211780 bytes
HTML transferred:       808320 bytes
Requests per second:    75.79 [#/sec] (mean)
Time per request:       2638.930 [ms] (mean)
Time per request:       13.195 [ms] (mean, across all concurrent requests)
Transfer rate:          31.17 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.4      0       4
Processing:    36 2352 4066.4   1586   63070
Waiting:       36 2352 4066.4   1586   63069
Total:         40 2352 4066.5   1586   63072

Percentage of the requests served within a certain time (ms)
  50%   1586
  66%   1810
  75%   2517
  80%   2541
  90%   2726
  95%   4520
  98%   5724
  99%   8795
 100%  63072 (longest request)

```

启用了opcache后

```bash
hiho@hiho-Aspire-E1-471G:~$ ab -n 10000 -c 200 "http://azooo.localhost.com/WechatWeb/Test/test"
This is ApacheBench, Version 2.3 <$Revision: 1528965 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking azooo.localhost.com (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:        nginx/1.6.3
Server Hostname:        azooo.localhost.com
Server Port:            80

Document Path:          /WechatWeb/Test/test
Document Length:        79 bytes

Concurrency Level:      200
Time taken for tests:   105.562 seconds
Complete requests:      10000
Failed requests:        59
   (Connect: 0, Receive: 0, Length: 59, Exceptions: 0)
Non-2xx responses:      59
Total transferred:      4217315 bytes
HTML transferred:       817022 bytes
Requests per second:    94.73 [#/sec] (mean)
Time per request:       2111.245 [ms] (mean)
Time per request:       10.556 [ms] (mean, across all concurrent requests)
Transfer rate:          39.01 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.4      0       4
Processing:    27 1654 4497.8    994   63014
Waiting:       27 1653 4497.8    994   63014
Total:         30 1654 4498.0    994   63016

Percentage of the requests served within a certain time (ms)
  50%    994
  66%   1012
  75%   1024
  80%   1047
  90%   1984
  95%   2189
  98%   5859
  99%  13570
 100%  63016 (longest request)

```

## 总结
由于php语言动态解析的特性，使用opcache和apc等opcode缓存是很有必要的。

—End—

## 迭代


* 2016年3月2日 14:22:17 初稿



