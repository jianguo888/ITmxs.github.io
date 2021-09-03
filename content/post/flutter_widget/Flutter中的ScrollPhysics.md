---
title: "Flutter中的ScrollPhysics"
date: 2021-08-15T20:00:27+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

使用 ListView 或 PageView 变体？如何改变滚动条的性质，让您的用户在使用时感觉更真实？让我们看看如何使用不同的 ScrollPhysics 做到这一点。

![img](https://miro.medium.com/max/1400/0*QSrBuzomFlQ5tfCA.png)

图片版权归各自所有者所有。

因此，如果您使用任何具有滚动功能的小部件，例如 ListView、ListView.builder、ListView.separated、PageView、PageView.builder、SingleChildScrollView 等，您将在`physics`其中获得一个名为的参数，该参数占用`ScrollPhysics`. 此参数定义用户将如何看到您的小部件滚动。让我们一一看看每种类型的 ScrollPhysics！





## 1. NeverScrollableScrollPhysics:



![img](https://miro.medium.com/max/480/0*tcabstG56IRaRD2Q.gif)

## 2. BouncingScrollPhysics:





![img](https://miro.medium.com/max/240/0*_eLPyGduUot6k5lH.gif)

## 3. ClampingScrollPhysics:



![img](https://miro.medium.com/max/240/0*6EBwenmeassRJWDv.gif)

## 4. FixedExtentScrollPhysics:





![img](https://miro.medium.com/max/240/0*E73g_3wK6jP6LPym.gif)

**FixedExtentScrollPhysics** with ListWhellScrollView:



![img](https://miro.medium.com/max/240/0*JqmARoLBIEn5iRta.gif)

## 5. PageScrollPhysics:



![img](https://miro.medium.com/freeze/max/28/0*mi_pd3txJn1_o0Xo.gif?q=20)

![img](https://miro.medium.com/max/240/0*mi_pd3txJn1_o0Xo.gif)

希望对你有用！