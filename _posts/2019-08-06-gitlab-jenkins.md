---
layout: post
title: 使用jenkins搭建gitlab持续集成CI和持续部署CD
date: 2019-08-06
categories: blog
tags: [ubuntu,gitlab,jenkins]
description: 使用jenkins搭建gitlab持续集成CI和持续部署CD

---

## 1.gitlab安装

gitlab的安装就不说了


## 2.java环境
由于之前就已经安装了java环境，所以就也不说了


## 3.安装jenkins


### 命令安装jenkins

```bash
wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install jenkins
```


如果出现启动失败，则可能需要修改端口
![1.png](/source/images/gitlab-jenkins/1.png)

- 配置文件在 /etc/default/jenkins中，
    - `NAME` 默认用户名为jenkins 
    - `HTTP_PORT` 默认`8080` 可以更改端口号等相关配置，
- 密码在 /var/lib/jenkins/secrets/initialAdminPassword中，如果没有权限访问可以修改文件夹权限。

修改完后重启启动jenkins

```bash
sudo service jenkins start
```

如果出现启动失败，使用`journalctl -xe`显示

```bash
-- Unit jenkins.service has begun starting up.
Aug 06 02:44:37 onehour jenkins[22830]: ERROR: No Java executable found in current PATH: /bin:/usr/bin:/sbin:/usr/sbin
Aug 06 02:44:37 onehour jenkins[22830]: If you actually have java installed on the system make sure the executable is in the aforementioned path and that 'type -p java' returns the java executable path
Aug 06 02:44:37 onehour systemd[1]: jenkins.service: Control process exited, code=exited status=1
Aug 06 02:44:37 onehour systemd[1]: jenkins.service: Failed with result 'exit-code'.
Aug 06 02:44:37 onehour systemd[1]: Failed to start LSB: Start Jenkins at boot time.

```

需要将java执行文件添加软连接到`/bin:/usr/bin:/sbin:/usr/sbin`其中一个目录下

```bash
sudo ln -s /usr/java/jdk1.8/bin/java java
```

使用`type -p java`检测



### 首次访问jenkins

- 1.网页打开`ip:端口`，我这里是`192.168.199.254:8088`
![2.png](/source/images/gitlab-jenkins/2.png)
这里它提示密码存放在`/var/lib/jenkins/secrets/initialAdminPassword`，打开该文件复制密码输入，然后点击`Continue`，

- 2.选择`Install suggested plugins`安装建议的插件
![3.png](/source/images/gitlab-jenkins/3.png)
插件安装中...
![4.png](/source/images/gitlab-jenkins/4.png)

- 3.安装完成后，创建管理员用户
![5.png](/source/images/gitlab-jenkins/5.png)
点击`Save and Continue`
![6.png](/source/images/gitlab-jenkins/6.png)
![7.png](/source/images/gitlab-jenkins/7.png)


- 4.登录账户
![8.png](/source/images/gitlab-jenkins/8.png)
![9.png](/source/images/gitlab-jenkins/9.png)


## 安装Jenkins插件

点击左侧导航栏`Manage Jenkins` -> `Manage Plugins` -> `Available`

安装`GitLab`两个插件

![10.png](/source/images/gitlab-jenkins/10.png)


## Jenkins创建测试
点击左边菜单栏 `New Item`
![11.png](/source/images/gitlab-jenkins/11.png)

输入名字，然后选择`Freestyle project`然后点击`OK`
![12.png](/source/images/gitlab-jenkins/12.png)

配置gitlab，点击`General` -> `Source Code Management` -> 选择`Git`，然后添加认证方式和仓库地址
![13.png](/source/images/gitlab-jenkins/13.png)

添加认证方式，我这里是账号密码，也可以根据自己实际情况，使用ssh方式也可以
![14.png](/source/images/gitlab-jenkins/14.png)


填好点击save
![15.png](/source/images/gitlab-jenkins/15.png)

然后可以运行一下测试
![16.png](/source/images/gitlab-jenkins/16.png)

测试信息
![17.png](/source/images/gitlab-jenkins/17.png)

### 提交后自动运行测试

- 1.点击`Configure` -> `Build Triggers` -> `Build when a change is pushed to GitLab. GitLab webhook URL:`

![18.png](/source/images/gitlab-jenkins/18.png)


- 2.然后打开gitlab，点击项目 -> `Settings` -> `Integrations` 

- 3.添加`URL`和`Secret Token`

![19.png](/source/images/gitlab-jenkins/19.png)

现在如果push的话jenkins就会自动运行测试


### 指定运行脚本

点击`Configure` -> `Build` -> `Add build step` -> `Execute shell`
在`Command`添加名利即可


![20.png](/source/images/gitlab-jenkins/20.png)

—End—

## 迭代

* 2019年08月05日 13：34 初稿

## 参考

- [《ubuntu wireshark 抓取本地数据包》](https://blog.csdn.net/Lock_Love_/article/details/48781867)

