---
title: "《Nginx学习笔记》"
subtitle: ""
date: 2021-03-17T07:18:09+08:00
lastmod: 2021-03-1707:18:09+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Nginx"]
categories: ["Nginx"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/nginx.jpg"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/nginx.jpg"

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



> 本文主要介绍nginx相关基础及简单使用

<!--more-->

[TOC]

**内容介绍**
**1、nginx基本概念。**

> (1) nginx是什么，能做什么事情
>
> (2) 反向代理。
>
> (3) 负载均衡。
>
> (4) 动静分离



**2、nginx 安装、常用命令和配置文件**

> (1)在liunx系统中安装nginx.
>
> (2) nginx常用命令。
>
> (3) nginx 配置文件。

**3、nginx 配置实例 1-反向代理**

**4、nginx 配置实例 2-负载均衡**

**5、nginx 配置实例 3-动静分离。**

**6、nginx配置高可用集群**

**7、nginx 原理。**

------

### 一、Nginx 简介

#### 1、什么是NGINX

```
	Nginx ("engine x")是一个高性能的HTTP和反向代理服务器，特点是占有内存少，并发能
力强，事实上nginx的并发能力确实在同类型的网页服务器中表现较好
    Nginx专为性能优化而开发，性能是其最重要的考量，实现上非常注重效率，能经受高负载
的考验，有报告表明能支持高达50000个并发连接数。
```

![img](https://raw.githubusercontent.com/dunwu/images/dev/cs/web/nginx/nginx.jpg)

#### **2、反向代理**

**a. 正向代理**
在客户端(浏览器)配置代理服务器，通过代理服务器进行互联网访问

```
{% asset_img image-20200606144302429.png  正向代理 %}
```

**b. 反向代理**
反向代理，其实客户端对代理是无感知的，因为客户端不需要任何配置就可以访问，我们只
需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器获取数据后,在返
回给客户端，此时反向代理服务器和目标服务器对外就是一个服务器，暴露的是代理服务器
地址，隐藏了真实服务器IP地址。

反向代理（Reverse Proxy）方式是指以代理服务器来接受 internet 上的连接请求，然后将请求转发给内部网络上的服务器，并将从服务器上得到的结果返回给 internet 上请求连接的客户端，此时代理服务器对外就表现为一个反向代理服务器。

![img](https://raw.githubusercontent.com/dunwu/images/dev/cs/web/nginx/reverse-proxy.png)

![反向代理](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200606150932688.png)

#### 3、负载均衡

单个服务器解决不了，我们增加服务器的数量，然后将请求分发到各个服务器上,将原先
请求集中到单个服务器上的情况改为将请求分发到多个服务器上,将负载分发到不同的服
务器，也就是我们所说的负载均衡

![负载均衡](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200606154956614.png)

#### 4、动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同的服务器来解析，加快解析速
度。降低原来单个服务器的压力。

![动静分离](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200606155620164.png)

### 二、Nginx 安装

下面的操作是以Centos7为例

**1. 使用远程连接工具连接Centos7操作系统**
**2. 安装nginx相关依赖**

```
gcc
pcre
openssl
zlib
```

① 安装 nginx 需要先将官网下载的源码进行编译，编译依赖 gcc 环境，如果没有 gcc 环境，则需要安装：

```
$ yum install gcc-c++
```

② PCRE(Perl Compatible Regular Expressions) 是一个Perl库，包括 perl 兼容的正则表达式库。nginx 的 http 模块使用 pcre 来解析正则表达式，所以需要在 linux 上安装 pcre 库，pcre-devel 是使用 pcre 开发的一个二次开发库。nginx也需要此库。命令：

```
$ yum install -y pcre pcre-devel
```

③ zlib 库提供了很多种压缩和解压缩的方式， nginx 使用 zlib 对 http 包的内容进行 gzip ，所以需要在 Centos 上安装 zlib 库。

```
$ yum install -y zlib zlib-devel
```

④ OpenSSL 是一个强大的安全套接字层密码库，囊括主要的密码算法、常用的密钥和证书封装管理功能及 SSL 协议，并提供丰富的应用程序供测试或其它目的使用。
nginx 不仅支持 http 协议，还支持 https(即在ssl协议上传输http)，所以需要在 Centos 安装 OpenSSL 库。

```
$ yum install -y openssl openssl-devel
```

**3. 安装Nginx**

① 下载nginx，两种方式

> a. 直接下载`.tar.gz`安装包，地址：https://nginx.org/en/download.html

```
 cd /usr/local/src/
```



> b. **使用`wget`命令下载(推荐)**。确保系统已经安装了wget，如果没有安装，执行 yum install wget 安装。

```
$ wget -c https://nginx.org/download/nginx-1.20.tar.gz
```

![image-20210911115041034](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210911115041034.png)



② 依然是直接命令：

```
$ tar -zxvf nginx-1.20.0.tar.gz
$ cd nginx-1.20.0
```

![image-20210911115212076](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210911115212076.png)

③ 配置：

其实在 nginx-1.12.0 版本中你就不需要去配置相关东西，默认就可以了。当然，如果你要自己配置目录也是可以的。
1.使用默认配置

```
$ ls

```

2.自定义配置(不推荐)

```
$ ./configure \
--prefix=/usr/local/nginx \
--conf-path=/usr/local/nginx/conf/nginx.conf \
--pid-path=/usr/local/nginx/conf/nginx.pid \
--lock-path=/var/lock/nginx.lock \
--error-log-path=/var/log/nginx/error.log \
--http-log-path=/var/log/nginx/access.log \
--with-http_gzip_static_module \
--http-client-body-temp-path=/var/temp/nginx/client \
--http-proxy-temp-path=/var/temp/nginx/proxy \
--http-fastcgi-temp-path=/var/temp/nginx/fastcgi \
--http-uwsgi-temp-path=/var/temp/nginx/uwsgi \
--http-scgi-temp-path=/var/temp/nginx/scgi
```

> 注：将临时文件目录指定为/var/temp/nginx，需要在/var下创建temp及nginx目录

④ 编辑安装

```
$ make && make install
```

查看版本号(`使用nginx操作命令前提条件:必须进入nginx的目录/usr/local/nginx/sbin`.)

```
$ ./nginx -v
```

查找安装路径：

```
$ whereis nginx
```

⑤ 启动，停止nginx

直接启动



```
 /usr/local/webserver/nginx/sbin/nginx

```

## Nginx 其他命令

以下包含了 Nginx 常用的几个命令：

```
/usr/local/nginx/sbin/nginx -s reload            # 重新载入配置文件
/usr/local/nginx/sbin/nginx -s reopen            # 重启 Nginx
/usr/local/nginx/sbin/nginx -s stop              # 停止 Nginx
```

```
$ cd /usr/local/nginx/sbin/
$ ./nginx 
$ ./nginx -s stop
$ ./nginx -s quit
$ ./nginx -s reload
```

```
nginx -s stop       快速关闭Nginx，可能不保存相关信息，并迅速终止web服务。
nginx -s quit       平稳关闭Nginx，保存相关信息，有安排的结束web服务。
nginx -s reload     因改变了Nginx相关配置，需要重新加载配置而重载。
nginx -s reopen     重新打开日志文件。
nginx -c filename   为 Nginx 指定一个配置文件，来代替缺省的。
nginx -t            不运行，仅仅测试配置文件。nginx 将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。
nginx -v            显示 nginx 的版本。
nginx -V            显示 nginx 的版本，编译器版本和配置参数。
```

如果不想每次都敲命令，可以在 nginx 安装目录下新添一个启动批处理文件**startup.bat**，双击即可运行。内容如下：

```
@echo off
rem 如果启动前已经启动nginx并记录下pid文件，会kill指定进程
nginx.exe -s stop

rem 测试配置文件语法正确性
nginx.exe -t -c conf/nginx.conf

rem 显示版本信息
nginx.exe -v

rem 按照指定配置去启动nginx
nginx.exe -c conf/nginx.conf
```



如果是运行在 Linux 下，写一个 shell 脚本，大同小异。

`nginx.conf` 配置文件如下：

> ***注：`conf/nginx.conf` 是 nginx 的默认配置文件。你也可以使用 nginx -c 指定你的配置文件\***

```
#运行用户
#user somebody;

#启动进程,通常设置成和cpu的数量相等
worker_processes  1;

#全局错误日志
error_log  D:/Tools/nginx-1.10.1/logs/error.log;
error_log  D:/Tools/nginx-1.10.1/logs/notice.log  notice;
error_log  D:/Tools/nginx-1.10.1/logs/info.log  info;

#PID文件，记录当前启动的nginx的进程ID
pid        D:/Tools/nginx-1.10.1/logs/nginx.pid;

#工作模式及连接数上限
events {
    worker_connections 1024;    #单个后台worker process进程的最大并发链接数
}

#设定http服务器，利用它的反向代理功能提供负载均衡支持
http {
    #设定mime类型(邮件支持类型),类型由mime.types文件定义
    include       D:/Tools/nginx-1.10.1/conf/mime.types;
    default_type  application/octet-stream;

    #设定日志
	log_format  main  '[$remote_addr] - [$remote_user] [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log    D:/Tools/nginx-1.10.1/logs/access.log main;
    rewrite_log     on;

    #sendfile 指令指定 nginx 是否调用 sendfile 函数（zero copy 方式）来输出文件，对于普通应用，
    #必须设为 on,如果用来进行下载等应用磁盘IO重负载应用，可设置为 off，以平衡磁盘与网络I/O处理速度，降低系统的uptime.
    sendfile        on;
    #tcp_nopush     on;

    #连接超时时间
    keepalive_timeout  120;
    tcp_nodelay        on;

	#gzip压缩开关
	#gzip  on;

    #设定实际的服务器列表
    upstream zp_server1{
        server 127.0.0.1:8089;
    }

    #HTTP服务器
    server {
        #监听80端口，80端口是知名端口号，用于HTTP协议
        listen       80;

        #定义使用www.xx.com访问
        server_name  www.helloworld.com;

		#首页
		index index.html

		#指向webapp的目录
		root D:\01_Workspace\Project\github\zp\SpringNotes\spring-security\spring-shiro\src\main\webapp;

		#编码格式
		charset utf-8;

		#代理配置参数
        proxy_connect_timeout 180;
        proxy_send_timeout 180;
        proxy_read_timeout 180;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarder-For $remote_addr;

        #反向代理的路径（和upstream绑定），location 后面设置映射的路径
        location / {
            proxy_pass http://zp_server1;
        }

        #静态文件，nginx自己处理
        location ~ ^/(images|javascript|js|css|flash|media|static)/ {
            root D:\01_Workspace\Project\github\zp\SpringNotes\spring-security\spring-shiro\src\main\webapp\views;
            #过期30天，静态文件不怎么更新，过期可以设大一点，如果频繁更新，则可以设置得小一点。
            expires 30d;
        }

        #设定查看Nginx状态的地址
        location /NginxStatus {
            stub_status           on;
            access_log            on;
            auth_basic            "NginxStatus";
            auth_basic_user_file  conf/htpasswd;
        }

        #禁止访问 .htxxx 文件
        location ~ /\.ht {
            deny all;
        }

		#错误处理页面（可选择性配置）
		#error_page   404              /404.html;
		#error_page   500 502 503 504  /50x.html;
        #location = /50x.html {
        #    root   html;
        #}
    }
}
```

好了，让我们来试试吧：

1. 启动 webapp，注意启动绑定的端口要和 nginx 中的 `upstream` 设置的端口保持一致。
2. 更改 host：在 C:\Windows\System32\drivers\etc 目录下的 host 文件中添加一条 DNS 记录

查询nginx进程：

```
$ ps aux|grep nginx
```

启动成功后，在浏览器可以看到这样的页面：

![image-20210911120628054](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210911120628054.png)

![image-20210829090820571](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210829090820571.png)



**实现工作**

修改nginx配置文件, `cd./`

`nginx.conf`

![image-20210829090845318](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210829090845318.png)

#### 例2 反向代理

**1、实现效果**

> 使用nginx反向代理，根据访问的路径跳转到不同端口的服务中。
> nginx.监听端口为9001，。
> 访问http://127.0.0.1:9001/edu/ 直接跳转到127.0.0.1:8081
> 访问http://127.0.0.1:9001/vod/ 直接跳转到127.0.0.1:8082

**2、准备工作**

> (1) 准备两个tomcat服务器，一个8080端口，一个8081端口
>
> (2) 创建文件夹和测试页面。

**3、具体nginx配置**

```
$ vi /usr/local/nginx/conf/nginx.conf
```

![nginx配置](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607121227387.png)

> (1) 找到nginx配置文件，进行反向代理配置。
>
> (2) 开放对外访问的端口号9001
>
> (3) 重启nginx服务器，使配置文件生效

**4、最终测试**

![edu请求示例](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607121539858.png)

**5、补充`location`部分**

location指令说明。
该指令用于匹配URL。。
语法如下:

```
location [ = | ~ | ~* | ^~] uri {

}
1、=: 用于不含正则表达式的uri前，要求请求字符串与uri严格匹配，如果匹配成功，
	就停止继续向下搜索并立即处理该请求
2、~: 用于表示uri包含正则表达式，并且区分大小写
3、~*: 用于表示uri包含正则表达式，并且不区分大小写
4、^~: 用于不含正则表达式的uri前，要求Nginx服务器找到标识uri和请求字
	符串匹配度最高的location后，立即使用此location处理请求，而不再使用location
	块中的正则uri和请求字符串做匹配
注意: 如果uri包含正则表达式，则必须要有~或者~*标识。
```

### 四、配置实例2 负载均衡

**1、实现效果**

> (1) 浏览器地址栏输入地址http://192.168.xxx.xxx/edu/index.html, 负载均衡效果，平均到8080
> 和8081端口中，

**2、准备工作**

> (1) 准备两台tomcat服务器，一 台8080， 一台8081
>
> (2) 在两台tomcat里面webapps目录中，创建名称是edu文件夹，在edu文件夹中创建
> 页面index.html,用于测试。

**3、在nginx的配置文件中进行负载均衡的配置**

![负载均衡配置](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607124508114.png)

**4、效果**

![负载均衡](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607124828732.png) ![负载均衡](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607124843736.png)

#### 负载分配策略

在linux下有Nginx、LVS、 Haproxy 等等服务可以提供负载均衡服务，而且Nginx提供了几种分配方式(策略):。

- **1、轮询(默认)**

  每个请求按时间顺序逐一分配到不 同的后端服务器，如果后端服务器down掉，能自动剔除

- **2、weight**
  weight代表权重默认为1,权重越高被分配的客户端越多。
  指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。例如: 。

- **3、ip hash**

  每个请求按访问ip的hash结果分配, 这样每个访客固定访问一个后端服务器,可以解诀session的问题。例如:

  ```
  upstream server pool{
    ip_ hash
    server 192.168.5.21:80
    server 192.168.5.22:80
  }
  ```

- **4、fair (第三方)**
  按后端服务器的响应时间来分配请求，响应时间短的优先分配

  ```
  upstream server_pool 
  	server 192.168.5.21:80;
  	server 192.168.5.22:80;
  	fair;
  }
  ```

### 五、配置实例3 动静分离

![动静分离](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607153752466.png)

```
通过location指定不同的后缀名实现不同的请求转发。通过expires参数设置，可以使浏
览器缓存过期时间，减少与服务器之前的请求和流量。具体Expires定义: 是给一个资源
设定一个过期时间，也就是说无需去服务端验证，直接通过浏览器自身确认是否过期即可，
所以不会产生额外的流量。此种方法非常适合不经常变动的资源。(如果经常更新的文件，
不建议使用Expires来缓存)，如果设置3d, 表示在这3天之内访问这个URL, 发送一
个请求，比对服务器该文件最后更新时间没有变化，则不会从服务器抓取，返回状态码304,
如果有修改，则直接从服务器重新下载，返回状态码200。。
```

**2、准备工作**

> (1) 在liunx系统中准备静态资源，用于进行访问
>
> /data/image 图片文件夹
>
> /data/www html文件夹

**3、具体配置**

> (1) 在nginx配置文件中进行配置

![动静分离](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607200057983.png)

4、实际测试

```
http://192.168.1.112/www/index.html
http://192.168.1.112/image/1.jpg
```

![img](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607200424689.png)

上图是因为`autoindex on`这个设置生效的

### 六、Nginx配置高可用集群

**1、什么是nginx高可用**

![img](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607205253505.png)

> (1) 需要两台nginx服务器。
> (2) 需要keepalived
> (3) 需要虚拟ip

**2、配置高可用的准备工作**

> (1) 需要两台服务器192.168.17.129 和192.168.17.1314
> (2) 在两台服务器安装nginx.
> (3) 在两合服务器安装keepalived.

**3、在两台服务器安装keepalived**
使用yum命令进行安装

```
$ yum install keepalived
$ rpm -q -a keepalived    #查看是否已经安装上
```

默认安装路径: /etc/keepalived

安装之后，在etc里面生成目录keepalived, 有配置文件keepalived.conf

**4、完成高可用配置(主从配置)**

(1)修改keepalived的配置文件`keepalived.conf`为：

```
global_defs {
	notification_email {
	  acassen@firewall.loc
	  failover@firewall.loc
	  sysadmin@firewall.loc
	}
	notification_email_from Alexandre.Cassen@firewall.loc
	smtp_ server 192.168.17.129
	smtp_connect_timeout 30
	router_id LVS_DEVEL	# LVS_DEVEL这字段在/etc/hosts文件中看；通过它访问到主机
}

vrrp_script chk_http_ port {
	script "/usr/local/src/nginx_check.sh"
	interval 2   # (检测脚本执行的间隔)2s
	weight 2  #权重，如果这个脚本检测为真，服务器权重+2
}

vrrp_instance VI_1 {
	state BACKUP   # 备份服务器上将MASTER 改为BACKUP
	interface ens33 //网卡名称
	virtual_router_id 51 # 主、备机的virtual_router_id必须相同
	priority 100   #主、备机取不同的优先级，主机值较大，备份机值较小
	advert_int 1	#每隔1s发送一次心跳
	authentication {	# 校验方式， 类型是密码，密码1111
        auth type PASS
        auth pass 1111
    }
	virtual_ipaddress { # 虛拟ip
		192.168.17.50 // VRRP H虛拟ip地址
	}
}
```

(2)在路径/usr/local/src/ 下新建检测脚本 nginx_check.sh

nginx_check.sh

```
#! /bin/bash
A=`ps -C nginx -no-header | wc - 1`
if [ $A -eq 0];then
	/usr/local/nginx/sbin/nginx
	sleep 2
	if [`ps -C nginx --no-header| wc -1` -eq 0 ];then
		killall keepalived
	fi
fi
```

(3) 把两台服务器上nginx和keepalived启动

```
$ systemctl start keepalived.service		#keepalived启动
$ ps -ef I grep keepalived		#查看keepalived是否启动
```

**5、最终测试**

(1) 在浏览器地址栏输入虚拟ip地址192.168.17.50

(2) 把主服务器(192.168.17.129) nginx和keealived停止，再输入192.168.17.50.

```
$ systemctl stop keepalived.service  #keepalived停止
```

### 七、Nginx原理解析

**1、master和worker**

![img](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607222133869.png)

**2、worker如何进行工作的**

![img](https://nishigouzi.github.io/2020/06/07/Nginx笔记/image-20200607222501486.png)

**3、一个master和多个woker的好处**
(1) 可以使用nginx -s reload热部署。

```
首先，对于每个worker进程来说，独立的进程，不需要加锁，所以省掉了锁带来的开销，
同时在编程以及问题查找时，也会方便很多。其次,采用独立的进程，可以让互相之间不会
影响，一个进程退出后，其它进程还在工作，服务不会中断，master进程则很快启动新的
worker进程。当然，worker进程的异常退出，肯定是程序有bug了，异常退出，会导致当
前worker.上的所有请求失败，不过不会影响到所有请求，所以降低了风险。
```

**4、设置多少个woker合适**

```
Nginx同redis类似都采用了io多路复用机制，每个worker都是一个独立的进程， 但每个进
程里只有一个主线程，通过异步非阻塞的方式来处理请求，即使是 千上万个请求也不在话
下。每个worker的线程可以把一个cpu的性能发挥到极致。所以worker数和服务器的cpu
数相等是最为适宜的。设少了会浪费cpu,设多了会造成cpu频繁切换上下文带来的损耗。
# 设置worker数量
worker.processes 4 

# work绑定cpu(4work绑定4cpu)
worker_cpu_affinity 0001 0010 0100 1000

# work绑定cpu (4work绑定8cpu中的4个)
worker_cpu_affinity 0000001 00000010 00000100 00001000
```

**5、连接数worker_ connection**

```
这个值是表示每个worker进程所能建立连接的最大值，所以，一个nginx 能建立的最大连接数，应该是worker.connections * worker processes。当然，这里说的是最大连接数，对于HTTP 请求本地资源来说，能够支持的最大并发数量是worker.connections * worker processes,如果是支持http1.1的浏览器每次访问要占两个连接，所以普通的静态访问最大并发数是: worker.connections * worker.processes / 2, 而如果是HTTP作为反向代理来说，最大并发数量应该是worker.connections * worker_proceses/4. 因为作为反向代理服务器，每个并发会建立与客户端的连接和与后端服务的连接，会占用两个连接.
```

> 第一个: 发送请求，占用了woker的几个连接数?
> 答案: 2或者4个。
>
> 第二个: nginx有一个master,有四个woker,每个woker支持最大的连接数1024,支持的最大并发数是多少?
> 答案：普通的静态访问最大并发数是: worker connections * worker processes /2，
> 而如果是HTTP作为反向代理来说，最大并发数量应该是worker connections * worker processes/4