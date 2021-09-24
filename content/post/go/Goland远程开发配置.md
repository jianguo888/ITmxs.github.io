---
title: "Goland远程开发配置"
date: 2021-09-24T14:04:24+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

前言Xjg201314
项目开发过程中，一般采用“本地编写+远端编译”的开发模式，即在本地编写完代码然后将代码放到远端的编译机器上编译调试。这种开发模式，我们需要采用远程开发的方式来提高开发效率，即编写的代码能够自动同步到远端，感觉上像是直接编辑远端的代码。

为什么要采用“本地编写+远端编译”的开发模式，而不是“本地编写+本地编译”呢，因为程序上线发布前的编译环境一般和本地不一样，所以一般我们都采用在本地（自己的电脑）上开发，然后将代码同步到远端的编译机进行编译。

用过 PhpStorm 的人一定会对其远程开发的功能赞不绝口，同样是 JetBrains 公司出品的 GoLand 对远程开发也有非常好的支持。本文将讨论使用 GoLand 如何实现远程开发，本文 GoLand 版本为 Windows 2020.2.1 。

# step 1 连接配置

```
Tools > Deployment > + SFTP > Configuration > Connection。
```

增加配置如下，其中“云虚拟机”是我给远程主机取的名字。


![image-20210924140437832](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210924140437832.png)

# step 2 映射配置

选择本地代码和远程代码目录的映射关系。

![image-20210924141634345](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210924141634345.png)

# step 3 上传代码至远端

可以手动上传代码至远端。点击 `Tools > Deployment > Upload to`。

![image-20210924142031317](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210924142031317.png)



# step 4 增加自动上传配置（可选）

如果想每次 Ctrl+S 保存之后都能直接同步到远程，需要增加自动上传配置，在 `Tools > Deployment > Options...` 页面配置如下。

![image-20210924142105613](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210924142105613.png)

# step 5 直连开发机进行命令行操作（可选）

如果想直接在 GoLand 命令窗口操作远端开发机，进行编译调试运行，那么可以直连开发机进行命令行操作。

由于上一步已经配置了 SFTP，这里直接点击 Tools > Start SSH Session…，选择已经配置好的主机进行连接即可直连开发机


![image-20210924142236002](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210924142236002.png)
