---



title: "我的第一个android应用"
date: 2021-08-15T21:18:22+08:00
draft: false
tags: ["android"]
categories: ["android"]
 
---





第一步

今天学习android开发‘



![image-20210815211908084](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815211908084.png)



![image-20210815212044968](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815212044968.png)





点击finish

等待gradle构件

![image-20210815212755431](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815212755431.png)

新建详情

**应用名称**：在桌面显示的app的名称，在后续的变成中可以修改。
 **工程名称**：显示在Eclipse中，用来标示工程的名称。
 **命名空间**：应用程序文件的命名空间。在手机系统内，应用的命名空间应该唯一。
 **Minimum Required SDK**：最小依赖的Android SDK版本。为了尽可能的让低版本的手机运行自己的程序，应尽可能的让自己的应用程序的核心code使用通用android api。
 **Target SDK**：最终应用程序测试版本。随着Android新版本的推送，我们应尽可能的让我们的应用程序在最新的版本上进行测试，更新Target SDK，并且易于使用最新的系统特性。

**三、简述Android应用程序中，各个文件以及文件夹的作用**
 1、**AndroidManifest.xml**：描述应用的基本特性（权限声明、应用配置版本、应用程序组件等）。
 2、**src**：应用程序主要的源代码目录。
 3、**res**：应用程序资源目录。
 drawable-hdpi/：图标资源目录，适应于high-density (hdpi) 屏幕。
 layout：应用程序布局页面，用户能看到的页面的布局配置xml。
 values：文案、配置等资源目录。例如：文案信息、颜色值、大小（dimens）。

![image-20210815215419111](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815215419111.png)





![image-20210815215503827](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815215503827.png)

![image-20210815215926390](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815215926390.png)





报错 JKS 密钥库使用专用格式。建议使用 "keytool -importkeystore -srckeystore E:\workspace\android\HelloWord\key.jks -destkeystore E:\workspace\android\HelloWord\key.jks -deststoretype pkcs12" 迁移到行业标准格式 PKCS12。





```
keytool -genkey -alias testalias -keypass 123456 -keyalg RSA -keysize 2048 -validity 36500 -keystore E:\workspace\android\HelloWord\app\test.keystore -storepass 123456

```



# keytool错误：java.lang.RuntimeException用法错误，and不是合法的命令



获取MD5指纹的时候，发现了这个问题：

keytool错误： java.lang.RuntimeException: 用法错误，and 不是合法的命令

错误原因：keytool不认识带有空格的路径

解决办法：将C:\documents and settings\administrator\.android\debug.keystore移动到其他不带空格的路径下，比如：D:\debug.keystore

# 【100%有效】解决AndroidStudio 控制台编译输出中文乱码，亲测解决！

在AndroidStudio中新建了一个Java Module，但是点击 Run ‘app’之后，Build Output 控制台输出的中文都是乱码，都是问号一样的字符

google了很多方法，要么就是文不对题，要么就是各种抄，没有真正测试过！

错误方案一
File Encodings 改为UTF-8？
没用！


错误方案二
build.gradle 添加如下代码？

 tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}

没用！ 这是解决System.out.print输出的中文乱码问题的！

正确解决办法
双击Shift，输入vmoption,，选择Edit Custom CM Options
如果之前没有配置过，会弹出窗口问是否创建配置文件，点击Create

输入
-Dfile.encoding=UTF-8
1

好用。在Help里倒数第4行，可以找到vmoption的菜单项

快速链接



![image-20210815222741795](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815222741795.png)





添加字符串的两种方式



![image-20210815223547768](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815223547768.png)



![image-20210815223456047](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815223456047.png)





# Entry name ‘res/color/material_on_surface_disabled.xml‘ collided





Android 闭坑日志

我刚刚创建了一个签名的apk，然后在我的项目中添加了两个文件my_web_activity.xml和MyWebActivity.kt。编译代码后，出现错误-“条目名称’res / color / material_on_surface_disabled.xml’发生冲突”没有其他规定。

解决方案：


点击Build ==> Clean Project ，然后再进行编译就可以了。
