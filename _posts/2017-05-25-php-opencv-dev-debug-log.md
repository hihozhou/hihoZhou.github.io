---
layout: post
title: PHP7内核--变量的内部实现
date: 2017-05-12
categories: blog
tags: [php,php内核]
description: 

---

注：本文是参考[pangudashu](https://github.com/pangudashu)的[php7-internal](https://github.com/pangudashu/php7-internal)，自己加以理解和做出的学习笔记。


## 变量的内部实现
编程语言中的变量由两部分组成：变量名称（变量的定义）、变量值。PHP语言中对应的是：zval、zend_value，
这两个概念要区分开，PHP中变量的内存是通过引用计数进行管理的（这里所指的引用次数其实就是值变量在php中的引用次数），
在php7中引用计数的是`zend_value`而不是`zval`（在源码中zval结构体包含zend_value结构体,用于计算变量的引用次数的字段存放在zend_value）。

PHP中可以通过$关键词定义一个变量：$a;，在定义的同时可以进行初始化：$b = "hi~";，注意这实际是两步：定义、初始化，只定义一个变量也是可以的，可以不给它赋值，比如：

```php
$a;//只定义变量$a，但是不赋值，也就是没有定义zval中的zend_value
$b='hi~';//定义了$b,并且赋值为字符串hi～，也就是定义了zend_value
```

### 变量的基础结构（php扩展中变量的定义）

```c
//Zend/zend_type.h
typedef union _zend_value {
	zend_long         lval;				//int整形
	double            dval;				//浮点型
	zend_refcounted  *counted;
	zend_string      *str;              //string字符串
	zend_array       *arr;              //array数组
	zend_object      *obj;              //object对象
	zend_resource    *res;              //resource资源类型
	zend_reference   *ref;              //引用类型，通过&$var_name定义的
	zend_ast_ref     *ast;              //下面几个都是内核使用的value
	zval             *zv;              
	void             *ptr;              
	zend_class_entry *ce;              
	zend_function    *func;              
	struct {
		uint32_t w1;
		uint32_t w2;
	} ww;
} zend_value;

struct _zval_struct {
	zend_value        value;			//变量实际的value
	union {
		struct {
			ZEND_ENDIAN_LOHI_4(
				zend_uchar    type,			/* active type */
				zend_uchar    type_flags,
				zend_uchar    const_flags,
				zend_uchar    reserved)	    /* call info for EX(This) */
		} v;
		uint32_t type_info;
	} u1;
	union {
		uint32_t     var_flags;
		uint32_t     next;                 /* hash collision chain */
		uint32_t     cache_slot;           /* literal cache slot */
		uint32_t     lineno;               /* line number (for ast nodes) */
		uint32_t     num_args;             /* arguments number for EX(This) */
		uint32_t     fe_pos;               /* foreach position */
		uint32_t     fe_iter_idx;          /* foreach iterator index */
	} u2;
};

```

zval结构比较简单，内嵌一个union类型的zend_value保存具体变量类型的值或指针，zval中还有两个union：u1、u2:

- value：也就是zend_value，用于存储变量的真实内容
- u1：用来区分变量的类型
- u2：这个值纯粹是个辅助值，假如zval只有:value、u1两个值，整个zval的大小也会对齐到16byte，既然不管有没有u2大小都是16byte，把多余的4byte拿出来用于一些特殊用途还是很划算的，比如next在哈希表解决哈希冲突时会用到，还有fe_pos在foreach会用到......


### 2.1.2 类型
zval.u1.type类型：

```
/* regular data types */
#define IS_UNDEF                    0
#define IS_NULL                     1
#define IS_FALSE                    2
#define IS_TRUE                     3
#define IS_LONG                     4
#define IS_DOUBLE                   5
#define IS_STRING                   6
#define IS_ARRAY                    7
#define IS_OBJECT                   8
#define IS_RESOURCE                 9
#define IS_REFERENCE                10

/* constant expressions */
#define IS_CONSTANT                 11
#define IS_CONSTANT_AST             12

/* fake types */
#define _IS_BOOL                    13
#define IS_CALLABLE                 14

/* internal types */
#define IS_INDIRECT                 15
#define IS_PTR                      17
```

理论上使用`zval.u1.type==IS_NULL`可以进行判断zval变量是的类型，但是加以使用`Z_TYPE_P`宏判断

```
Z_TYPE_P(zval) == IS_NULL
```


#### 2.1.2.1 标量类型（没有zend_value指针的类型）

最简单的类型是true、false、long、double、null，其中true、false、null没有value，直接根据type区分，而long、double的值则直接存在value中：zend_long、double，也就是标量类型不需要额外的`value指针`。
- true/false/null：zend_value没有值，type为IS_TRUE/IS_FALSE/IS_NULL
- long/double：zend_value为zend_long/double，type为IS_LONG/IS_DOUBLE

#### 2.1.2.2 非标量类型（zend_value指针类型）
相对与标量类型就是zend_value实例是指针类型,指针类型变量都含有一个`zend_refcounted_h`的结构体


##### 2.1.2.2.1 字符串
PHP中字符串通过zend_string表示:

```
struct _zend_string {
    zend_refcounted_h gc;
    zend_ulong        h;                /* hash value */
    size_t            len;
    char              val[1];
};
```

- gc： 变量引用信息，比如当前value的引用数，所有用到引用计数的变量类型都会有这个结构，3.1节会详细分析
- h： 哈希值，数组中计算索引时会用到
- len： 字符串长度，通过这个值保证二进制安全
- val： 字符串内容，变长struct，分配时按len长度申请内存,为什么这里用`val[1]`有待研究

事实上字符串又可具体分为几类：

- IS_STR_PERSISTENT(通过malloc分配的)
- IS_STR_INTERNED(php代码里写的一些字面量，比如函数名、变量值)
- IS_STR_PERMANENT(永久值，生命周期大于request)
- IS_STR_CONSTANT(常量)、IS_STR_CONSTANT_UNQUALIFIED


这个信息通过flag保存：zval.value->gc.u.flags，后面用到的时候再具体分析。

##### 2.1.2.3 数组
array是PHP中非常强大的一个数据结构，它的底层实现就是普通的有序HashTable，这里简单看下它的结构，下一节会单独分析数组的实现。

```
typedef struct _zend_array HashTable;  //给_zend_array定义吧别名为HashTable

struct _zend_array {
	zend_refcounted_h gc;   //引用计数信息，与字符串相同
	union {
		struct {
			ZEND_ENDIAN_LOHI_4(
				zend_uchar    flags,
				zend_uchar    nApplyCount,
				zend_uchar    nIteratorsCount,
				zend_uchar    reserve)
		} v;
		uint32_t flags;
	} u;
	uint32_t          nTableMask;
	Bucket           *arData;
	uint32_t          nNumUsed;
	uint32_t          nNumOfElements;
	uint32_t          nTableSize;
	uint32_t          nInternalPointer;
	zend_long         nNextFreeElement;
	dtor_func_t       pDestructor;
};
```

—End—


## 迭代

* 2017年05月12日 18:03 初稿

## 参考文章
[《与 UNIX 构建系统交互: config.m4(php官方手册)》](http://php.net/manual/zh/internals2.buildsys.configunix.php)


