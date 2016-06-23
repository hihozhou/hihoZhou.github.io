---
layout: post
title: php cli进程起名
date: 2016-04-01
categories: blog
tags: [php,php系统编程]
description: php cli

---


[TOC]

#简介
　　在php cli开发中,有时候我们需要把进程服务在占住在内存当中(例如守护进程),
如果想要快速区分找到该服务进程,我们不妨可以给该服务进程起一个别名

#实现思路
可以在运行php的进程中通过代码实现该改变该进程的名称

- [cli_set_process_title](http://php.net/manual/en/function.cli-set-process-title.php)(在php版本>=5.5中自带的函数)
- [setproctitle](http://php.net/manual/en/function.setproctitle.php)(protitle扩展的函数)

#代码例子

```php
/**
 * 设置当前进程的名称，在ps aux命令中有用
 * 注意 需要php>=5.5或者安装了protitle扩展
 * @param string $title
 * @return void
 */
function setProcessTitle($title)
{
    // >=php 5.5
    if (function_exists('cli_set_process_title'))
    {
        @cli_set_process_title($title);
    } // 需要扩展
    elseif (extension_loaded('proctitle') && function_exists('setproctitle'))
    {
        @setproctitle($title);
    }
}

setProcessTitle('SetProcessTitleDemo: master');
sleep(1000);
```

```bash
//运行php程序
php test.php
```

![php_set_process_title.png](http://7xpyze.com1.z0.glb.clouddn.com/php_set_process_title.png)





—End—



##迭代


* 2016年06月23日 16:29:00 初稿


