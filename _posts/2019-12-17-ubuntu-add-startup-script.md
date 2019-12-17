---
layout: post
title: Ubuntu18.04添加开机启动脚本
date: 2019-12-17
categories: blog
tags: [ubuntu]
description: Ubuntu18.04添加开机启动脚本

---

## Ubuntu18与16的区别

ubuntu 的官方论坛看到原来 ubuntu-16.10 开始不再使用initd管理系统，改用system

>systemd is now used for user sessions. System sessions had already been provided by systemd in previous Ubuntu releases.

其实这个改动到不是算大，主要是开机启动比以前复杂多了。`systemd` 默认读取 `/etc/systemd/system` 下的配置文件，该目录下的文件会链接/lib/systemd/system/下的文件。

## 添加开机脚本

### 1.为rc.local.service添加Install

执行 `ls /lib/systemd/system` 你可以看到有很多启动脚本，其中就有我们需要的 `rc.local.service`

打开脚本内容：

```bash
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.

# This unit gets pulled automatically into multi-user.target by
# systemd-rc-local-generator if /etc/rc.local is executable.
[Unit]
Description=/etc/rc.local Compatibility
ConditionFileIsExecutable=/etc/rc.local
After=network.target

[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
RemainAfterExit=yes
```

一般正常的启动文件主要分成三部分

>[Unit] 段: 启动顺序与依赖关系
 [Service] 段: 启动行为,如何启动，启动类型
 [Install] 段: 定义如何安装这个配置文件，即怎样做到开机启动


可以看出，/etc/rc.local 的启动顺序是在网络后面，但是显然它少了 Install 段，也就没有定义如何做到开机启动，所以显然这样配置是无效的。 因此我们就需要在后面帮他加上 [Install] 段:

```bash
[Install]  
WantedBy=multi-user.target  
Alias=rc-local.service
```

### 2.添加/etc/rc.local文件

这里需要注意一下，ubuntu-18.04 默认是没有 /etc/rc.local 这个文件的，需要自己创建`sudo vim /etc/rc.local`


```bash
#!/bin/bash
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.
echo "this just a test" > /usr/local/text.log

exit 0

```


### 3.给rc.local添加可执行权限

```bash
sudo chmod +x /etc/rc.local
# or
sudo chmod 755 /etc/rc.local
```

### 4.生成软链

做完这一步，还需要最后一步 前面我们说 systemd 默认读取 /etc/systemd/system 下的配置文件, 所以还需要在 /etc/systemd/system 目录下创建软链接


```bash
ln -s /lib/systemd/system/rc.local.service /etc/systemd/system/
```

OK, 接下来，重启系统，然后看看 /usr/local/text.log 文件是否存在就知道开机脚本是否生效了。


## 添加服务

### 1.添加启用服务

```bash
sudo systemctl enable rc-local
```

### 2.启动服务并检查状态

```bash
sudo systemctl start rc-local.service
sudo systemctl status rc-local.service
```

—End—

## 迭代

* 2019年11月17日 11：22 初稿

## 参考

