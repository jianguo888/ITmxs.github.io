---
title: "Flutter深入浅出组件篇  继承关系图"
date: 2021-09-02T21:05:27+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

## 1、Flutter Object继承关系

该继承图为flutter2.2版本最新继承关系图。

 [![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/Main.png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/Main.png)

## 2、Flutter Widget组件继承图

[Flutter关系继承图.xmind](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/Flutter继承关系图.xmind)

##### `Widget` 下面有五个子类， `PreferredSizeWidget` 下面总共有6个组件， `ProxyWidget` 下面总共有47个组件， `RenderObjectWidget` 下面总共有94个组件， `StateFulWidget` 下面总共有167个组件， `StatelessWidget` 下面总共有108个组件，加上自身5个组件，所以总共的组件有高达427之多，这可能也是很多人觉得Flutter很难学的原因之一，其实我们只要掌握一些基本常用的可以了。

- **PreferredSizeWidget**：主要用于 `AppBar` 和 `TabBar` , 通过继承该类可实现自定义大小。
- **ProxyWidget**: 是一个抽象类，主要用于提供给子 `Widget` 的抽象 `Widget`
- **RenderObjectWidget**：是一个抽象类， `RenderObjectWidgets` 为 `RenderObjectElements` 提供配置，它包装 `RenderObjects` ，提供应用程序的实际渲染。
- **StateFulWidget**：具有可变状态的 `Widget`
- **StatelessWidget**：不需要可变状态的 `Widget`

[![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image.png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image.png)

## 3、**PreferredSizeWidget**

[![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (1).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (1).png)

## 4、**ProxyWidget**

[![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (2).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (2).png) [![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (3).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (3).png)

## 5、**RenderObjectWidget**

[![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (4).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (4).png)

## 6、**StateFulWidget**

[![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (5).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (5).png) [![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (6).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (6).png) [![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (7).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (7).png) [![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (8).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (8).png) [![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (9).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (9).png) [![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (10).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (10).png)

## 7、StatelessWidget

[![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (11).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (11).png) [![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (12).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (12).png) [![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (13).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (13).png) [![img](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (14).png)](https://gz-ljm-blog.oss-cn-guangzhou.aliyuncs.com/blog/image (14).png)