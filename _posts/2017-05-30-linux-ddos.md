---
layout: post
title: linux防御DDOS攻击
date: 2017-05-12
categories: blog
tags: [linux,ddos]
description: 

---


# 昨晚公司的服务器被别人用ddos攻击了（准确来说是CC攻击）

昨晚是`2017年5月29日`，端午节前一天，3姐和我的两个外甥过来我家吃完饭后，带上我和女朋友去一间喝东西的地方（是我三姐朋友新开的店铺）。  
本来是宁静和欢快的一晚，准备等待端午节的到来，结果到了大概8点半的时候，收到微信公众帐号报警提示回调接口没有反应的消息。  
于是我打开我们公司的网站，发现报502错误，心想只是简单的fpm崩溃。然后当我重启fpm之后没有多久就立马又崩了。
尝试了重启几次都发现一样，然后我怀疑是大访问fpm导致fpm挂掉，然后我就查看nginx的error.log。
发现大量的访问同一个接口，而且这个接口获取手机验证码接口，可以判断是CC攻击，来来去去就那10来个ip一直访问这接口，攻击级别并不算高  
然后我就第一步采取nginx防ddos部署，接着和另外一个同事确定用上iptables防ddos攻击。


## nginx防DDOS攻击
nginx主要有两个模块可以实行防DDOS攻击策略

- `http_limit_conn`（ngx_http_limit_conn_module）可以限制单个IP的连接数
- `http_limit_req`（ngx_http_limit_req_module）可以限制单个IP每秒请求数，通过限制连接数和请求数能相对有效的防御CC攻击。下面是配置方法：

### 一. 限制每秒请求数
`ngx_http_limit_req_module`模块通过漏桶原理来限制单位时间内的请求数，一旦单位时间内请求数超过限制，就会返回503错误。

- nginx.conf的http段内定义触发条件，可以有多个条件
- 在location内定义达到触发条件时nginx所要执行的动作

配置需要在两个地方设置：

e.g:

```nginx
http {
    limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s; //触发条件，所有访问ip 限制每秒10个请求
    ...
    server {
        ...
        location  ~ \.php$ {
            limit_req zone=one burst=5 nodelay;   //执行的动作,通过zone名字对应
        }
    }
    ... 
}    
```

参数说明：


- $binary_remote_addr  二进制远程地址
- zone=one:10m    定义zone名字叫one，并为这个zone分配10M内存，用来存储会话（二进制远程地址），1m内存可以保存16000会话
- rate=10r/s;     限制频率为每秒10个请求
- burst=5         允许超过频率限制的请求数不多于5个，假设1、2、3、4秒请求为每秒9个，那么第5秒内请求15个是允许的，反之，如果第一秒内请求15个，会将5个请求放到第二秒，第二秒内超过10的请求直接503，类似多秒内平均速率限制。
- nodelay         超过的请求不被延迟处理，设置后15个请求在1秒内处理。

—End—


## 迭代

* 2017年05月25日 18:03 初稿

## 参考文章


