---
layout: post
title: php-fpm优化
date: 2016-09-26
categories: blog
tags: [php-fpm,fpm,fpm优化]
description: php-fpm optimize

---


[TOC]

# 用到命令
```bash
free -m #查看内存使用情况
top 
```

# 背景
最近公司收购一家公司,原是ucloud服务商的服务器,然后着手搬迁到腾讯云上.  
总用户量在100W左右,平常还是没什么问题,但是一到了微信公众帐号推送的时候就会一直报警.

奇怪的就是原来服务器推送报警都不会那么严重,搬迁后报警次数增长了太多,注:新服务器硬件性能比原服务器硬件性能要好  
新旧服务器对题:


|服务器     | cpu  |内存 | 硬盘    |系统|
|:---      | :--- | :--| :--    |:--|
|新(腾讯云) | 4核   | 8G |  云盘   | Ubuntu 14.04 64位|
|旧(ucloud)| 4核   |6G  |物理机械硬盘|Ubuntu 14.04 64位|

服务器比原服务器要好,但是表现的性能却不如以前.

# 排查

nginx  
一开始我怀疑是不是nginx的问题,但是发现两者的配置发现是几乎一样的,因为迁移的时候nginx尽量配置为旧服务器一样
所以问题应该不是在nginx上

然后就到fpm
想了想,然后记得我fpm是没有修改过儿和配置的,只是运行`sudo apt-get install php5-fpm`安装后直接启动
然后用ps aux | grep fpm 查看,发现了巨大的错误了

fpm默认配置:

```
pm = dynamic

; The number of child processes to be created when pm is set to 'static' and the
; maximum number of child processes when pm is set to 'dynamic' or 'ondemand'.
; This value sets the limit on the number of simultaneous requests that will be
; served. Equivalent to the ApacheMaxClients directive with mpm_prefork.
; Equivalent to the PHP_FCGI_CHILDREN environment variable in the original PHP
; CGI. The below defaults are based on a server without much resources. Don't
; forget to tweak pm.* to fit your needs.
; Note: Used when pm is set to 'static', 'dynamic' or 'ondemand'
; Note: This value is mandatory.
pm.max_children = 5

; The number of child processes created on startup.
; Note: Used only when pm is set to 'dynamic'
; Default Value: min_spare_servers + (max_spare_servers - min_spare_servers) / 2
pm.start_servers = 2

```

法相fpm默认启动3个进程

# 配置fpm参数进行优化

### 需要配置的参数

- pm : 表示使用那种方式，有两个值可以选择，就是static（静态）或者dynamic（动态）。
      在更老一些的版本中，dynamic被称作apache-like。这个要注意看配置文件的说明。
- pm.max_children：静态方式下开启的php-fpm进程数量
- pm.start_servers：动态方式下的起始php-fpm进程数量
- pm.min_spare_servers：动态方式下的最小php-fpm进程数
- pm.max_spare_servers：动态方式下的最大php-fpm进程数量

### 区别
如果pm设置为 static，那么其实只有pm.max_children这个参数生效。系统会开启设置数量的php-fpm进程。  
如果pm设置为 dynamic，那么pm.max_children参数失效，后面3个参数生效。  
系统会在php-fpm运行开始 的时候启动pm.start_servers个php-fpm进程，  
然后根据系统的需求动态在pm.min_spare_servers和pm.max_spare_servers之间调整php-fpm进程数  

### 如何配置
对于我们的服务器，选择哪种执行方式比较好呢？事实上，跟Apache一样，运行的PHP程序在执行完成后，或多或少会有内存泄露的问题。  
这也是为什么开始的时候一个php-fpm进程只占用3M左右内存，运行一段时间后就会上升到20-30M的原因了。  
对于内存大的服务器（比如8G以上）来说，指定静态的max_children实际上更为妥当，因为这样不需要进行额外的进程数目控制，会提高效率。 
因为频繁开关php-fpm进程也会有时滞，所以内存够大的情况下开静态效果会更好。数量也可以根据 内存/30M 得到，比如8GB内存可以设置为100， 
那么php-fpm耗费的内存就能控制在 2G-3G的样子。如果内存稍微小点，比如1G，那么指定静态的进程数量更加有利于服务器的稳定。 
这样可以保证php-fpm只获取够用的内存，将不多的内存分配给其他应用去使用，会使系统的运行更加畅通。  
对于小内存的服务器来说，比如256M内存的VPS，即使按照一个20M的内存量来算，10个php-cgi进程就将耗掉200M内存，那系统的崩溃就应该很正常了。  
因此应该尽量地控制php-fpm进程的数量，大体明确其他应用占用的内存后，给它指定一个静态的小数量，会让系统更加平稳一些。或者使用动态方式，  
因为动态方式会结束掉多余的进程，可以回收释放一些内存，所以推荐在内存较少的服务器或VPS上使用。具体最大数量根据 内存/20M 得到。  
比如说512M的VPS，建议pm.max_spare_servers设置为20。至于pm.min_spare_servers，则建议根据服务器的负载情况来设置，比如服务器上只是部署php环境的话，比较合适的值在5~10之间。  


# 优化后的参数

100*25M=2500M  
pm = static  
pm.max_children = 100  
pm.max_requests = 1000  




—End—



## 迭代


* 2016年09月26日 16:15:00 初稿

## 参考文章
- [《linux free详解》](http://hihozhou.github.io/blog/2016/09/25/linux-free.html)
- [《ubuntu14.*系统 php-fpm重启bug》](http://www.linuxidc.com/Linux/2016-07/133682.htm)
