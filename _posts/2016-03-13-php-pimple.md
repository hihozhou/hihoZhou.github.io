---
layout: post
title: Pimple —— 一个轻巧的PHP依赖注入容器
date: 2016-03-13
categories: blog
tags: [php,php pimple]
description: php pimple

---

注意:当前Pimple 3.x文档。 如果你用的是1.x版本， 请查看[Pimple 1.x 文档](https://github.com/silexphp/Pimple/tree/1.1)。 查看Pimple 1.x代码也是很好来学习更多关于如何创建一个简单的依赖注入容的方式(Pimple新版本更注重的是性能)。



[TOC]

Pimple是一个轻巧的PHP依赖注入容器。

##安装

在你项目中使用Pimple前,要先通过composer.json文件添加它:

```bash
$ ./composer.phar require pimple/pimple ~3.0
```

另外,Pimple也可以做为PHP C扩展:

```bash
$ git clone https://github.com/silexphp/Pimple
$ cd Pimple/ext/pimple
$ phpize
$ ./configure
$ make
$ make install
```

##使用

创建一个容器实例：

```php
use Pimple\Container;

$container = new Container();
```

像许多其他的依赖注入容器，Pimple管理两种不同类型的数据：服务和参数。


###定义服务
服务是庞大的系统中做一些工作的对象,服务的例子:一个数据库链接,一个模板引擎或邮件.几乎所有的全局对象都是服务  
通过匿名方法,并且返回对象的实例来定义服务

```php
// 定义一些服务
$container['session_storage'] = function ($c) {
    return new SessionStorage('SESSION_ID');
};

$container['session'] = function ($c) {
    return new Session($c['session_storage']);
};
```

注意,匿名发放中已经访问的容器实例,允许引用其他服务或参数。  
一个对象只有在获取的时候才会被创建,定义对象的顺序并不重要。

使用定义的服务也很容易：

```php
// get the session object
$session = $container['session'];

// 上面的调用大致相当于下面的代码：
// $storage = new SessionStorage('SESSION_ID');
// $session = new Session($storage);
```

###定义服务工厂
默认情况下,每次你获取一个服务,Pimple返回同一个实例给你.如果你想所有的调用返回不同的实例,可以使用factory()函数包装你的匿名方法.

```php
$container['session'] = $container->factory(function ($c) {
    return new Session($c['session_storage']);
});
```
现在,每次调用$container\['session'\]都会返回一个新的session实例对象.

###参数定义
定义一个参数轻松地从外部访问你容器的构造和储存全局值:

```php
// 定义一些参数
$container['cookie_name'] = 'SESSION_ID';
$container['session_storage_class'] = 'SessionStorage';
```
如果你更改`session_storage`服务定义如下:

```php
$container['session_storage'] = function ($c) {
    return new $c['session_storage_class']($c['cookie_name']);
};
```

你现在可以容易的通过重写`session_storage_class`参数更改`cookie_name`的值,而不是重新定义服务

###保护参数
因为Pimple用匿名方法定义服务,你需要用protect()方法包裹匿名方法来存储保护参数:

```php
$container['random_func'] = $container->protect(function () {
    return rand();
});
```

###修改定义后的服务
在某些情况下，您可能要修改它已定义的服务定义。在你的服务被创建后,你可以使用extend()方法添加额外的代码:

```php
$container['session_storage'] = function ($c) {
    return new $c['session_storage_class']($c['cookie_name']);
};

$container->extend('session_storage', function ($storage, $c) {
    $storage->...();

    return $storage;
});
```
第一个参数是需要扩展的服务名称,其次是一个获取对象实例和容器访问权限的方法.

###Extending a Container扩展一个容器
如果你重复的使用相同的库，你可能想从一个项目到另外一个项目重新使用一些服务;通过Pimple\ServiceProviderInterface实现封装你的服务到provider

```php
use Pimple\Container;

class FooProvider implements Pimple\ServiceProviderInterface
{
    public function register(Container $pimple)
    {
        // register some services and parameters
        // on $pimple
    }
}
```

Then, register the provider on a Container:  
然后,在容器中注册一个provider

```php
$pimple->register(new FooProvider());
```

###获取服务的创建方法
你访问一个对象时，Pimple会自动调用您定义的匿名函数，为您创建服务对象。如果你想获得原访问此方法，您可以使用raw()方法：


```php
$container['session'] = function ($c) {
    return new Session($c['session_storage']);
};

$sessionFunction = $container->raw('session');

```

#谁在支持Pimple?
Pimple是[Fabien Potencier](http://fabien.potencier.org/)写的,[Symfony框架](http://www.symfony.com/)的作者.Pimple是MIT许可下发布.


—End—

##迭代


* 2016年3月13日 22:39:12 初稿
* 2016年3月25日 22:39:12 翻译原稿


## 原文
- [《Pimple - A simple PHP Dependency Injection Container》](http://pimple.sensiolabs.org/)
