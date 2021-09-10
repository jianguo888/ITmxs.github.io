---
title: "Go安装与环境配置"
date: 2021-09-06T13:38:30+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["go"]
categories: ["go"]
---

### 一、Goland官网下载

官网下载地址：https://www.jetbrains.com/go/download/other.html

![image-20210906133904206](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210906133904206.png)

## **运行并激活Goland**

先下载相关的破解补丁：http://c.biancheng.net/uploads/course/go/Goland_Crack_Sinicization.zip 下载后解压得到的文件


![image-20210906134114732](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210906134114732.png)

将下载好的文件解压，并将破解补丁`jetbrains-agent.jar`复制到 Goland 安装目录下的 bin 目录中，放到这里没有什么特殊的意义，只是为了防止误删。

 Goland 安装目录的 bin 目录下找到`goland.exe.vmoptions`和`goland64.exe.vmoptions`两个文件。用记事本将它们打开，并分别在两个文件的最后面追加`-javaagent:Goland 的安装目录\bin\jetbrains-agent.jar`，注意将路径修改成你电脑上 Goland 的安装目录，例如：`-javaagent:D:\WindowsSoftware\GoLand 2021.2.1\bin\jetbrains-agent.jar`，修改完成后记得保存。

运行Goland进入激活界面，选择Active，选择激活码激活，将解压的激活码打开复制到文本框中，然后OK即可。

到这里 Goland 已经成功激活了。

## **Goland汉化**

默认全是英文的，这对于刚刚接触它的新手来说会造成一定的困扰，可以进行汉化，但是一般不建议汉化。

先关闭Goland，在解压的破解补丁中找到`resources_cn.jar`，并将它复制到 Goland 安装目录下的 lib 目录中。 然后，同样在 lib 目录中找到`resources_en.jar`文件，它是 Goland 内置的一个英文补丁，这里需要将它重命名为`resources_en.jar.backup`，因为想要恢复英文的话还需要用到它。

然后再运行 Goland ，你会发现 Goland 中的菜单已经变成我们熟悉的中文了。
