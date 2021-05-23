---
title: "《Linxu下安装Mysql教程》"
subtitle: ""
date: 2021-04-23T07:18:48+08:00
lastmod: 2021-04-23T07:18:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["linux","Mysql","安装教程"]
categories: ["linux","Mysql","安装教程"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/linux.jpg"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/linux.jpg"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment:
  enable: true
mapbox:
  accessToken: ""
---



> 本教程手把手教你如何在 Linux 安装 MySQL 数据库，以 CentOS 8为例。

4.23日，我买了阿里云的服务器centos7，安装mysql，特此记录一下，方便自己以后查找使用

<!--more-->

[TOC]



### 1. 下载并安装 MySQL 官方的 Yum Repository

```shell
wget https://repo.mysql.com//mysql80-community-release-el7-1.noarch.rpm
```

使用上面的命令就直接下载了安装用的Yum Repository，大概25KB的样子，然后就可以直接yum安装了。

2、然后进行repo的安装： 

```shell
rpm -ivh mysql80-community-release-el7-1.noarch.rpm
```

执行完成后会在/etc/yum.repos.d/目录下生成两个repo文件mysql-community.repo mysql-community-source.repo

之后就开始安装MySQL服务器。

```shell
 yum install mysql-server
```

这步可能会花些时间，安装完成后就会覆盖掉之前的mariadb。

![Linux 安装 MySQL 8 数据库1.png](https://luckly007.oss-cn-beijing.aliyuncs.com/img/00d8cefb-c782-4155-a8a9-057afe23ec41.png)

然而，好像这些信息对我们来说并没有什么用处，什么守护进程没启动，我们也不能知如何去启动这些，这个时候我们查看一下mysql自带的告警日志，路径一般在/var/log/mysqld.log下面

```
tail -200f  /var/log/mysqld.log
```

https://blog.csdn.net/liurui50/article/details/105478422



### 2. MySQL数据库设置

#### 2.1. 设置不区分大小写

```shell
vim /etc/my.cnf
```

在[mysqld]下，添加以下内容

```shell
#让MYSQL大小写敏感(1-不敏感，0-敏感)
lower_case_table_names=1
```

#### 2.2. 启动MySQL

```shell
systemctl start mysqld.service 
```

#### 2.3. 查看MySQL运行状态

```shell
systemctl status mysqld.service
```

![Linux 安装 MySQL 8 数据库2.png](https://luckly007.oss-cn-beijing.aliyuncs.com/img/00d8cefb-c782-4155-a8a9-057afe23ec41.png)

#### 2.4. 设置密码

此时MySQL已经开始正常运行，不过要想进入MySQL还得先找出此时root用户的密码，通过如下命令可以在日志文件中找出密码：

```shell
grep "password" /var/log/mysqld.log
```

![Linux 安装 MySQL 8 数据库3.png](https://luckly007.oss-cn-beijing.aliyuncs.com/img/00d8cefb-c782-4155-a8a9-057afe23ec41.png)

进入数据库：

```shell
mysql -uroot -p
```

输入刚刚上面的初始密码

![Linux 安装 MySQL 8 数据库4.png](https://luckly007.oss-cn-beijing.aliyuncs.com/img/00d8cefb-c782-4155-a8a9-057afe23ec41.png)

此时不能做任何事情，因为MySQL默认必须修改密码之后才能操作数据库：

```SQL
ALTER USER 'root'@'localhost' IDENTIFIED BY 'new password';
```

new password为你设置的root密码 这里有个问题，新密码设置的时候如果设置的过于简单会报错：

![Linux 安装 MySQL 8 数据库5.png](https://luckly007.oss-cn-beijing.aliyuncs.com/img/00d8cefb-c782-4155-a8a9-057afe23ec41.png) ![Linux 安装 MySQL 8 数据库6.png](https://luckly007.oss-cn-beijing.aliyuncs.com/img/00d8cefb-c782-4155-a8a9-057afe23ec41.png)

尽量把密码设置复杂一些

![Linux 安装 MySQL 8 数据库7.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/8e66d86f-a429-4d52-9213-71150278f093.png)

#### 2.5. 设置远程访问

此时不能远程访问，如Navicat访问不了

![Linux 安装 MySQL 8 数据库8.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/ab533966-d79b-41cf-8cad-b13520fc3b23.png)居中

##### 2.5.1 开放端口

（1）开放3306端口

```shell
firewall-cmd --permanent --zone=public --add-port=3306/tcp
```

（2）如果提示FirewallD is not running，如下图所示

![Linux 安装 MySQL 8 数据库9.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/255341b9-e8be-45ed-8b94-70e39d501ec2.png)

（3）通过systemctl status firewalld查看firewalld状态，发现当前是dead状态，即防火墙未开启。

![Linux 安装 MySQL 8 数据库10.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/76f1334f-29a2-482c-bede-f935918e3c98.png)

（4）通过systemctl start firewalld开启防火墙，没有任何提示即开启成功。

![Linux 安装 MySQL 8 数据库11.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/18db6527-c26f-4bf2-ad8a-47b5dfa67671.png)

（5）再次通过systemctl status firewalld查看firewalld状态，显示running即已开启了。

![Linux 安装 MySQL 8 数据库12.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/1b408e36-9ba5-44c7-a4c9-d11e6e10739a.png)

（6）如果要关闭防火墙设置，可能通过systemctl stop firewalld这条指令来关闭该功能。

![Linux 安装 MySQL 8 数据库13.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/5a88a2db-3bbb-44ab-9b45-68a28af5f179.png)

（7）再次执行firewall-cmd --permanent --zone=public --add-port=3306/tcp ，提示success，表示设置成功，这样就可以继续后面的设置了。

![Linux 安装 MySQL 8 数据库14.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/55085161-ee77-4b89-bd36-9f5ed60e3598.png)

（8）刷新

```shell
firewall-cmd --reload
```

![Linux 安装 MySQL 8 数据库15.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/8e79b192-4b9d-4cf2-a19b-cb801a588938.png)

#### 2.5.2. root远程访问

当防火墙开启后，Navicat访问出现如下图：

![Linux 安装 MySQL 8 数据库16.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/0b2fa002-f236-475a-a044-619d27fe824a.png)

（1）设置认证方式。 如果你没有设置认证方式，默认的密码加密方式是：caching_sha2_password，而现在很多客户端工具还不支持这种加密认证方式，连接测试的时候就会报错：

```shell
client does not support  authentication protocol requested by server; consider upgrading MySQL client
```

这里的错误信息就是不支持身份认证方式，没关系，编辑/etc/my.cnf（编辑命令：vim /etc/my.cnf）文件里面在[mysqld]下面加上这句话即可：

```shell
default_authentication_plugin=mysql_native_password
```

![Linux 安装 MySQL 8 数据库17.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/494fd374-539e-47fa-8f2d-b30b0dd00b6b.png)

（2）登陆到mysql命令行：然后输入密码

```shell
mysql -u root -p
```

![Linux 安装 MySQL 8 数据库18.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/63477ce0-4fcf-41fe-b70e-ff267566a586.png)

（3）进入之后选择mysql库，用户信息都存在这个库的user表中

```SQL
use mysql;
select host, user, authentication_string, plugin from user;
```

![Linux 安装 MySQL 8 数据库19.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/426377bb-751d-4890-9dc3-d191f15721a5.png)居中

可以看到，用户对应的主机是localhost，而不是%，所以不能连接。 （4）授权root用户可以远程登陆

```SQL
update user set host='%' where user = 'root';
flush privileges;
```

![Linux 安装 MySQL 8 数据库20.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/0eca199c-1a32-4a83-a634-44648f0ec276.png)

（5）刷新权限

```SQL
flush privileges;
```

![Linux 安装 MySQL 8 数据库21.png](https://geshanzsq.com/geshanzsq-file/profile/upload/2020/12/07/2fdc9aa1-1f40-4882-8972-6302867667a2.png)



#### 2.6阿里云设置安全组需要开放3306端口


安全组---配置规则---快速添加即可



![image-20210423200433985](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210423200433985.png)

此时，数据库就已经安装配置好了。 如此详细的图文教程，你学会了吗？赶紧收藏并试试吧！

#### 2.7 2059错误的解决方案

MySQL新版本（8以上版本）的用户登录账户加密方式是【caching_sha2_password】，Navicat不支持这种用户登录账户加密方式。

执行

```
show variables like 'default_authentication_plugin';
select host,user,plugin from mysql.user;

ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'mima';
flush privileges;
```

![image-20210423200747492](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210423200747492.png)

![image-20210423200806117](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210423200806117.png)