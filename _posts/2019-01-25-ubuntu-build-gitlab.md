---
layout: post
title: Ubuntu搭建自己的gitlab
date: 2019-01-25
categories: blog
tags: [ubuntu,gitlab]
description: Ubuntu搭建自己的gitlab

---

注：本次使用Ubuntu18.04服务器

## 1.安装依赖包，运行命令

1.1 运行命令安装

```bash
sudo apt-get update
sudo apt-get install curl openssh-server ca-certificates
```

1.2 接下来，安装Postfix以发送通知电子邮件。如果要使用其他解决方案发送电子邮件，请跳过此步骤并 在安装GitLab后[配置外部SMTP服务器](https://docs.gitlab.com/omnibus/settings/smtp.html)。

```bash
sudo apt-get install -y postfix
```

执行完成后，出现邮件配置，选择`Internet Site`，连续两次`回车ok`
![1.png](/source/images/ubuntu-build-gitlab/1.png)

![2.png](/source/images/ubuntu-build-gitlab/2.png)


## 2.安装gitlab-ce

添加[清华大学源](https://mirror.tuna.tsinghua.edu.cn/help/gitlab-ce/)

首先信任 GitLab 的 GPG 公钥:
```bash
curl https://packages.gitlab.com/gpg.key 2> /dev/null | sudo apt-key add - &>/dev/null
```

Debian/Ubuntu 版本，文本框中内容写进 `/etc/apt/sources.list.d/gitlab-ce.list`

```bash
deb https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu xenial main
```


接下来，安装GitLab包。

```bash
sudo apt-get install gitlab-ce
```

![3.png](/source/images/ubuntu-build-gitlab/3.png)

修改`/etc/gitlab/gitlab.rb`中的`external_url`配置访问页面，我这里是本地机子ip是`192.168.8.254`

```bash
sudo vim  /etc/gitlab/gitlab.rb
```


```bash
sudo gitlab-ctl reconfigure
```

### reconfigure 错误

在reconfigure后发现报错

```bash
    ================================================================================
    Error executing action `run` on resource 'bash[migrate gitlab-rails database]'
    ================================================================================
```
查看一下下面提示的错误发现

```bash
ArgumentError: could not find a temporary directory
```

执行命令

```bash
chmod +t /tmp
```
具体问题可以参考https://stackoverflow.com/questions/21117878/ruby-could-not-find-a-temporary-directory

检查GitLab是否安装好并且已经正确运行，输入下面的命令（查看gitlab的状态）

```bash
sudo gitlab-ctl status
```

## 修改端口

由于本机已经安装有nginx监听了80端口，而gitlab自带启动的nginx也是80端口会冲突

1.先关闭gitlab服务

```bash
 sudo gitlab-ctl stop
```



2.修改gitlab自带的nginx监听端口

```bash
# 修改配置 
sudo vim /etc/gitlab/gitlab.rb
```

将`nginx['listen_port'] = nil`（默认80）改为`nginx['listen_port'] = 4567`

![4.png](/source/images/ubuntu-build-gitlab/4.png)

修改为自己需要监听的端口，我这里是修改成`4567`



```bash
# 重新加载配置
sudo gitlab-ctl reconfigure
# 重新启动
sudo gitlab-ctl restart
```


## 3.浏览到主机名并登录


### 3.1 设置初始密码

访问你配置的gitlab链接，我这里是`http://192.168.8.254:4567`，第一次访问会重定向到`密码重置页面`，然后设置一下超级管理员的账号的密码，超级管理员账号用户名为`root`

![5.png](/source/images/ubuntu-build-gitlab/5.png)

输入两次密码后，点击`Change your password`就可以

### 3.2 登录

登录使用root和你刚刚设置的密码

![6.png](/source/images/ubuntu-build-gitlab/6.png)

## 使用非gitlab的nginx

因为本来机子已经有一个nginx，不想一台机子跑两个nginx服务，

官网参考文档: https://blog.csdn.net/pujiaolin/article/details/80709299

1.修改配置文件，禁用内嵌 nginx

```bash
sudo vim /etc/gitlab/gitlab.rb
```

修改一下配置
```bash
# 关闭nginx服务
nginx['enable'] = false
# 修改web服务用户，我这里是www
web_server['external_users'] = ['www']
```

![7.png](/source/images/ubuntu-build-gitlab/7.png)


```bash
# 重新加载配置
sudo gitlab-ctl reconfigure
# 重新启动
sudo gitlab-ctl restart
```

![8.png](/source/images/ubuntu-build-gitlab/8.png)

可以看到没有启动nginx服务

2.可以参考官网文档修改配置nginx
https://docs.gitlab.com/omnibus/settings/nginx.html#vhost-server-block

修改nginx配置

```bash
# 添加upstream指向gitlab
upstream gitlab-workhorse {
  server unix:/var/opt/gitlab/gitlab-workhorse/socket  fail_timeout=0;
}

# 在server中添加
server
{

    ......

    location / {
        	# serve static files from defined root folder;.
        	# @gitlab-workhorse is a named location for the upstream fallback, see below
        	try_files $uri $uri/index.html $uri.html @gitlab-workhorse;
    }
    
    location @gitlab-workhorse {
        	# If you use https make sure you disable gzip compression 
        	# to be safe against BREACH attack
     
            proxy_read_timeout 300; # Some requests take more than 30 seconds.
            proxy_connect_timeout 300; # Some requests take more than 30 seconds.
            proxy_redirect     off;
    
            proxy_set_header   X-Forwarded-Proto $scheme;
            proxy_set_header   Host              $http_host;
            proxy_set_header   X-Real-IP         $remote_addr;
            proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
            proxy_set_header   X-Frame-Options   SAMEORIGIN;
     
        	proxy_pass http://gitlab-workhorse;
    }

    location ~ ^/(assets)/  {
        	root /opt/gitlab/embedded/service/gitlab-rails/public;
        	# gzip_static on; # to serve pre-gzipped version
        	expires max;
        	add_header Cache-Control public;
    }

    .....
}

```

重启后就可以访问nginx配置可以顺利进入

![9.png](/source/images/ubuntu-build-gitlab/9.png)

## 配置gitlab邮箱

1.开启QQ邮箱的smtp服务

设置--》账户--》smtp--》密保验证--》验证成功返回一串字符串，形状如（ausdixersybgcgid）

![10.png](/source/images/ubuntu-build-gitlab/10.png)

2.修改gitlab配置

```bash
vim /etc/gitlab/gitlab.rb
# 按 / 后输入smtp_enable，找到下面这一串文本，进行修改

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.qq.com"//qq smtp 邮箱
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "xxxxx@qq.com" //自己的邮箱
gitlab_rails['smtp_password'] = "开通smtp时返回的字符"//如 （mcgzkoruwuxvbggj）
gitlab_rails['smtp_domain'] = "qq.com"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = true
/git_user_email找到
user['git_user_email'] = "xxx@qq.com" //自己的邮箱
/gitlab_email_from 找到
gitlab_rails['gitlab_email_from']-'自己的邮箱'


按esc退出到命令行模式
之后:wq 保存并退出
gitlab-ctl reconfigure//重新启动配置

```


测试邮件服务是否正常

```bash
gitlab-rails console
Notify.test_email('接收方邮件地址','邮件标题','邮件内容').deliver_now
```
按回车，测试发送。

![11.png](/source/images/ubuntu-build-gitlab/11.png)
![12.png](/source/images/ubuntu-build-gitlab/12.png)


##　使用frp将本地gitlab代理到外网




—End—


## 迭代

* 2019年01月25日 19：58 初稿

## 参考

- [《Ubuntu下安装gitlab(亲测有效)》](https://blog.csdn.net/qq_32106647/article/details/81586506)
- [《GitLab Installation》](https://about.gitlab.com/install/#ubuntu)
- [《Ruby: Could not find a temporary directory》](https://stackoverflow.com/questions/21117878/ruby-could-not-find-a-temporary-directory)
- [《What permissions does nginx need for the Gitlab-workhorse socket?》](https://stackoverflow.com/questions/35327882/what-permissions-does-nginx-need-for-the-gitlab-workhorse-socket)
- [《GitLab 的邮箱配置》](https://blog.csdn.net/weixin_40400084/article/details/81448156)
