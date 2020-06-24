---
layout: post
title: mac下查看隐藏文件
date: 2020-06-24
categories: blog
tags: [mac,隐藏文件]
description: mac下查看隐藏文件
---

## 开启隐藏可见


```bash
defaults write com.apple.finder AppleShowAllFiles -bool true
```


## 关闭隐藏可见


```bash
defaults write com.apple.finder AppleShowAllFiles -bool false
```


>执行命令后，强制重启finder即可


—End—

## 迭代

* 2020年06月24日 12：02 初稿

## 参考

