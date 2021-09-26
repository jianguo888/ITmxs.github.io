---
title: "十分钟入门Nginx"
date: 2021-09-25T15:30:31+08:00
draft: false
tags: ["nginx"]
categories: ["nginx"]
---

虚拟主机

网站访问量大，需要负载均衡。然而并不是所有网站都如此出色，有的网站，由于访问量太小，需要节省成本，将多个网站部署在同一台服务器上。

例如将两个网站部署在同一台服务器上，两个域名解析到同一个IP地址，但是用户通过两个域名却可以打开两个完全不同的网站，互相不影响，就像访问两个服务器一样，所以叫两个虚拟主机。

```
server {
	listen 80 default_server;
	server_name _;
	return 444; # 过滤其他域名的请求，返回444状态码
}
server {
	listen 80;
	server_name www.aaa.com; # www.aaa.com域名
	location / {
		proxy_pass http://localhost:8080; # 对应端口号8080
	}
}
server {
	listen 80;
	server_name www.bbb.com; # www.bbb.com域名
	location / {
		proxy_pass http://localhost:8081; # 对应端口号8081
	}
}

```

在服务器8080和8081分别开了一个应用，客户端通过不同的域名访问，根据server_name可以反向代理到对应的应用服务器。

虚拟主机的原理是通过HTTP请求头中的Host是否匹配server_name来实现的，有兴趣的同学可以研究一下HTTP协议。

另外，server_name配置还可以过滤有人恶意将某些域名指向你的主机服务器。





# 启动

```yaml
nginx
```

# 重启

```yaml
nginx -s reopen
```

# 停止

```yaml
nginx -s stop
```

# 热加载

```yaml
./nginx -s reload
```

# 测试修改配置文件是否正常

```yaml
nginx -t
```

# 默认配置文件位置

```yaml
/usr/local/nginx/conf/
```

# 调整Nginx基本配置

```yaml
/usr/local/nginx/conf/nginx.conf
```

# 自定义的Nginx配置放到哪里

```yaml
/usr/local/nginx/servers
```

# 也可以自定义只要放被include就可以

```yaml
nginx
```

![image-20210925113217243](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210925113217243.png)

# 安全退出

```yaml
./nginx -s quit
```

# 负载均衡

1.轮询2.权重3.fair4.url_hash 5.ip_hash

## 1、轮询（默认）

每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。

```nginx
upstream backserver {
    server 192.168.0.14;
    server 192.168.0.15;
}
```

## 2、weight

指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的
情况。

```routeros
upstream backserver {
    server 192.168.0.14 weight=3;
    server 192.168.0.15 weight=7;
}
```

权重越高，在被访问的概率越大，如上例，分别是30%，70%。

## 3、ip_hash

上述方式存在一个问题就是说，在负载均衡系统中，假如用户在某台服务器上登录了，那么该用户第二次请求的时候，因为我们是负载均衡系统，每次请求都会重新定位到服务器集群中的某一个，那么***已经登录某一个服务器的用户再重新定位到另一个服务器，其登录信息将会丢失，这样显然是不妥的\***。
我们可以采用***ip_hash\***指令解决这个问题，如果客户已经访问了某个服务器，当用户再次访问时，会将该请求通过***哈希算法，自动定位到该服务器\***。
每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决***session的问题\***。

```roboconf
upstream backserver {
    ip_hash;
    server 192.168.0.14:88;
    server 192.168.0.15:80;
}
```

## 4、fair（第三方）

按后端服务器的响应时间来分配请求，响应时间短的优先分配。

```abnf
upstream backserver {
    server server1;
    server server2;
    fair;
}
```

## 5、url_hash（第三方）

按访问url的hash结果来分配请求，使每个url定向到同一个（对应的）后端服务器，后端服务器为缓存时比较有效。

```nginx
upstream backserver {
    server squid1:3128;
    server squid2:3128;
    hash $request_uri;
    hash_method crc32;
}
```

在需要使用负载均衡的server中增加

```scss
proxy_pass http://backserver/; 
upstream backserver{ 
    ip_hash; 
    server 127.0.0.1:9090 down; (down 表示单前的server暂时不参与负载) 
    server 127.0.0.1:8080 weight=2; (weight 默认为1.weight越大，负载的权重就越大) 
    server 127.0.0.1:6060; 
    server 127.0.0.1:7070 backup; (其它所有的非backup机器down或者忙的时候，请求backup机器) 
} 
```

max_fails ：允许请求失败的次数默认为1.当超过最大次数时，返回proxy_next_upstream 模块定义的错误

fail_timeout:max_fails次失败后，暂停的时间
配置实例：

```axapta
#user  nobody;

worker_processes  4;
events {
# 最大并发数
worker_connections  1024;
}
http{
    # 待选服务器列表
    upstream myproject{
        # ip_hash指令，将同一用户引入同一服务器。
        ip_hash;
        server 125.219.42.4 fail_timeout=60s;
        server 172.31.2.183;
    }

    server{
        # 监听端口
        listen 80;
        # 根目录下
        location / {
        # 选择哪个服务器列表
            proxy_pass http://myproject;
        }

    }
}
```

# 配置文件结构

性能在全局

![image-20210925122312778](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210925122312778.png)

![image-20210925124053765](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210925124053765.png)