---
layout: post
title: PHP程序守护进程化
date: 2016-06-24
categories: blog
tags: [php,php系统编程,php守护进程,daemon]
description: php daemon

---


[TOC]

#简介
　　一般Server程序都是运行在系统后台，这与普通的交互式命令行程序有很大的区别。
glibc里有一个函数daemon。调用此函数，就可使当前进程脱离终端变成一个守护进程，
具体内容参见man daemon。PHP中暂时没有此函数，当然如果你有兴趣的话，可以写一个PHP的扩展函数来实现。

#方法:

- 使用nohup
- 使用PHP代码来实现

##一 、使用nohup

```bash
php myprog.php > log.txt &
```

这里就实现了守护进程化。

单独执行 php myprog.php，当按下ctrl+c时就会中断程序执行，会kill当前进程以及子进程。

`php myprog.php &`这样执行程序虽然也是转为后台运行，实际上是依赖终端的，当用户退出终端时进程就会被杀掉。


##二、使用PHP代码来实现

```php
function daemonize()
{
    $pid = pcntl_fork();//创建子进程
    if ($pid == -1)
    {
        die("fork(1) failed!\n");
    }
    elseif ($pid > 0)
    {
        //让由用户启动的进程退出
        exit(0);//退出父进程
    }
    
    //建立一个有别于终端的新session以脱离终端,子进程成为进程组组长
    posix_setsid();
    
    $pid = pcntl_fork();//创建进程组组长(刚刚父进程创建的子进程)的子进程
    if ($pid == -1)
    {
        die("fork(2) failed!\n");
    }
    elseif ($pid > 0)
    {
        //退出进程组组长进程, 剩下子进程成为最终的独立进程
        exit(0);
    }
}

daemonize();
sleep(1000);
```

这里有人会疑问为什么要fork两次创建子进程,很多copy代码的人也不会去了解为什么需要fork两次.

1 、第一次fork的作用是让shell 认为本条命令 已经终止，不用挂在终端输入上。还有一个作用是为后面setsid服务。setsid的调用者不能是进程组组长(group leader). 此时父进程是进程组组长。
    
2 、setsid() 是本函数最重要的一个调用。它完成了daemon函数想要做的大部分事情。调用完整个函数。子进程是会话组长(sid==pid)，也是进程组组长(pgid == pid)，并且脱离了原来控制终端。到了这一步，基本上不管控制终端如何怎么样。新的进程都不会收到那些信号。

3  、经过前面2个步骤，基本想要做的都做了。第2次fork不是必须的。也看到很多开源服务没有fork第二次。fork第二次主要目的是。防止进程再次打开一个控制终端。因为打开一个控制终端的前提条件是该进程必须是会话组长。再fork一次，子进程ID != sid（sid是进程父进程的sid）。所以也无法打开新的控制终端。




—End—



##迭代


* 2016年06月24日 17:30:00 初稿


