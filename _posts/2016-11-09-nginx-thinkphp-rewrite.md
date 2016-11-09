---
layout: post
title: thinkphp nginx路由配置及路由重写
date: 2016-11-09
categories: blog
tags: [thinkphp,nginx,路由重写]
description: 这是一篇关于路由thinkphp nginx重写路由,并且实现隐藏index.php和路由隐藏默认模块与控制器名称

---

##nginx+fpm服务器处理流程
服务器一般使用nginx+fpm搭建  
请求处理如下:  
用户发送请求->nginx接收请求->nginx判断是否需要交给fpm处理,不需要则直接返回给用户->fpm处理->fpm返回给nginx->nginx返回给用户->用户得到响应

##nginx配置thinkphp路由重写

```vim
server {
        #监听80端口
        listen       80;
        #配置域名
        server_name  localhost;
        #项目路径
        root           /home/hiho/www;
        #配置访问日志路径,产品环境不建议开启,减低服务器文件读写
        #access_log  logs/host.access.log  main;

        
        location / {
            #配置默认的请求访问的文件
            index index.php index.html index.htm;
            #访问路径的文件不存在则重写URL转交给ThinkPHP处理
            if (!-e $request_filename) {
                #判断访问的页面是一级目录(不存在斜杠)并且html结尾,这里隐藏的是Home模块的Index控制器
                #如访问localhost/about.html会访问localhost/index.php/Home/Index/about.html
                rewrite ^/([^/]*\.html)$ /index.php/Home/Index/$1 last; 
                rewrite  ^/(.*)$  /index.php/$1  last;
                break;
            }
        }
        #配置访问404的时候输出页面
        error_page  404 /404.html;
        
        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }


        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        location ~ .+\.php($|/) {
                fastcgi_pass   127.0.0.1:9000;
                fastcgi_index  index.php;
                #加载Nginx默认"服务器环境变量"配置
                include        fastcgi.conf;

                #设置PATH_INFO并改写SCRIPT_FILENAME,SCRIPT_NAME服务器环境变量
                set $fastcgi_script_name2 $fastcgi_script_name;
                if ($fastcgi_script_name ~ "^(.+\.php)(/.+)$") {
                        set $fastcgi_script_name2 $1;
                        set $path_info $2;
                }
                fastcgi_param   PATH_INFO $path_info;
                fastcgi_param   SCRIPT_FILENAME   $document_root$fastcgi_script_name2;
                fastcgi_param   SCRIPT_NAME   $fastcgi_script_name2;
        }

    }

```




—End—

##迭代


* 2016年11月09日 17:17 初稿



