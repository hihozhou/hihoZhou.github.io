---
layout: post
title: go 无法下载golang.org/x/库问题 
date: 2019-09-05
categories: blog
tags: [ubuntu,网易云音乐]
description: Ubuntu18.04 安装 网易云音乐

---


## 问题

在编写go框架时候，在执行`go run main.go`后发现报错

```bash
go: downloading golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223
build command-line-arguments: cannot load golang.org/x/sys/unix: unrecognized import path "golang.org/x/sys" (https fetch: Get https://golang.org/x/sys?go-get=1: dial tcp 216.239.37.1:443: i/o timeout)
```

![1.png](/source/images/go-download-timeout/1.png)

这里大概提示的是尝试到golang.org/x/sys下载sys库，但是超时。

## 解决

使用go mod replace方法


从 Go 1.11 版本开始，新增支持了 go modules 用于解决包依赖管理问题。该工具提供了 replace，就是为了解决包的别名问题，也能替我们解决 golang.org/x 无法下载的的问题。

go module 被集成到原生的 go mod 命令中，但是如果你的代码库在 $GOPATH 中，module 功能是默认不会开启的，想要开启也非常简单，通过一个环境变量即可开启 export GO111MODULE=on。

`golang.org/x/`对应`github.com/golang/`

以我这里为例子`golang.org/x/sys`对应`github.com/golang/sys`

在`go.mod` 加上：

```bash
replace golang.org/x/sys v0.0.0-20190222072716-a9d3bda3a223 => github.com/golang/sys v0.0.0-20190813064441-fde4db37ae7a
```

![2.png](/source/images/go-download-timeout/2.png)

—End—

## 迭代

* 2019年09月05日 11：22 初稿

## 参考

- [《解决unknown import path "golang.org/x/sys/unix": unrecognized import path "golang.org/x/sys"》](https://www.cnblogs.com/sage-blog/p/10640947.html)

