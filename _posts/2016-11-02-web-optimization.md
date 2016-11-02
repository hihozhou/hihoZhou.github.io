---
layout: post
title: web前端优化
date: 2016-11-02
categories: blog
tags: [web优化]
description: web前端优化

---

##通过DOM去获取一个element是很费时的操作

在一个页面中有一个id为body的输入框

```html
<!DOCTYPE html>
<html>
<head>
</head>
<body>
<input type="text" id="body" value="1">

</body>
</html>
```

测试代码为:

```js
//测试代码1
let body = $('#body');
for (let i = 0; i < 100000; i ++) {
    body.val();
}

//测试代码2
for (let i = 0; i < 100000; i++) {
    let body = $('#body');
    body.val();
}
```

代码1测试结果截图:
![web-optimization-test-1.png](http://7xpyze.com1.z0.glb.clouddn.com/web-optimization-test-1.png)
代码2测试结果截图:
![web-optimization-test-2.png](http://7xpyze.com1.z0.glb.clouddn.com/web-optimization-test-2.png)


可以对比两段代码测试结果截图,第一段代码大概在170毫秒左右,然而第二段代码大概耗时在350毫秒+,差不多是两倍.  
这时候有人就疑问说"第一段代码和第二段代码最影响可能是来至定义变量放在循环里面和外面的区别!"  
OK,我们继续拿第一端代码更改对比

```
//测试代码3
let body = $('#body');
for (let i = 0; i < 100000; i ++) {
    body.val();
}


//测试代码4
let body1 = $('#body');
for (let i = 0; i < 100000; i ++) {
    let body = body1;//重复定义变量
    body.val();
}
```

代码3测试结果截图:
![web-optimization-test-3.png](http://7xpyze.com1.z0.glb.clouddn.com/web-optimization-test-3.png)
代码4测试结果截图:
![web-optimization-test-4.png](http://7xpyze.com1.z0.glb.clouddn.com/web-optimization-test-4.png)

可以看出,在for循环里面重复定义变量的影响不大.


—End—

##迭代


* 2016年11月02日 15:42 初稿,添加`通过DOM去获取一个element是很费时的操作` 



