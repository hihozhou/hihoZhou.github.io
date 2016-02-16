---
layout: post
title: Composer自动加载(autoload)
date: 2015-12-12
categories: blog
tags: [composer,composer自动加载]

---

注：运行 Composer 需要 PHP 5.3.2+ 以上版本。一些敏感的 PHP 设置和编译标志也是必须的，但对于任何不兼容项安装程序都会抛出警告。  
本人系统上已将composer全局安装,使用的`composer ...`命令，正常应该使用`php /composer path/composer.phar ...`，具体请看[composer全局安装](http://docs.phpcomposer.com/00-intro.html#Downloading-the-Composer-Executable)





[TOC]

##1、简介

Composer 是PHP的一个包依赖管理工具，类似Ruby中的RubyGems或者Node中的NPM，它并非官方，但现在已经非常流行。
此文介绍Composer的autoload的内容吧。


##composer.json声明依赖关系
项目中的composer是由`composer.json`文件来配置的

举例来说,加入我们在项目中用到[monolog库](https://github.com/Seldaek/monolog),我们需要在composer.json里告诉composer我们需要它：  

- 方法1.你可以手动生成名为`composer.json`文件,或者在项目中已有的`composer.json`的`require`配置加上`"monolog/monolog": "^1.17"`

```json
{
  "require": {
    "monolog/monolog": "1.2.*"
  }
}
```
然后运行`composer install`或`composer update`

- 方法2.你可以直接运行  

```
composer require "monolog/monolog:1.2.*"
```

注意:我们只要指出我们的项目需要一些 monolog/monolog 的包，从 1.2 开始的任何版本。  
方法2通过composer命令帮你运行方法1的操作,本人更推荐方法1的操作。  


##composer库使用
好，现在安装完了，该怎么使用呢?  
Composer自动生成了一个autoload文件，你只需要引用它

然后就可以非常方便的去使用第三方的类库了，是不是感觉很棒啊！
例如我们创建一个monologTest.php文件
对于我们需要的monolog，就可以这样用了：

```php
/**
 * monologTest.php
 * User: hiho
 */
 
/**
 * require '/path/to/vendor/autoload.php';
 * 如果不引入autoload.php文件会报Fatal error: Class 'Monolog\Logger' not found错误
 */
require_once "vendor/autoload.php";

use Monolog\Logger;
use Monolog\Handler\StreamHandler;

// create a log channel
$log = new Logger('name');
$log->pushHandler(new StreamHandler('/path/to/logs/log_name.log', Logger::WARNING));

// add records to the log
$log->addWarning('Foo');
$log->addError('Bar');
```

现在用composer管理的框架(如Yii2.0,laravel等)都会在框架的入口文件添加引入`autoload.php`文件,所以在你使用这些框架的时候只需要添加你需要的库就可以了使用了.  
o(∩∩)o...哈哈,有没有立马爱上的感觉.



—End—

##迭代


* 2016年02月09日 14:22:17 初稿



