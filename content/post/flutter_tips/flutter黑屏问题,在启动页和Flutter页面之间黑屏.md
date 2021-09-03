---
title: "Flutter黑屏问题,在启动页和Flutter页面之间黑屏"
date: 2021-08-24T09:44:37+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---

# 黑屏

解决方法：android/app/src/main/AndroidManifest.xml

在 AndroidManifest.xml 的 <activity> </activity> 中添加

```java
  <!-- Flutter  去除启动页的白屏  -->
            <meta-data
                android:name="io.flutter.app.android.SplashScreenUntilFirstFrame"
                android:value="true" />
            <!-- 去除启动页的黑屏 -->
            <meta-data
                android:name="io.flutter.embedding.android.SplashScreenDrawable"
                android:resource="@drawable/launch_background" />
            <!-- Flutter 黑屏问题，在启动页和Flutter页面之间黑屏。 -->
```



完整

```java
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.flutter_package_demo">
    <!-- io.flutter.app.FlutterApplication is an android.app.Application that
         calls FlutterMain.startInitialization(this); in its onCreate method.
         In most cases you can leave this as-is, but you if you want to provide
         additional functionality it is fine to subclass or reimplement
         FlutterApplication and put your custom class here. -->
    <application
        android:name="io.flutter.app.FlutterApplication"
        android:label="flutter_package_demo"
        android:icon="@mipmap/ic_launcher">
        <activity
            android:name=".MainActivity"
            android:launchMode="singleTop"
            android:theme="@style/LaunchTheme"
            android:configChanges="orientation|keyboardHidden|keyboard|screenSize|smallestScreenSize|locale|layoutDirection|fontScale|screenLayout|density|uiMode"
            android:hardwareAccelerated="true"
            android:windowSoftInputMode="adjustResize">
            <!-- Specifies an Android theme to apply to this Activity as soon as
                 the Android process has started. This theme is visible to the user
                 while the Flutter UI initializes. After that, this theme continues
                 to determine the Window background behind the Flutter UI. -->
            <meta-data
                android:name="io.flutter.embedding.android.NormalTheme"
                android:resource="@style/NormalTheme" />
            <!-- Displays an Android View that continues showing the launch screen
                 Drawable until Flutter paints its first frame, then this splash
                 screen fades out. A splash screen is useful to avoid any visual
                 gap between the end of Android's launch screen and the painting of
                 Flutter's first frame. -->
            <meta-data
                android:name="io.flutter.embedding.android.SplashScreenDrawable"
                android:resource="@drawable/launch_background" />
            <!-- Flutter  去除启动页的白屏  -->
            <meta-data
                android:name="io.flutter.app.android.SplashScreenUntilFirstFrame"
                android:value="true" />
            <!-- 去除启动页的黑屏 -->
            <meta-data
                android:name="io.flutter.embedding.android.SplashScreenDrawable"
                android:resource="@drawable/launch_background" />
            <!-- Flutter 黑屏问题，在启动页和Flutter页面之间黑屏。 -->
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        <!-- Don't delete the meta-data below.
             This is used by the Flutter tool to generate GeneratedPluginRegistrant.java -->
        <meta-data
            android:name="flutterEmbedding"
            android:value="2" />
    </application>
</manifest>

```



# 设置启动页动画

```java
<?xml version="1.0" encoding="utf-8"?><!-- Modify this file to customize your launch splash screen -->
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@android:color/white" />

    <!-- You can insert your own image assets here -->
    <!-- 设置启动页背景 -->
    <item>
        <bitmap
            android:gravity="center"
            android:src="@mipmap/ic_launcher" />
    </item>
</layer-list>

```

# 修改名称和图标

## Android修改名称和图标

android/app/src/main/AndroidManifest.xml


打开如下图的文件：

![image-20210824101611734](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210824101611734.png)

上图右侧红框内有label和icon属性就是代表名称和图标，名称很简单，想叫什么就改成什么就可以了，图标是mipmap下的文件，mipmap目录如下图：

![image-20210824101639747](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210824101639747.png)

注意有好几个mipmap文件夹，这和ios是一样的，这几个是适配不同分辨率的手机。只需让美工出相应尺寸的图标放到相应的文件夹就可以了。
注意图片需要是png格式。

## IOS修改名称和图标

名称修改如下图：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/20190710094039756.png)

CFBundleName 下面的字符串就是app的名称。
图标修改如下图：

![在这里插入图片描述](https://luckly007.oss-cn-beijing.aliyuncs.com/image/20190710094158439.png)

图片的名称可以自己命名，不过要在Contents.json文件中相对应，这些图片也是为了适配不同的ios手机和ipad。
