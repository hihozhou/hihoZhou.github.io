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


## 3.创建OSX虚拟机

我这里创建是osx15

### 3.1 新建虚拟机

- 1.打开vmware player
![22.png](/source/images/ubuntu18-vmware-osx/22.png)

- 2.点击创建新虚拟机
![23.png](/source/images/ubuntu18-vmware-osx/23.png)

- 3.选择稍后选择镜像，点击`Next`
![24.png](/source/images/ubuntu18-vmware-osx/24.png)

- 4.选择`Apple OS X`，`version`选择`macOS 14.5`，然后点击`Next`
![25.png](/source/images/ubuntu18-vmware-osx/25.png)

- 5.名称和路径根据自己需要填入，然后点击`Next`
![26.png](/source/images/ubuntu18-vmware-osx/26.png)

- 6.根据自己需要分配硬盘，机械硬盘选择`1单个文件存储`，ssd硬盘选择`2多个文件存储`
![27.png](/source/images/ubuntu18-vmware-osx/27.png)

- 7.点击完成
![28.png](/source/images/ubuntu18-vmware-osx/28.png)

### 3.2 配置虚拟机

- 1.选中新建的虚拟机，点击编辑
![29.png](/source/images/ubuntu18-vmware-osx/29.png)

- 2.根据自己的情况分配内存
![30.png](/source/images/ubuntu18-vmware-osx/30.png)

- 3.根据自己情况设置处理器数量
![31.png](/source/images/ubuntu18-vmware-osx/31.png)

- 4.选择系统镜像
![32.png](/source/images/ubuntu18-vmware-osx/32.png)

- 5.设置网络
![33.png](/source/images/ubuntu18-vmware-osx/33.png)

- 6.打开创建的虚拟机目录，编辑`xxx.vmx`文件，在`vmci0.present = "TRUE"`下加入`smc.present = "TRUE"`，`smc.version = "0"`
![34.png](/source/images/ubuntu18-vmware-osx/34.png)
![35.png](/source/images/ubuntu18-vmware-osx/35.png)

- 7.最后修改
打开VMware，进入点开`选项`，将客户机系统更改为`Windows`，并将版本更改为`windows10×64`单击“确定”保存。
![36.png](/source/images/ubuntu18-vmware-osx/36.png)
![37.png](/source/images/ubuntu18-vmware-osx/37.png)

>注意：第6、7部记得操作，不操作会一直开在苹果logo进不去安装

改完设置记得关闭软件，重新打开，如果不关闭软件重新打开，可能会出现不读进度条蓝屏等现象，启动虚拟机，安装macOS

### 3.3 安装osx系统

- 1.点击`Power on`开启虚拟机
- 2.等待进入安装界面
![39.png](/source/images/ubuntu18-vmware-osx/39.png)

- 3.安装界面，选着语言
![40.png](/source/images/ubuntu18-vmware-osx/40.png)

- 4.打开磁盘工具程序
![41.png](/source/images/ubuntu18-vmware-osx/41.png)

- 5.抹去磁盘，名称随意
![42.png](/source/images/ubuntu18-vmware-osx/42.png)
![43.png](/source/images/ubuntu18-vmware-osx/43.png)


- 6.关闭`磁盘工具`后点击`继续`，
![44.png](/source/images/ubuntu18-vmware-osx/44.png)
![45.png](/source/images/ubuntu18-vmware-osx/45.png)

- 7.选择刚刚格式化的盘，进行安装，耐心等待几分钟
![46.png](/source/images/ubuntu18-vmware-osx/46.png)
![47.png](/source/images/ubuntu18-vmware-osx/47.png)
![48.png](/source/images/ubuntu18-vmware-osx/48.png)
重新启动以完成安装
>一旦安装完成，重启的时候，立即关闭虚拟机

- 8.重新配置vm
![49.png](/source/images/ubuntu18-vmware-osx/49.png)
将客户机系统和版本更改为“MacOS 10.14”，并断开`CD/DVD引导盘`的连接。(同上面的步骤8，方法一样，只是再改回macOS的)
![50.png](/source/images/ubuntu18-vmware-osx/50.png)
![51.png](/source/images/ubuntu18-vmware-osx/51.png)

- 9.VMware中打开MacOS10.15 Catalina
接下来，打开VMware，经过一系列设置，并为创建一个新帐户。即可进入macOS桌面
![52.png](/source/images/ubuntu18-vmware-osx/52.png)
![53.png](/source/images/ubuntu18-vmware-osx/53.png)
![54.png](/source/images/ubuntu18-vmware-osx/54.png)

### 3.4安装VMware tools


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

## 3.没有符合安装资格的软件包，请联系软件制造商以获得帮助

格式化好安装盘后，进行安装出现提示`没有符合安装资格的软件包，请联系软件制造商以获得帮助`。

解决方法:

打开实用工具——终端，可以输入一下代码：

如果你的Mac是联网状体，可以输入一下代码：

```bash
ntpdate time.apple.com
```

这是苹果系统自动通过联网校准时间

或

输入：date 070512052018.03 （这里设置一个2015年以后的时间，因为10.11发布时2015年）

## 4.CPU虚拟化错误

在第一次启动虚拟机进行osx安装，无法进入安装界面，提示：

```bash
A fault has occurred causing a virtual CPU to enter the shutdown state. 
If this fault had occurred outside of a virtual machine, 
it would have caused the physical machine to restart. 
The shutdown state can be reached by incorrectly configuring the virtual machine, 
a bug in the guest operating system, or a problem in VMware Workstation.
```

解方法：

这个问题肯定与CPU有关，可以尝试调整CPU核心数或者更改vmx文件里的`guestOS`为这样`guestOS = "darwin10-64"`或`guestOS = "darwin11-64"`或`guestOS = "darwin12-64"`，如果还是不行的话，可以到网上找一个Vmware的补丁，之后新建虚拟机可以直接找到MacOs对应版本的虚拟机，直接选择并新建即可。

—End—

## 迭代

* 2020年01月13日 12：02 初稿

## 参考

- [《Ubuntu18.04: 构建vmmon失败，安装 VMware》](https://www.kutu66.com//ubuntu/article_167968)
- [《vmware-host-modules》](https://github.com/mkubecek/vmware-host-modules)
- [《CPU虚拟化错误》](http://www.etwiki.cn/linux/251.html)
