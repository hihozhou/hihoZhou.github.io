---
layout: post
title: 腾讯云服务器云硬盘挂载
date: 2015-08-10
categories: blog
tags: [腾讯云，硬盘挂载]
description: 腾讯云服务器购买后，云硬盘挂载

---

## 服务器购买后挂载多出的云硬盘（非系统盘）

在购买腾讯云服务器时，我们一般会选择云硬盘大小，

![1](/source/images/tencent-cloud-cloud-hard-disk-mount/1.png)

而硬盘又分为系统盘和数据盘:

- 系统盘在服务器创建初始的时候就自动挂载和安装系统
- 而数据盘相当于扩展盘，需要自行挂载分区


链接服务器，运行命令

```bash
ubuntu@VM-ubuntu:~$ df -h
Filesystem      Size  Used Avail Use% Mounted on
udev            3.9G     0  3.9G   0% /dev
tmpfs           786M   41M  745M   6% /run
/dev/vda1        50G  1.7G   45G   4% /
tmpfs           3.9G   24K  3.9G   1% /dev/shm
tmpfs           5.0M     0  5.0M   0% /run/lock
tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
tmpfs           786M     0  786M   0% /run/user/500

```

可以看出只有系统盘50G，而并没有我们另外购买的数据盘150G


使用命令`sudo fdisk -l`

![2](/source/images/tencent-cloud-cloud-hard-disk-mount/2.png)

可以看出服务器是挂载了`Disk /dev/vdb: 150 GiB`的硬盘

接下来我们要对150G（/dev/vdb）的硬盘进行分区和挂载。



## （可选）对磁盘进行分区操作

如果你不选择分区，就可以直接对盘进行整个盘格式，然后挂载

```bash
sudo fdisk /dev/vdb

```

按照界面的提示，依次输入“n”(新建分区)、“p”(新建扩展分区)、“1”(使用第1个主分区)，两次回车(使用默认配置)，输入“wq”(保存分区表)，回车开始分区。

这里是以创建 1 个分区为例，开发者也可以根据自己的需求创建多个分区。

![3](/source/images/tencent-cloud-cloud-hard-disk-mount/3.png)

使用`“fdisk -l”`命令，即可查看到，新的分区 /dev/vdb1 已经创建完成。

![4](/source/images/tencent-cloud-cloud-hard-disk-mount/4.png)

##（有条件）硬盘格式化

注意：此步骤假定您在处理初始化的硬盘。如果该硬盘已经包含数据（如，从快照创建的云硬盘），请勿使用 mkfs（而应跳到下一步）。否则，您会格式化并删除现有数据。

因为我们的硬盘是新分区，没有格式化过，所以这里要运行运行`mkfs.ext4 device_name`命令格式化并创建 ext4 文件系统。
用设备名称（例如，/dev/vdb）替换 `device_name`。
根据应用程序的要求或操作系统的限制，您可以选择其他文件系统类型，如 ext3 或 XFS。

这里我们是

```bash
mkfs.ext4 /dev/vdb
```


## 挂载硬盘

格式化后，我们还要把硬盘挂载到对应linux的目录

```bash
mkdir /disk2 -p  # 创建示例挂载点
mount /dev/vdb /disk2

```


—End—


## 迭代

* 2015年08月10日 10：41 初稿

## 参考

- [Linux系统分区、格式化、挂载及创建文件系统](https://cloud.tencent.com/document/product/362/6735#.EF.BC.88.E5.8F.AF.E9.80.89.EF.BC.89.E5.AF.B9.E7.A3.81.E7.9B.98.E8.BF.9B.E8.A1.8C.E5.88.86.E5.8C.BA.E6.93.8D.E4.BD.9C3)
