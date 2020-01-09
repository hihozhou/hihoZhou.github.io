---
layout: post
title: 使用docker部署golang应用
date: 2020-01-09
categories: blog
tags: [golang,go,docker]
description: 使用docker部署golang应用

---

## 构建golang docker应用部署

## 简单的web服务

这里我用gin框架先写一个简单的web服务，新建一个文件夹`gin-example`

使用go module包管理，并引入gin

```bash
go mod init
go get -u github.com/gin-gonic/gin
```

main.go

```bash
package main

import "github.com/gin-gonic/gin"

func main() {
	r := gin.Default()
	r.GET("/", func(c *gin.Context) {
		c.JSON(200, gin.H{
			"message": "This is gin-example first page.",
		})
	})
	r.Run() // listen and serve on 0.0.0.0:8080 (for windows "localhost:8080")
}

```

启动

```bash
go run main.go
```

访问127.0.0.1:8080

![1.png](/source/images/golang-docker/1.png)




## 一、简单编写部署文件

1.编写应用的dockerfile文件

```dockerfile
FROM golang:latest

ENV GOPROXY https://goproxy.cn,direct
WORKDIR $GOPATH/src/github.com/hihozhou/gin-example
COPY . $GOPATH/src/github.com/hihozhou/gin-example
RUN go build .
EXPOSE 8080
ENTRYPOINT ["./gin-example"]

```


2.构建镜像

```bash
docker build -t hihozhou/gin-example .
```


```bash
➜  gin-example docker build -t hihozhou/gin-example .
Sending build context to Docker daemon  17.41kB
Step 1/7 : FROM golang:latest
 ---> dc7582e06f8e
Step 2/7 : ENV GOPROXY https://goproxy.cn,direct
 ---> Using cache
 ---> ae7e7fe8b504
Step 3/7 : WORKDIR $GOPATH/src/github.com/hihozhou/gin-example
 ---> Running in bf7dad26283d
Removing intermediate container bf7dad26283d
 ---> 3800126c2d9f
Step 4/7 : COPY . $GOPATH/src/github.com/hihozhou/gin-example
 ---> 052ed0e99957
Step 5/7 : RUN go build .
 ---> Running in 276496234dea
go: downloading github.com/gin-gonic/gin v1.5.0
go: extracting github.com/gin-gonic/gin v1.5.0
go: downloading github.com/mattn/go-isatty v0.0.11
go: downloading gopkg.in/yaml.v2 v2.2.7
go: downloading github.com/gin-contrib/sse v0.1.0
go: extracting github.com/mattn/go-isatty v0.0.11
go: downloading github.com/ugorji/go v1.1.7
go: extracting github.com/gin-contrib/sse v0.1.0
go: extracting gopkg.in/yaml.v2 v2.2.7
go: downloading gopkg.in/go-playground/validator.v9 v9.31.0
go: downloading github.com/golang/protobuf v1.3.2
go: downloading golang.org/x/sys v0.0.0-20200107162124-548cf772de50
go: extracting gopkg.in/go-playground/validator.v9 v9.31.0
go: downloading github.com/leodido/go-urn v1.2.0
go: downloading github.com/go-playground/universal-translator v0.17.0
go: extracting github.com/golang/protobuf v1.3.2
go: extracting github.com/go-playground/universal-translator v0.17.0
go: downloading github.com/go-playground/locales v0.13.0
go: extracting github.com/ugorji/go v1.1.7
go: downloading github.com/ugorji/go/codec v1.1.7
go: extracting github.com/ugorji/go/codec v1.1.7
go: extracting golang.org/x/sys v0.0.0-20200107162124-548cf772de50
go: extracting github.com/leodido/go-urn v1.2.0
go: extracting github.com/go-playground/locales v0.13.0
go: finding github.com/gin-gonic/gin v1.5.0
go: finding github.com/gin-contrib/sse v0.1.0
go: finding github.com/golang/protobuf v1.3.2
go: finding github.com/ugorji/go/codec v1.1.7
go: finding gopkg.in/go-playground/validator.v9 v9.31.0
go: finding github.com/go-playground/universal-translator v0.17.0
go: finding github.com/go-playground/locales v0.13.0
go: finding github.com/leodido/go-urn v1.2.0
go: finding gopkg.in/yaml.v2 v2.2.7
go: finding github.com/mattn/go-isatty v0.0.11
go: finding golang.org/x/sys v0.0.0-20200107162124-548cf772de50
Removing intermediate container 276496234dea
 ---> 1349feec93f1
Step 6/7 : EXPOSE 8080
 ---> Running in 7e933d7d58da
Removing intermediate container 7e933d7d58da
 ---> 494a751c904f
Step 7/7 : ENTRYPOINT ["./gin-example"]
 ---> Running in 3b3ca3f5a34b
Removing intermediate container 3b3ca3f5a34b
 ---> 31fe35f3b71a
Successfully built 31fe35f3b71a
Successfully tagged hihozhou/gin-example:latest

```


3.使用`docker images`查看构建的镜像

![2.png](/source/images/golang-docker/2.png)


4.运行镜像

```bash
docker run -p 8080:8080 hihozhou/gin-example
```

这里-p物理机器端口与docker端口的映射，`-p 物理机器端口:docker端口`，`docker端口`与编写dockerfile时候`EXPOSE`对应，物理机器端口你可以自行指定

然后访问`http://127.0.0.1:8080`，也能正常访问了

## 二、构建最小的运行镜像

当运行`docker ps -a`时候会发现一个这么简单的golang web服务，居然也占用了886MB
其实 FROM golang:latest 拉取的是官方 golang 镜像，包含Golang的编译和运行环境，外加一堆GCC、build工具，相当齐全

这是有问题的，我们可以不在Golang容器中现场编译的，压根用不到那些东西，我们只需要一个能够运行可执行文件的环境即可

1.编译可执行文件

```bash
CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -o go-gin-example .
```

编译所生成的可执行文件会依赖一些库，并且是动态链接。在这里因为使用的是 scratch 镜像，它是空镜像，因此我们需要将生成的可执行文件静态链接所依赖的库

2.重新编写dockerfile

```dockerfile
FROM scratch

WORKDIR $GOPATH/src/github.com/hihozhou/gin-example
COPY . $GOPATH/src/github.com/hihozhou/gin-example

EXPOSE 8080
CMD ["./gin-example"]
```

3.构建镜像
docker build -t hihozhou/gin-example-scratch .

![3.png](/source/images/golang-docker/3.png)

可以对比发现，scratch构建的镜像只有15MB


—End—

## 迭代

* 2020年01月09日 11：22 初稿

## 参考

