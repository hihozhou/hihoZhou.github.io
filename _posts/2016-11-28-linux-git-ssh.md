---
layout: post
title: linux下配置ssh链接github
date: 2016-11-28
categories: blog
tags: [linux,git,ssh]
description: git使用ssh

---


##检查本机ssh key配置

```bash
$ cd ~/.ssh && ls -l
```
如果没有则提示： `cd: no such file or directory: /home/你的用户名/.ssh`
或则显示没有文件则表示本机子没有创建过ssh key

![linux-git-ssh-check.png](http://7xpyze.com1.z0.glb.clouddn.com/linux-git-ssh-check.png)

##生成新的ssh key
```bash
#进入用户的ssh管理目录，没有则自行创建
$ cd ~/.ssh
#使用ssh-keygen生成ssh key
$ ssh-keygen -t rsa -C "你的邮箱地址"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/hiho/.ssh/id_rsa): blog #输入你存放ssh key的文件名称，不输入默认为/home/hiho/.ssh/id_rsa，这里我叫blog
Enter passphrase (empty for no passphrase): #输入密码（可以为空）
Enter same passphrase again:  #再次确认密码（可以为空）
Your identification has been saved in blog. #生成的密钥
Your public key has been saved in blog.pub.  #生成的公钥

```

![linux-git-ssh-keygen.png](http://7xpyze.com1.z0.glb.clouddn.com/linux-git-ssh-keygen.png)

另外你也可以在生成时候用`-f`制定生成的文件名称，如：

```bash
$ ssh-keygen -t rsa -C "邮箱地址" -f ~/.ssh/blog #生成ssh key的名称为blog
```

##添加ssh key到Github
1.登录GitHub系统；点击右上角账号头像的“▼”→Settings→SSH kyes→Add SSH key。

2.复制id_rsa.pub的公钥内容。

3.配置账户

```bash
$ git config --global user.name “your_username”  #设置用户名
$ git config --global user.email “your_registered_github_email”  #设置邮箱地址(建议用注册giuhub的邮箱)
```

4.测试ssh keys是否设置成功

```bash
$ ssh -T git@github.com
```


—End—

##迭代


* 2016年11月28日 11:14 初稿



