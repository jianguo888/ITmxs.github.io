---
title: "goland使用go mod模式的步骤详解"
date: 2021-09-03T23:10:11+08:00
draft: false
tags: ["go"]
categories: ["go"]
 
---

# goland使用go mod模式的步骤详解

使用go mod之后，想要在goland中有代码提示，有两种方式，一种是使用gopath下的goimport工具，另一种是使用gomod自身的管理工具

我是用的是非gopath的方式，每次新建项目后总是报错

> go list -m: can't compute 'all' using the vendor directory
>  (Use -mod=mod or -mod=readonly to bypass.)，

得不到想要的效果，最后终于发现是步骤不对

第一步：创建空文件夹

第二步：goland以项目方式打开文件夹

第三步：设置goland中的配置，goroot，gomodule

![img](https://gitee.com/itmxs/images/raw/master/img/1u9TsjcXd633N1wv.png)

第四步：执行go mod init + 项目名，这个截图的地方多一个go modules，用于存放下载的包的

![img](https://gitee.com/itmxs/images/raw/master/img/1u9TsjcXd633N1wv.png)

第五步：创建.go文件，然后写上代码

第六步：执行go mod tidy，下载所需的包，也会删除多余的包

然后代码就有提示了。完成。