---
layout: post
title: Jekyll博客文章文件名不使用日期
date: 2017-06-29
categories: blog
tags: [jekyll,博客,github pages]
description: Jekyll Filename Without Date.

---

## 问题

最近想用Github Page搭建项目的文档，所以路径想要：  
`/doc/en/xxx`和`/doc/zh/xxx`，  
但是jeklly搭建博客规定：  

> 发表一篇新文章，你所需要做的就是在_posts文件夹中创建一个新的文件。 文件名的命名非常重要。  
Jekyll 要求一篇文章的文件名遵循下面的格：
`年-月-日-标题.MARKUP`  
例如:`2011-12-31-new-years-eve-is-awesome.md`。

## 问题解决

我分别尝试了在_posts下创建文件夹和文件名加上前缀都无法实现，最后google一下找到了方法：

1.不用_post目录，而是创建一个`_doc`文件夹,再分别在下面创建`en`和`zh`文件夹  

2.然后配置 `_config.yml`：  


```
collections:
  doc:
    output:         true
    permalink:      /:collection/:path
```

然后就可以在en和zh下编写说明文档，而访问的路径也是`/doc/en/xxx`和`/doc/zh/xxx`。

## 测试结果：


![result](/source/images/jekyll-filename-without-date/result.png)



—End—


## 迭代

* 2017年06月29日 14:36 初稿

## 参考文章
- [《jekyll-filename-without-date》](https://stackoverflow.com/questions/27099427/jekyll-filename-without-date)
- [《jekyllrb文档collections》](https://jekyllrb.com/docs/collections/)


