---
layout: post
title: 使用jenkins搭建gitlab持续集成CI和持续部署CD（二）
date: 2019-08-14
categories: blog
tags: [ubuntu,gitlab,jenkins]
description: 使用jenkins搭建gitlab持续集成CI和持续部署CD（二）

---


## 测试执行远程服务器脚本

有时候我们需要完成ci后到远程服务器上执行脚本
例如，ci结束后，自动到测试服务器更新服务并且重启，这样就能一键提交，测试，更新部署

- 1.开发人员push到仓库
- 2.gitlab通过webhook通知jenkins
- 3.jenkins接收到webhook请求后执行测试
- 4.执行本地自定义脚本（一些单元测试，启动测试等）
- 5.执行远程自定义脚本（远程测试服务器拉去代码，重启服务）


### 1.安装ssh插件

搜索ssh，并勾选安装
![1.png](/source/images/gitlab-jenkins2/1.png)
![2.png](/source/images/gitlab-jenkins2/2.png)


### 2.添加ssh认证

在jenkins首页 点击 `Credentials` 进入如下页面
![3.png](/source/images/gitlab-jenkins2/3.png)



### 3.配置远程链接

`Manage Jenkins`--> `configure system`--->`SSH remote hosts`即行配置
![4.png](/source/images/gitlab-jenkins2/4.png)


### 4.添加远程脚本

点击项目 --> `Configure` --> `Build` --> `Add build step` --> `Execute shell script on remote host using ssh`

![5.png](/source/images/gitlab-jenkins2/5.png)

选择链接选项，并且输入执行的命令即可
![6.png](/source/images/gitlab-jenkins2/6.png)


### 5.运行结果

测试结果
![7.png](/source/images/gitlab-jenkins2/7.png)


—End—

## 迭代

* 2019年08月014日 10：29 初稿

## 参考


