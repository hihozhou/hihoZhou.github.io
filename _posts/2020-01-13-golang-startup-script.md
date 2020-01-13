---
layout: post
title: 为golang应用编写简单的启动停止脚本
date: 2020-01-13
categories: blog
tags: [golang,go]
description: 为golang应用编写简单的启动停止脚本

---

## 1、简单的启动脚本

1.1、编写启动脚本

```bash
vim start.sh
```

```bash
#!/usr/bin/env bash
go build  -o go_example main.go
nohup ./go_example > go_example.out 2>&1 &
```

- `go_example`为编译的执行文件
- `main.go`为项目入口文件
- `go_example.out`为日志输出文件

1.2、启动脚本添加运行权限

```bash
chmod u+x ./start.sh
```

### 2、编写停止脚本

2.1、编写停止脚本

```bash
vim stop.sh
```

```bash
#!/usr/bin/env bash
ps -ef | grep go_example | grep -v grep | awk '{print $2}' | xargs kill -9
```

2.2、停止脚本添加运行权限

```bash
chmod u+x ./stop.sh
```


## 使用

启动：

```bash
./start.sh
```

停止：

```bash
./stop.sh
```


![1.png](/source/images/golang-startup-script/1.png)


—End—

## 迭代

* 2020年01月13日 10：12 初稿

## 参考

