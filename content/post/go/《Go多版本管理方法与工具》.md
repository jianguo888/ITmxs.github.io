---
title: "《Go多版本管理方法与工具》"
subtitle: ""
date: 2021-05-28T16:47:50+08:00
lastmod: 2021-05-28T16:47:50+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["go"]
categories: ["go"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/f77be1f477e980732d57475d3ddb69d.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/f77be1f477e980732d57475d3ddb69d.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍go多版本的使用方式

<!--more-->

有些人可能注意到，每次 Go 发布新版本，官方都会提供类似这样的升级截图：

![image-20210528170702228](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210528170702228.png)

### 官方多版本的使用方式

根据上面的图，安装某个版本的 Go，跟一般 Go 包安装一样，执行 go get 命令：

```
$ go get golang.org/dl/go<version>  // 其中 <version> 替换为你希望安装的 Go 版本
```

这一步，只是安装了一个特定 Go 版本的包装器，真正安装特定的 Go 版本，还需要执行如下命令：

```
$ go<version> download   // 和上面一样，<version> 是具体的版本
```

因此，如果需要安装 Go1.16.4，执行如下两个命令即可。

```
$ go get golang.org/dl/go1.16.4
$ go1.16.4 download
```

几个注意的点：

- 有一个特殊的版本标记：gotip，用来安装最新的开发版本；
- 因为 golang.org 访问不了，你应该配置 GOPROXY（所以，启用 Module 是必须的）；
- 跟安装其他包一样，go get 之后，go1.16.4 这个命令会被安装到 `$GOBIN` 目录下，默认是`~/go/bin` 目录，所以该目录应该放入 PATH 环境变量；
- 没有执行 download 之前，运行 go1.16.4，会提示 `go1.16.4: not downloaded. Run 'go1.16.4 download' to install to ~/sdk/go1.16.4`；

可见，最后下载下来的 Go 放在了 ~/sdk/go1.16.4 目录下。



### 其他多版本管理方式

https://github.com/hit9/oo 像pyenv一样管理go版本



一直再用goenv 系列的还有nodenv，pyenv，rbenv，jenv

用scoop 或者 brewhome



WSL2文件系统没有打通，而且速度也不太好，我推荐WSL1。除非是有一些特别的东西一定要用2

gvm也很好



下载地址

https://github.com/voidint/g/releases
1
2、设置环境变量

G_MIRROR
值为
https://golang.google.cn/dl/

由于国内无法自由访问Golang官网，导致查询及下载go版本都变得困难，因此可以通过该环境变量指定一个镜像站点。


G_HOME
值为
E:\gvm

设置g的工作目录


GOROOT
值为
%G_HOME%\go

配置GOROOT，这个指向g工作目录下的go，g安装go版本后，会在这个路径下建立一个软链到versions目录下的指定版本，靠这个方法来控制当前的go版本，如果安装g之前已经安装过go了，需要修改GOROOT这个环境变量。


3、配置
为了方便使用，我们在E盘目录下建一个目录，然后通过环境变量指向到它，这样就可以使用命令行随时访问了。

将其添加到系统的path中

测试使用


4、使用

这里比较重要的一步，【以管理员身份运行命令行工具】，不管是CMD、还是powershell，都要以管理员身份运行！！！

不然，会出现【Symlink…A required privilege is not held by the client.】，就是无权创建软链，导致GOROOT指向的go目录不存在。

注：目前最新的1.2.0版本还无法实现自定义安装的目录，根据作者说的master分支已实现自定义目录的功能，试了确实可以。

其它命令

g ls 查询已安装的go版本
g ls-remote  查询可供安装的所有go版本
g ls-remote stable 查询当前可供安装的stable状态的go版本
g install 1.14.6 安装目标go版本1.14.6
g use 1.14.6 切换至1.14.6版本
g uninstall 1.14.6 卸载一个已安装的go版本