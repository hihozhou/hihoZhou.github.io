---
layout: post
title: 执行 ‘pecl install swoole’之后，遇到的一些坑
date: 2018-12-19
categories: blog
tags: [php,swoole]
description: 执行 ‘pecl install swoole’之后，遇到的一些坑

---

## 用pecl安装swoole出错

之前在ubuntu16上用pecl安装swoole没有问题，但在线上centos服务器上尝试用`pecl install swoole`安装swoole后报错：


>No releases available for package "pecl.php.net/swoole"
install failed


```bash
$pecl search swoole
```


>Connection to `ssl://pecl.php.net:443′ failed:


查看openssl是否正常


```bash
$ openssl version
OpenSSL 1.0.2k-fips  26 Jan 2017
```


查看证书调用

```bash
$ php -r "print_r(openssl_get_cert_locations());"
Array
 (
     [default_cert_file] => /usr/local/openssl/cert.pem
     [default_cert_file_env] => SSL_CERT_FILE
     [default_cert_dir] => /usr/local/openssl/certs
     [default_cert_dir_env] => SSL_CERT_DIR
     [default_private_dir] => /usr/local/openssl/private
     [default_default_cert_area] => /usr/local/openssl
     [ini_cafile] => /etc/pki/tls/certs/ca-bundle.crt
     [ini_capath] => 
 )

```

ls /usr/local/openssl/ 发现没有 `cert.pem`这个证书


```bash
$ wget -c https://curl.haxx.se/ca/cacert.pem  /usr/local/openssl/cert.pem --no-check-certificate

```

重新执行，就可以了


```bash
$pecl install swoole
```


## nghttp2

如果需要支持`nghttp2`的时候，需要安装nghttp2，在centos下安装完后记得`sudo ldconfig`
不然会报一下类似错误：

```bash
PHP Warning: PHP Startup: Unable to load dynamic library ‘swoole.so’ (tried: /softs/php/lib/php/extensions/no-debug-zts-20170718/swoole.so (libnghttp2.so.14: cannot open shared object file: No such file or directory), /softs/php/lib/php/extensions/no-debug-zts-20170718/swoole.so.so (/softs/php/lib/php/extensions/no-debug-zts-20170718/swoole.so.so: cannot open shared object file: No such file or directory)) in Unknown on line 0
```

—End—


## 迭代

* 2018年12月16日 15：55 初稿

## 参考

