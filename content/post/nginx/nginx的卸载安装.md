---
title: "Nginx的卸载安装"
date: 2021-09-11T11:40:08+08:00
draft: false
tags: ["nginx"]
categories: ["nginx"]
---

> 本文主要介绍

#### 卸载nginx

1. 首先输入命令 `ps -ef | grep nginx`检查一下nginx服务是否在运行。如果在运行就停止运行，需要在nginx的安装目录下的sbin执行，如果配置了环境就不需要了：

```js
./nginx -s stop
```

1. 查找、删除Nginx相关文件

- 查看Nginx相关文件：

```js
whereis nginx
```

![image-20210911114123437](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210911114123437.png)

- find查找相关文件

```js
find / -name nginx
```

![image-20210911114144347](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210911114144347.png)

- 依次删除find查找到的所有目录：

```js
rm -rf /usr/local/nginx  /usr/local/sbin/nginx /usr/local/nginx-1.13.9/objs/nginx
```

1. 再使用yum清理

```js
yum remove nginx
```

#### 安装nginx

- 添加 Nginx 源

```js
rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
```

- 安装 Nginx

```js
yum install -y nginx
```

- 启动 Nginx

```js
systemctl start nginx.service
```

- 设置开机自启 Nginx

```js
systemctl enable nginx.service
```

- nginx的配置文件在/etc/nginx/nginx.conf，目录在/etc/nginx

  ![img](https://ask.qcloudimg.com/http-save/2726701/b2stkx39b1.png?imageView2/2/w/1620)在这里插入图片描述

  ![img](https://ask.qcloudimg.com/http-save/2726701/trpyg1iqas.png?imageView2/2/w/1620)在这里插入图片描述

- 自定义的配置文件放在/etc/nginx/conf.d

- 项目文件存放在/usr/share/nginx/html/

- 日志文件存放在/var/log/nginx/

- 还有一些其他的安装文件都在/etc/nginx

- 安装完成之后就可以访问：

  ![img](https://ask.qcloudimg.com/http-save/2726701/7z8z1epqas.png?imageView2/2/w/1620)在这里插入图片描述

- 比较之前的源码编译安装配置文件都相对分散一些，不容易找到，所以安装的时候还是源码编译安装比较好。

