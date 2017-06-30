---
layout: post
title: PHP扩展开发中，数组绑定到新的对象中并返回
date: 2017-06-30
categories: blog
tags: [php扩展,php扩展开发]
description: PHP扩展开发中，数组绑定到新的对象中并返回

---

## 需求

最近在做一个PHP扩展开发，想实现一个方法，方法主要是生成一个对象，并且对象中有一个数组属性，并返回这个新生成的对象


## 实现
首先要知道，php扩展中，创建一个新的数组使用`array_init`宏
php扩展中`array_init`中的定义

```c
//zend_API.h 第376行
#define array_init(arg)			_array_init((arg), 0 ZEND_FILE_LINE_CC)
```

然后像数组中添加元素可以使用`add_next_index_xxx`，如添加整形可以使用`add_next_index_long`

最后就是绑定到对象中，
由于ZEND_API中的`zend_update_property_xxx`并没有提供数组类型的，
我们可以直接使用`zend_update_property`

代码如下：

```c
zval array;//定义一个空的zval
array_init(&array);//zval初始化为数组类型的
add_next_index_long(&val,1);//想zval中的中value指向的数组添加与那数
add_next_index_long(&val,2);

zend_update_property(object_ce, getThis(), "myArr", sizeof("myArr")-1, &array);//添加到对象中

```

## 计数指针问题

上面写完后，逻辑是ok的，但是只有上面的代码，会发现内存泄漏问题`memory leaks detected`。  
这里主要是`array`在`array_init`的时候refcount=1,插入成员属性zend_update_property()会自动加一次，变为2，  
对象销毁后只会减1，需要要在zend_update_property()后主动减一次引用：`Z_DELREF(zval)`。  


```c
Z_DELREF(array);
```





—End—


## 迭代

* 2017年06月30日 17：47 初稿


