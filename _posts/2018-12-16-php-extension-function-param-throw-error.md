---
layout: post
title: PHP扩展编写方法时，如果传入参数错误直接抛出错误
date: 2018-12-16
categories: blog
tags: [php扩展,方法]
description: PHP扩展编写方法时，如果传入参数错误直接抛出错误

---

## PHP扩展方法获取参数传入

在编写php扩展方法的时候，有时候我们需要为函数定义传入的参数

```c++
if (zend_parse_parameters(ZEND_NUM_ARGS(), "lll|O", &rows, &cols, &type, &scalar_zval,opencv_scalar_ce) == FAILURE) {
        RETURN_NULL();
}
```


这种写法没有什么问题，但是如果参数传入错误的时候，例如上面需要至少3个参数，
这时候如果缺少第三个参数，只会报一个warning，并且返回NULL值（构造函数返回一个空对象）。
```bash
PHP Warning:  CV\Mat::__construct() expects at least 3 parameters, 2 given in xxxxxxxx on line xx
```

这样并不利于程序开发，所以我们想要让程序如果传入参数有误就直接抛出`PHP Fatal error: `

## 改造

可以使用`zend_parse_parameters_ex`，自定义`flag`

```c++
if (zend_parse_parameters_ex(ZEND_PARSE_PARAMS_THROW, ZEND_NUM_ARGS(), "lll|O", &rows, &cols, &type, &scalar_zval,opencv_scalar_ce) == FAILURE) {
        RETURN_NULL();
}
```

然后缺少第三个字段会直接抛出错误

```bash
PHP Fatal error:  Uncaught ArgumentCountError: CV\Mat::__construct() expects at least 3 parameters, 2 given in /home/hiho/www/tutorial-demo/core/basic_ops/basic_ops.php:28
Stack trace:
#0 /home/hiho/www/tutorial-demo/core/basic_ops/basic_ops.php(28): CV\Mat->__construct(2, 2)
#1 {main}
  thrown in /home/hiho/www/tutorial-demo/core/basic_ops/basic_ops.php on line 28

```

—End—


## 迭代

* 2018年12月16日 15：55 初稿

## 参考

