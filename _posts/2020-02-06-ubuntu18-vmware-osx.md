---
layout: post
title: Ubuntu18使用VMware安装OSX系统
date: 2020-01-13
categories: blog
tags: [ubuntu,linux,VMware,osx]
description: Ubuntu18使用VirtualBox安装OSX系统

---


## 1.安装vmware

### 1.1下载

可以到[官网](https://www.vmware.com/cn/products/workstation-pro/workstation-pro-evaluation.html)下载`vmware`，
我这里使用的的是15.1.0版本。

![1.png](/source/images/ubuntu18-vmware-osx/1.png)


### 1.2安装


首先安装刚下载好的`vmware`应用程序

1.在终端输入命令启动安装程序

```bash
chmod u+x VMware-Workstation-Full-15.1.0-13591040.x86_64.bundle 
sudo ./VMware-Workstation-Full-15.1.0-13591040.x86_64.bundle
```

2.同意条款
![2.png](/source/images/ubuntu18-vmware-osx/2.png)
选择`I accept`->`Next`

3.是否检测更新最新版，这里因为我使用`15.1`，选`否`
![3.png](/source/images/ubuntu18-vmware-osx/3.png)

4.加入xxxx
![4.png](/source/images/ubuntu18-vmware-osx/4.png)

5.用户设置
![5.png](/source/images/ubuntu18-vmware-osx/5.png)

6.端口设置
![6.png](/source/images/ubuntu18-vmware-osx/6.png)

7.激活秘钥输入
![7.png](/source/images/ubuntu18-vmware-osx/7.png)

等待安装完成就可以了
![8.png](/source/images/ubuntu18-vmware-osx/8.png)
![9.png](/source/images/ubuntu18-vmware-osx/9.png)

### 1.2安装unlocker

VMware Workstations自身是不支持安装Mac系统的，不过有大神开发出了解锁插件（其实就是一些驱动文件），安装插件后，即可在VMware中安装cdr格式的苹果系统镜像的虚拟机。

>注意:VMware Workstations Pro已经更新到了15.5.0，并且Unlock3.0.2不支持15.5，请下载最新的Unlock，这个Unlock支持Catalina 10.15系统。
![14.png](/source/images/ubuntu18-vmware-osx/14.png)


启动vmware
![10.png](/source/images/ubuntu18-vmware-osx/10.png)

![13.png](/source/images/ubuntu18-vmware-osx/13.png)

安装unlocker前，没有`OSX`选项。
![14.png](/source/images/ubuntu18-vmware-osx/14.png)

[unlocker地址](https://github.com/theJaxon/unlocker)

```bash
git clone https://github.com/theJaxon/unlocker.git
cd unlocker
sudo lnx-install.sh
```
![19.png](/source/images/ubuntu18-vmware-osx/19.png)

![16.png](/source/images/ubuntu18-vmware-osx/16.png)

## 问题

### 1.安装Virtual Machine Monitor失败
第一次启动vmware需要安装环境，安装virtual machine monitor失败
![11.png](/source/images/ubuntu18-vmware-osx/11.png)
![12.png](/source/images/ubuntu18-vmware-osx/12.png)


这里大概原因就是环境问题，可以使用https://github.com/mkubecek/vmware-host-modules安装必要的环境

```bash
wget https://codeload.github.com/mkubecek/vmware-host-modules/tar.gz/w15.1.0-k5.5
tar -xzf w15.1.0-k5.5
cd vmware-host-modules-w15.1.0-k5.5
make
sudo make install
```

### 2.unlocker安装失败
![15.png](/source/images/ubuntu18-vmware-osx/15.png)

其实是 [https://softwareupdate.vmware.com/cds/vmw-desktop/fusion/](https://softwareupdate.vmware.com/cds/vmw-desktop/fusion/) ，我用的时候`11.5.1`没有`packages`，
只有`11.1.0`有，他这个unlocker脚本默认是获取最新版的，所以没有办法获取。

![17.png](/source/images/ubuntu18-vmware-osx/17.png)
![18.png](/source/images/ubuntu18-vmware-osx/18.png)

解决：

```bash
vim gettools.py
```


```bash
# 将95行代码
url = url + parser.HTMLDATA[-1] + '/' #底95行原来代码
# 改为
url = url +  '11.1.0/' #修改后的代码
```

—End—

## 迭代

* 2020年01月13日 12：02 初稿

## 参考

- [《Ubuntu18.04: 构建vmmon失败，安装 VMware》](https://www.kutu66.com//ubuntu/article_167968)
- [《vmware-host-modules》](https://github.com/mkubecek/vmware-host-modules)
