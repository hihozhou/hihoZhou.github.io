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

### 更改www用户登录权限

有时候，我们需要登录www-data用户进行一些shell操作，例如安装npm等，注意：这里在正式环境上不建议
由于本人项目中使用npm打包，并且使用nvm对node管理，
所以测试环境上自动跟新，需要对www-data用户允许登录和运行shell安装node和nvm。



```bash
sudo vim /etc/passwd
```

找到`www-data`那一行，
eg:
```
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
```
可以发现www-data用户是不允许登录的

如果登录root再切换www-data会提示`This account is currently not available.`
```bash
su root
su www-data
This account is currently not available.
```

然后我们将`/usr/sbin/nologin`改为`/bin/bash`，然后`:wq`保存退出，就可以切换www-data用户



—End—



## 迭代


* 2016年10月10日 11:33:00 初稿


