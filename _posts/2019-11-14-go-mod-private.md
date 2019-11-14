---
layout: post
title: go mod配置私有仓库
date: 2019-11-14
categories: blog
tags: [go,go mod,go mod私有仓库]
description: go mod配置私有仓库

---


## 问题

如果项目引用公共仓库直接`go get`即可，但如果是公司项目一定用到私有仓库
如果不配置直接go get 私有仓库会发现404的情况

![1.png](/source/images/go-mod-private/1.png)

以下以gitlab为例子

## 配置GOPROXY
在 Go 1.13 版本的发布前，设置 `GOPROXY` 只能指定一个代理服务地址。进入 Go 1.13 版本后，`GOPROXY` 支持多代理设置`,`通过,隔开即可。如下:

```bash
go env -w GOPROXY=https://goproxy.io,direct
```

按官方文档的说明，当第一个`proxy`在处理`ge get`所发出的HTTP请求时，返回HTTP状态码为`404`或`410`时，就会查找下一个`proxy`。

## 配置GOPRIVATE

If your Go version >= 1.13, the GOPRIVATE environment variable controls which modules the go command considers to be private (not available publicly) and should therefore not use the proxy or checksum database. For example:

```bash
# Set environment variable allow bypassing the proxy for selected modules
go env -w GOPRIVATE=*.corp.example.com
```

也就是当你设置`GOPRIVATE`的配置会让go忽略goproxy和gosumdb校验，直接走回源


—End—

## 迭代

* 2019年11月14日 11：22 初稿

## 参考

- [《请问如何让 go mod 对某些私有 module 跳过 GOPROXY 代理？》](https://www.golangtc.com/t/5d00d209b17a82478bd860c5)

