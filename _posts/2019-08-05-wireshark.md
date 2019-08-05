---
layout: post
title: ubuntu使用wireshark抓包
date: 2019-08-05
categories: blog
tags: [ubuntu,wireshark]
description: ubuntu使用wireshark抓包

---

## 安装和启动wireshark


```bash
sudo apt-get install wireshark
sudo wireshark
```

![1.png](/source/images/wireshark/1.png)


## 启动抓包

![2.png](/source/images/wireshark/2.png)

![3.png](/source/images/wireshark/3.png)


## 抓取本地数据包

先暂停抓包，`Capture`->`Options`,然后点击`Input`只是勾选`Loopback`，然后点击`start`

![4.png](/source/images/wireshark/4.png)

![5.png](/source/images/wireshark/5.png)

## 筛选

如果需要筛选，可以在输入框输入对应的条件，如筛选tcp协议，端口为18305的，`tcp.port == 18305`
![6.png](/source/images/wireshark/6.png)



—End—

## 迭代

* 2019年08月05日 13：34 初稿

## 参考

- [《ubuntu wireshark 抓取本地数据包》](https://blog.csdn.net/Lock_Love_/article/details/48781867)

