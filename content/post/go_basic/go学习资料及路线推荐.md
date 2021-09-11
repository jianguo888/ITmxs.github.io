---
title: "Go学习资料及路线推荐"
date: 2021-09-10T11:14:29+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["go"]
categories: ["go"]
---

## go 语言网站

如下为用到的资源网站，整理如下

### go 语言中文网 [books.studygolang.com/](https://books.studygolang.com/)

### go 语言 learnku 社区 [learnku.com/go](https://learnku.com/go)

### go 语言顶级 goer [www.topgoer.com/](http://www.topgoer.com/)

### go 指南 [tour.studygolang.com/](http://tour.studygolang.com/)

[20 个学习 GO 的网站 ](https://gobea.cn/blog/detail/RoRD4Mr3.html)
开始使用 [go](https://docs.microsoft.com/zh-cn/learn/paths/go-first-steps/)

[gin](https://gin-gonic.com/)

[gin中文](https://gin-gonic.com/zh-cn/docs/)



基本语法
学习任何一门语言，最基础的就是语法了，语法没得说的，但是相对于其它的老式编程语言还是有点差距的，推荐连接：

菜鸟教程 https://www.runoob.com/go/go-tutorial.html

官方文档 https://golang.google.cn/doc/

web 框架学习
作为一名 web 开发者，学习 web 框架是有必要的，在这里我深入研究了 Gin 和 Beego，包括它们的使用和设计，其实源代码并不复杂，
这 2 个框架都只是在 go http 库上面套个壳而已，没啥难的，一天即看完所有的源代码，源代码不是重点，重点是他们的设计思路

Beego 官方链接https://beego.me/

Gin 官方链接

http2 协议
作为 http 协议的后继者 http2，真可谓是进行了大大阔斧的改革，学习 http2，主要是为了学子 GRPC，因为 GRPC 基于 http2 进行开发，我觉得所有的 go 开发人员都应该学习下 http2 协议，
看一下它是如何设计出来的，下面列出 http2 的 retf 地址：

HTTP2 RETF 文档 https://httpwg.org/specs/rfc7540.html

至于 HTTP2 的 go 实现，你应该看看 go 的官方包：

HTTP2 官方实现 https://pkg.go.dev/golang.org/x/net/http2

GRPC
GRPC 是啥，这里就不多说了，搞 Go 开发，不知道这个咋行呢？它的东家是 Google，用法嘛，没啥难的，如果你有兴趣的话，可以看看 Google 的 GRPC 实现，

Go GRPC 实现 https://github.com/grpc/grpc-go

源代码并不复杂，学习它主要是为了理解它的设计，以便用于自己以后多的开发

微服务
我学习的是 go-mirco，其实也主要是学习它的设计思路，框架本身没啥难的，相对于 etcd 来说，就是小意思了，地址：

go-micro https://github.com/asim/go-micro



https://learnku.com/articles/56078

## 学习寄语

我始终相信，做难事，终有所得，学习没啥大的技巧，唯有坚持才是制胜法宝，还是那句话：

**山重水复疑无路，聊暗花明又一村**

![goph](https://luckly007.oss-cn-beijing.aliyuncs.com/image/goph.png)