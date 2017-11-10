---
layout: post
title: php函数笔记
date: 2014-06-15
categories: blog
tags: [php,函数]
description: php函数。

---


## property_exists
(PHP 5 >= 5.1.0, PHP 7)  
`property_exists` — 检查对象或类是否具有该属性  
本函数检查给出的 property 是否存在于指定的类中（以及是否能在当前范围内访问）。

>bool [property_exists](http://php.net/manual/zh/function.property-exists.php) ( mixed $class , string $property )


## get_class
(PHP 4, PHP 5, PHP 7)  
`get_class` — 返回对象的类名  
返回对象实例 obj 所属类的名字。如果 obj 不是一个对象则返回 FALSE。

>string [get_class](http://www.php.net/manual/zh/function.get-class.php) (\[ object $obj \] )



## class_parents
(PHP 5 >= 5.1.0, PHP 7)  
`class_parents` — 返回指定类的父类  
本函数返回一个包含了指定类class父类名称的数组。

>array [class_parents](http://php.net/manual/zh/function.class-parents.php) ( mixed $class \[, bool $autoload \] )

例子

```php
class foo {}
class bar extends foo {}
class baz extends bar {}

print_r(class_parents(new baz));
/*
Will output:
Array
(
    [bar] => bar
    [foo] => foo
)
*/

```

—End—

## 迭代


* 2014年6月15日 19:09:17 初稿



