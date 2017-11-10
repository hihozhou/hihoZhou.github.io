---
layout: post
title: git使用webhook实现自动更新
date: 2016-10-10
categories: blog
tags: [git,webhook]
description: git使用webhook实现自动更新

---



[TOC]

### 在git服务器中配置webhook的api url



### 编写脚本

```php
<?php

\\hook.php
error_reporting(7);
date_default_timezone_set('UTC');
$wwwRoot='/xx/wwwroot/';//需要更新的项目目录
$logFile='/xx/wwwroot/git-hook.log';//保存运行脚本的日志
$shell = sprintf("cd %s && /usr/bin/git pull origin dev:dev 2>&1", $wwwRoot);
$output = shell_exec($shell);
$log = sprintf("[%s] %s \n", date('Y-m-d H:i:s', time()), $output);
echo $log;
file_put_contents($logFile, $log, FILE_APPEND);exit;

```

### 权限问题

由于使用webhook出发回调访问的时候是,运行的是www-data用户,而项目目录是普通登录用户

修改项目目录权限
```bash
chown -R  www-data:www-data 项目路径
```

### 修改origin保存密码
由原来`https://username@url`改为`https://username:password@url`

```bash
git remote set-url origin ​
//如git remote set-url https://hihozhou:123@bitbucket.org/hihozhou/test.git
```


—End—



## 迭代


* 2016年10月10日 11:33:00 初稿


