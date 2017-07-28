---
layout: post
title: PHP扩展开发中，用户传入闭包在C++回调事件中执行
date: 2017-07-28
categories: blog
tags: [php扩展,php扩展开发]
description: PHP扩展开发中，用户传入闭包在C++回调事件中执行

---

## 需求

在开PHPOpenCV扩展，在写php的`createTrackbar`方法的时候，需要实现用户传入的一个闭包，而闭包在C++`createTrackbar`滑动事件时候调用

结果如下：

```php
createTrackbar('trackBarName',"winName",100,200,function($num){
    //$num为滑动条选中值
});
```


## 实现

首先要获取用户传入闭包

```c++
char *trackbarname, *winname;
    long value, count, trackbarname_len,winname_len;
    zval retval;

    zend_fcall_info fci;
    zend_fcall_info_cache fci_cache;
    if (zend_parse_parameters(ZEND_NUM_ARGS(), "ssll|f",
                              &trackbarname, &trackbarname_len,
                              &winname,&winname_len,
                              &value,&count,
                              &fci, &fci_cache) == FAILURE) {
        return;
    }
```

用户传入的闭包保存在`fci`和`fci_cache`变量当中


c++ 版的`createTrackbar`最后可以值可以传入自己的值，从而在回调当中带入

所以我们要新建一个struct把fci和fci_cache包在一起

```php
struct opencv_fcall_info_cb{
    zend_fcall_info *fci;
    zend_fcall_info_cache *fci_cache;
};
```

结构体定义好了，我们需要定义他的生成方法，因为C++直接用"="复制，`fci`和`fci_cache`变量是方法中的局部变量，如果离开了方法就会释放，
我们要为传入的值创建内存，并且拷贝必要的信息（包括一些指针的值）

```c++
opencv_fcall_info_cb * opencv_fcall_info_cb_create(zend_fcall_info *fci_ptr, zend_fcall_info_cache *fci_cache_ptr) {
    opencv_fcall_info_cb *cb = new opencv_fcall_info_cb;
    cb->fci = new zend_fcall_info;
    cb->fci_cache = new zend_fcall_info_cache;

    memcpy(cb->fci, fci_ptr, sizeof(zend_fcall_info));
    memcpy(cb->fci_cache, fci_cache_ptr, sizeof(zend_fcall_info_cache));
    Z_TRY_ADDREF(cb->fci->function_name);
    cb->fci->param_count = 0;
    cb->fci->no_separation = 1;
    cb->fci->retval = NULL;

    return cb;
}

```

最后在方法中调用

```c++
opencv_fcall_info_cb *cb = opencv_fcall_info_cb_create(&fci, &fci_cache);
createTrackbar(trackbarname, winname, trackbar_value_ptr, (int)count,opencv_create_trackbar_callback,cb);
```

最后是`opencv_create_trackbar_callback`方法

```c++
void opencv_create_trackbar_callback(int pos, void* userdata){
    opencv_fcall_info_cb *fci_s=(opencv_fcall_info_cb*)userdata;

    zval retval;
    zval args[1];
    ZVAL_LONG(&args[0], (long)pos);//将滑动条滑动的值传入到闭包参数中
    fci_s->fci->param_count = 1;
    fci_s->fci->params = args;
    fci_s->fci->retval = &retval;

    zend_call_function(fci_s->fci, fci_s->fci_cache);
    zval_ptr_dtor(&args[0]);
}
```

—End—


## 迭代

* 2017年07月28日 10：41 初稿

## 参考

- [php-ion](https://github.com/php-ion/php-ion/blob/982f24bcc7b880488e392e45448247314a16ade6/src/ion/ion_callback.c)
