---
title: "AndroidStudio出现Unabletostartthedaemonprocess问题"
date: 2021-08-29T21:21:03+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->

# Android Studio出现Unable to start the daemon process问题

一、问题
在Android Studio中新建的工程出现了以下问题

Error:Unable to start the daemon process.http://write.blog.csdn.net/postedit?ref=toolbar
This problem might be caused by incorrect configuration of the daemon.
For example, an unrecognized jvm option is used.
Please refer to the user guide chapter on the daemon at https://docs.gradle.org/2.14.1/userguide/gradle_daemon.html
Please read the following process output to find out more:
-----------------------
Error occurred during initialization of VM
Could not reserve enough space for 1572864KB object heap

二、分析
根据问题我们分析可以得出：Android Studio中gradle构建堆栈空间不足1572864KB。


三、解决
打开工程目录下的gradle.properties文件

![image-20210829212429432](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210829212429432.png)

(方法一)

将org.gradle.jvmargs=-Xmx1536m中的值设置小一些，一般为1024或512

(方法二)

将org.gradle.jvmargs=-Xmx1536m前面加上“#”注释掉即可(Gradle的jvm的内存分配设置，注释掉就是使用默认或者系统配置。)




解决方案有效：

1.在android studio中打开项目**gradle.properties**文件

2.在文件末尾添加这一行**org.gradle.jvmargs=-Xmx1024m** & 保存文件

3.关闭并重新打开项目

1)OPEN项目

2)点击gradle脚本

3)打开gradle.properties

4) 将 org.gradle.jvmargs=-Xmx1536m 改为 org.gradle.jvmargs=-Xmx1024m

5)保存工作

6)打开文件->无效缓存/重启

它完成了！！！