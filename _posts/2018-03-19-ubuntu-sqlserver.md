---
layout: post
title: Ubuntu16.04安装使用sqlserver
date: 2018-03-19
categories: blog
tags: [ubuntu,sqlserver]
description: Ubuntu16.04安装使用sqlserver

---

## 安装SQL Server

官网安装指南：[https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup-ubuntu](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup-ubuntu)。

安装步骤如下：

1.导入repository GPG键和SQL Server Ubuntu的安装库：
      
```bash
curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/16.04/prod.list)"
```

2.安装SQL Server：

```bash
sudo apt-get update
sudo apt-get install -y mssql-server
```

3.初始化SQL Server配置：

```bash
sudo /opt/mssql/bin/mssql-conf setup
```

4.确认SQL Server服务的状态：

```bash
systemctl status mssql-server
```

![sql server status](/source/images/ubuntu-sqlserver/status.png)

5.如果您计划远程连接`SQL Server`，也可能需要在防火墙上打开SQL Server TCP端口（默认值`1433`）。

设置防火墙开启SQL Server服务端口：
```bash
sudo ufw allow 1433/tcp
sudo ufw reload
```

Note：默认情况下Ubuntu防火墙是关闭状态，ufw status可以查看。  
另外如果有其它防火墙工具，比如firewalld工具包，则需要执行以下命令设置防火墙开启SQL Server服务端口：

```bash
firewall-cmd --zone=public --add-port=1433/tcp --permanent
firewall-cmd --reload
```

6.启动和停止sqlserver服务
```
sudo systemctl start mssql-server
sudo systemctl stop mssql-server
```


## 安装SQL Server Tools
官方安装文档见：[https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup-tools#ubuntu](https://docs.microsoft.com/en-us/sql/linux/sql-server-linux-setup-tools#ubuntu)。

To create a database, you need to connect with a tool that can run Transact-SQL statements on the SQL Server. The following steps install the SQL Server command-line tools: sqlcmd and bcp.

1.Import the public repository GPG keys:

```bash
wget -qO- https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
```

2.Register the Microsoft Ubuntu repository:

```bash
sudo add-apt-repository "$(wget -qO- https://packages.microsoft.com/config/ubuntu/16.04/prod.list)"
```


3.Update the sources list and run the installation command with the unixODBC developer package:

```bash
sudo apt-get update
sudo apt-get install -y mssql-tools unixodbc-dev
```

4.为了以后方便使用sqlcmd，把安装目录添加到bash shell环境变量中：
For convenience, add `/opt/mssql-tools/bin/` to your `PATH` environment variable. 
This enables you to run the tools without specifying the full path. 
Run the following commands to modify the `PATH` for both login sessions and interactive/non-login sessions:

```bash
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
source ~/.bashrc
```

5.安装完成后就可以使用sqlcmd tool了。
![sql sqlcmd](/source/images/ubuntu-sqlserver/sqlcmd.png)


关于sqlcmd的使用可以参考：[https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility](https://docs.microsoft.com/en-us/sql/tools/sqlcmd-utility)。

5.使用sqlcmd链接SQL Server  
帐号为：sa  
密码是刚刚是用`sudo /opt/mssql/bin/mssql-conf setup`设置的密码

6.使用

```bash
sqlcmd -U sa
>select * from sysdatabases;
>go

```




—End—


## 迭代

* 2018年02月09日 15：45 初稿

## 参考

- [《SQL 教程》](http://www.w3school.com.cn/sql/)