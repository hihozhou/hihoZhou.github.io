---
layout: post
title: laravel 生产模式优化
date: 2015-03-08
categories: blog
tags: [laravel,laravel5,laravel5优化]
description: 现在laravel 4在优化和性能的文档有些缺乏。这编文章,我将给出一点意见laravel 4是怎么工作的,和怎样提升其性能。

---



[TOC]

## 为何写这篇文章
　　现在laravel 在优化和性能的文档有些缺乏。这编文章,我将给出一点意见laravel是怎么工作的,和怎样提升其性能




## PHP普通优化

　　首先,这个实用于正常普通的PHP环境,也适用于laravel。

* 1、PHP的内置函数会比手工编写效率要高
* 2、你的数据库结构会影响你的性能等等  

## 使用composer管理优化
　　laravel使用自动懒加载类.这意味着它不会包含所有vendor目录下的所有文件,相反而是等待调用一个未被加载的类,然后对它可能所在的位置进行一些智能的猜测。    
　　如果你使用的psr-4自动加载，它会检查文件`Namespace/Namespace/Class.php`你在composer.json 的PSR-4 目录定义,如果你使用的是ClassMap,它就很容易找出类的名称和加载对应的文件.  
　　如果你添加了一个新的类到目录只会通过ClassMap被自动载入,这就是为什么，在你运行`composer dump-autoload`之前,会提示“class not found“错误.  
　　psr-4是不错的发展,因为它可以让你添加一个类，自动加载器会立即发现它，只要你命名空间命名和文件命名是正确的。当然，在生产环境中，你通常不想浪费一小部分性能在psr-4自动加载上。你可以运行`composer dump-autoload --optimize`重新编译你所有的psr-4自动加载规则到规则的ClassMap(vendor/composer/autoload_classmap.php)，它将会跑的更快。  

##l aravel优化
　　当`app/config/app.php`文件下的debug 设置为false时候,当运行laravel artisan命令`php artisan optimize`,它会做两件事情：

- 运行composer命令`composer dump-autoload --optimize`
- bootstrap/compiled.php 文件。该文件包含了很多laravel框架中常用的类,和允许的框架文件，只是文件系统需要一个框架，即使是在分裂为许多现实学习的文件。

　　当debug设置为true的时候`php artisan optimize`命令只会运行第一步,而不会生成bootstrap/compiled.php 文件.他会自动检测你的debug配置.  
　　另外,如果你不想改debug配置,但是也想生成bootstrap/compiled.php 文件,你可以在命令后面加上`--force`,`php artisan optimize --force` 强行生成  


　　强力推荐每次更新文件后运行`php artisan optimize`,它可以大大的提高性能。考虑让它每次你部署自动完成（以防止问题,最好是在临时运行`php artisan down`后）。  

　　你可以把特殊需要的类文件添加到app/config/compile.php中。文件需要引用相对与项目的跟目录，例如：

>'vendor/laravel/framework/src/Illuminate/Support/Collection.php',
'vendor/cartalyst/sentry/src/Cartalyst/Sentry/Sentry.php',
'app/library/HelperClass.php',


　　通过像 loic-sharma/profiler这样的性能分析工具,发现它们被加载到你的请求中,和加到compiled.php数组中,这样做,请求响应时间几乎减半


## 总结
* 1、`app/config/app.php` debug设置为false
* 2、把特殊需要的类文件添加到`app/config/compile.php`
* 3、运行命令`php artisan optimize`
* 4、运行命令`php artisan config:cache`
* 5、运行命令`php artisan route:cache`


—End—

## 迭代


* 2014年3月8日 19:09:17 初稿

## 参考文章
- [《laravel 4 for production server》](https://www.lutro.me/posts/optimizing-for-production-with-laravel-4)
- [《Laravel 5 optimization commands》](http://sentinelstand.com/article/laravel-5-optimization-commands)

