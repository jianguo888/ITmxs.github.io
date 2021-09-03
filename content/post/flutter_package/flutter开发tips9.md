---
title: "Flutter开发tips9"
date: 2021-08-30T22:21:26+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->

# 升级者

[upgrader](https://pub.dev/packages/upgrader)是一个超级包，用于提醒用户应用程序的**更新****可用**。

创建起来有点麻烦，但绝对是即使是对 Flutter 有一定了解的初学者也能做到的。该包有几个优点。一旦它可以在 MaterialDesign 以及 Cupertionostyle 中显示警报，还可以设置不同的语言。

![img](https://miro.medium.com/max/60/0*JFKJgrYx_vu3MeJd.png?q=20)

![img](https://miro.medium.com/max/400/0*JFKJgrYx_vu3MeJd.png)

# 在应用程序审查

虽然我们讨论的是小弹出窗口，但我们不妨继续讨论。**应用评论**对于应用非常重要，因为它们反映了用户对它的看法以及作为应用开发者您可以改进的地方。

这就是为什么有一个很棒的插件叫做[in_app_review](https://pub.dev/packages/in_app_review)。有了这个包，用户可以在应用的某个点给一个评分，这个评分也会在不离开应用的情况下发布到 App & Google Play 商店。

![img](https://miro.medium.com/max/60/0*w63xdE1eoV_vsgIe.jpg?q=20)

![img](https://miro.medium.com/max/700/0*w63xdE1eoV_vsgIe.jpg)

# Flutter 资金选择

好的，这是今天的最后一个弹窗，我保证:)

在欧洲，必须在应用程序中询问是否**允许收集数据**以改善服务等。为此有包[flutter_funding_choices](http://pub.dev/packages/flutter_funding_choices)。这是资金选择的非官方 Flutter 实现，这是一项谷歌服务，允许请求用户同意 AdMob 中的个性化广告。

![img](https://miro.medium.com/max/34/0*R6lxWnm3wFYPiGQd.png?q=20)

![img](https://miro.medium.com/max/700/0*R6lxWnm3wFYPiGQd.png)

# 应用内通知

WhatsApp、Signal 等通知。非常方便，我已经在另外 10 篇 Flutter 技巧文章中展示了它适用于哪个包。如果您想**在应用程序内**有独立**于**操作系统的小**通知**，那么您可以使用[in_app_notification](https://pub.dev/packages/in_app_notification)包。它非常易于使用，您可以显示这些小卡片，例如，如果登录过程中出现问题。说到登录，对用户使用强密码不是有一个很好的解决方案吗？

![img](https://miro.medium.com/freeze/max/28/0*2Zw-Rwi72Svnwpbn.gif?q=20)

![img](https://miro.medium.com/max/320/0*2Zw-Rwi72Svnwpbn.gif)

```dart
InAppNotification.show(
  child: NotificationBody(count: _count),
  context: context,
  onTap: () => print('Notification tapped!'),
);
```

# Flutter 密码验证器

回答上一个提示中的问题：是的，它存在。[flutter_pw_validator](https://pub.dev/packages/flutter_pw_validator)是一个非常不错的包，可以确保用户满足**一个好的密码**的**要求**。这也很容易使用，绝对应该看看。

![img](https://miro.medium.com/freeze/max/28/0*T0E0lTmjdN_PL0li.gif?q=20)

![img](https://miro.medium.com/max/320/0*T0E0lTmjdN_PL0li.gif)

```dart
dTextField(
    controller: _passwordController,
),
FlutterPwValidator(
    controller: _passwordController,
    minLength: 6,
    uppercaseCharCount: 2,
    numericCharCount: 3,
    specialCharCount: 1,
    width: 400,
    height: 150,
    onSuccess: yourCallbackFunction,
)
```

# 网页视图颤动

**在**不使用浏览器或其他东西的情况下**在应用程序中查看网站**？好吧，你可以很容易地做到这一点，使用[webview_flutter](https://pub.dev/packages/webview_flutter)。就像这里介绍的每个包一样，超级易于使用，只需要一行代码：

```
return WebView(initialUrl: 'https://tomicriedel.medium.com');
```

是的，就是这样，现在您的 webview 已经可以工作了。当然，如果您打算创建一个浏览器，您可以将整个内容链接到一个变量：

```
String tomicRiedel = '<https://tomicriedel.medium.com>';

return WebView(initialUrl: tomicRiedel);
```

# 旗帜

**标志**可用于许多不同的领域。例如，如果您希望用户选择他的来源，或者您希望使用标志使国家/地区代码更具描述性。包[标志](https://pub.dev/packages/flag)为您提供了世界上所有的标志，以便在您的应用程序中使用它们：

```
Flag.fromCode(FlagsCode.COUNTRY_CODE, height: HEIGHT, width: WIDTH),
//OR
Flag.fromString(COUNTRY_CODE, height: HEIGHT, width: WIDTH),
```

# 局域网扫描仪

每个人都知道这个弹出窗口询问应用程序是否允许该应用程序**发现本地网络中的网络设备**。您的应用程序中也需要这个吗？那么[lan_scanner](https://pub.dev/packages/lan_scanner)包正是您所需要的。这一次它可能不是用一行代码编写的，但我会告诉你如何：

```
inal port = 80;
final subnet = "192.168.0";
final timeout = Duration(seconds: 5);
final scanner = LanScanner();
final stream = scanner.preciseScan(
  subnet,
  timeout: timeout,
  progressCallback: (ProgressModel progress) {
    print('${progress.percent * 100}% 192.168.0.${progress.currIP}');
  },
);
stream.listen((DeviceModel device) {
  if (device.exists) {
    print("Found device on ${device.ip}:${device.port}");
  }
});
```

但您可能不知道您的子网是什么。那么你仍然应该使用包[network_info_plus](https://pub.dev/packages/network_info_plus)然后调用 ipToSubnet() 函数：

```
var wifiIP = await (NetworkInfo().getWifiIP())
var subnet = ipToSubnet(wifiIP);
```

# 视频修剪器

每个人都知道。当您想在应用程序中**修剪视频**时。如果您希望它在您的应用程序中工作，您应该使用包[video_trimmer](https://pub.dev/packages/video_trimmer)。它可以修剪视频，然后观看并将其保存在文件中。这个包的另一个非常酷的功能是它甚至支持 .gif 文件。

![img](https://miro.medium.com/max/700/0*-DxPwvy8t7JTeuSJ.png)

由于加载视频等需要一定的权限，所以这里就不解释怎么设置了，但是包的README很详细，很容易理解。

# 火焰

正如一开始所承诺的那样，我将向您展示Flutter的**游戏引擎**。而这个叫做[火焰](https://pub.dev/packages/flame)。该引擎仍处于测试阶段，但正在不断开发中，并且已经有很多人在制作。[Robert Brunhage](https://www.youtube.com/c/RobertBrunhage)在一个[视频中](https://www.youtube.com/watch?v=AfDYTOK_tfM&t=6s)制作了一个非常简单的游戏，但这实际上只是一个简单的例子，我相信你可以用它制作真正的热门游戏 ;)