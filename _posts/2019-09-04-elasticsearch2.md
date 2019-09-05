---
layout: post
title: ElasticSearch（二）——中文分词器ik
date: 2019-09-04
categories: blog
tags: [ubuntu,ElasticSearch]
description: ElasticSearch

---


## 一、前言

为什么要在elasticsearch中要使用ik这样的中文分词呢，那是因为es提供的分词是英文分词，对于中文的分词就做的非常不好了，因此我们需要一个中文分词器来用于搜索和使用。


## 二、IK分词器的安装

IK分词插件[github地址](https://github.com/medcl/elasticsearch-analysis-ik)，找到release对应自己elasticsearch的版本
>我这里是7.3.1，所以我这里下载插件是v7.3.1

![1.png](/source/images/elasticsearch2/1.png)

```bash
# 进入elasticsearch目录
cd ~/Utils/elasticsearch-7.3.1
# 安装插件
./bin/elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.3.1/elasticsearch-analysis-ik-7.3.1.zip

```

![2.png](/source/images/elasticsearch2/2.png)

安装完成后，可以看到elasticsearch的`plugins`目录下多了一个`analysis-ik`目录

![3.png](/source/images/elasticsearch2/3.png)

重新启动`elasticsearch`，可以看到提示加载`analysis-ik`插件

![4.png](/source/images/elasticsearch2/4.png)

## 三、使用

https://www.cnblogs.com/zyrblog/p/9830624.html
https://blog.csdn.net/weixin_40341116/article/details/80630812




—End—

## 迭代

* 2019年09月04日 15：04 初稿

## 参考

