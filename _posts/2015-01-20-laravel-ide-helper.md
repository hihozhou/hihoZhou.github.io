---
layout: post
title: PhpStorm下Laravel代码智能提示
date: 2015-01-20
categories: blog
tags: [laravel,laravel phpstrom提示]
description: 写不是义务，写本身就是写的报酬。

---


　　PhpStorm是一款不错的php IDE，最新版本已经支持php7，Laravel是我用过最好的框架，不过悲伤的是PhpStrom不支持最好的Laravel的代码提示，后来我在github上翻，发现了 [laravel-ide-helper](https://github.com/barryvdh/laravel-ide-helper)，用到现在一直很给力

代码自动补全流程

##安装

进到laravel的项目中用composer命令下载提示所需的扩展库

```bash
composer require barryvdh/laravel-ide-helper
```

等composer下载完后,在`config/app.php`中的`providers`数组中添加

```bash
Barryvdh\LaravelIdeHelper\IdeHelperServiceProvider::class,
```

##生成提示文件

用laravel Facades生成自动提示phpDoc,运行命令

```bash
php artisan ide-helper:generate
```

注意:首先要先清除`bootstrap/compiled.php`,因此在运行命令生成提示前先运行`php artisan clear-compiled`(生成后再运行`php artisan optimize`).

命令运行完后会提示`A new helper file was written to _ide_helper.php`,它将会在你项目跟目录下生成一个`_ide_helper.php`文件.  
OK,现在你的PhpStrom已经可以对laravel代码自动提示了

—End—

##迭代


* 2015年1月20日 13:04:17 初稿



