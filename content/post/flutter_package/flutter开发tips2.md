---
title: "flutter开发tips2"
date: 2021-08-18T14:05:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]
---



# Smooth Page Indicator

嗯，**页面指示器**可以在任何地方使用。在应用程序、软件包、网站等中。要创建非常好的指标，您可以轻松使用包

```
AnimatedSmoothIndicator(
	activeIndex: yourActiveIndex,
	count:  6,
	effect:  WormEffect(),
)
```



# Percent Indicator

要快速**ð一世张开的百分比值**，例如下载过程是如何完成为止，这个[包](http://pub.dev/packages/percent_indicator)是非常好的，易于使用。

```
new CircularPercentIndicator(
  radius: 60.0,
  lineWidth: 5.0,
  percent: 1.0,
  center: new Text("100%"),
  progressColor: Colors.green,
)
```



# Liquid Progress Indicator

好吧，老实说，以前的包更适合标准应用程序。如果你想要一个看起来**很酷**的**进度指示器**，那么你绝对应该使用这个[包](http://pub.dev/packages/liquid_progress_indicator)



```
LiquidCircularProgressIndicator(
  value: 0.25, // Defaults to 0.5.
  valueColor: AlwaysStoppedAnimation(Colors.pink), // Defaults to the current Theme's accentColor.
  backgroundColor: Colors.white, // Defaults to the current Theme's backgroundColor.
  borderColor: Colors.red,
  borderWidth: 5.0,
  direction: Axis.horizontal, // The direction the liquid moves (Axis.vertical = bottom to top, Axis.horizontal = left to right). Defaults to Axis.vertical.
  center: Text("Loading..."),
);
```

# Syncfusion Flutter Sliders

这个[包](http://pub.dev/packages/syncfusion_flutter_sliders)真的提供了**各种滑块**。在下面显示的例子中，你可以看到一个非常“极端”的例子，说明你可以用这个包做什么。当然，您也可以使用它创建非常简单的滑块。

Syncfusion 不仅有这个包，还有很多其他的包可供选择。如果你想看到它们，你可以去[syncfusion.com](http://syncfusion.com/)。

```
SfSlider(
  min: 0.0,
  max: 100.0,
  value: _value,
  interval: 20,
  showTicks: true,
  showLabels: true,
  enableTooltip: true,
  minorTicksPerInterval: 1,
  onChanged: (dynamic value){
    setState(() {
      _value = value;
    });
   },
)
```





# Responsive Builder

应用程序**在任何情况下都应该是响应式的**。为了粗略地实现这一点，有[responsive_builder](http://pub.dev/packages/responsive_builder)包。正如您从代码中看到的，您只需要一个小部件即可开始使用。



```
// Use the widget
ResponsiveBuilder(
  builder: (context, sizingInformation) {
  // Check the sizing information here and return your UI
    if (sizingInformation.deviceScreenType == DeviceScreenType.desktop) {
      return Container(color:Colors.blue);
    }
    if (sizingInformation.deviceScreenType == DeviceScreenType.tablet) {
      return Container(color:Colors.red);
    }
    if (sizingInformation.deviceScreenType == DeviceScreenType.watch) {
      return Container(color:Colors.yellow);
    }
    return Container(color:Colors.purple);
  },
),
```





# TimelineTile

想要一个**不错的时间表**？好吧，那么只需使用[时间轴](http://pub.dev/packages/timeline_tile)包即可。我个人已经将这个包用于多个应用程序，并且可能也会在我当前的应用程序中使用它。

# Concentric Transition



```
ConcentricPageView(
  colors: <Color>[Colors.white, Colors.blue, Colors.red],
  itemCount: 1, // null = infinity
  physics: NeverScrollableScrollPhysics(),
  itemBuilder: (int index, double value) {
    return Center(
      child: Container(
        child: Text('Page $index'),
      ),
    );
  },
);
```



# Smooth star rating

您正在构建一个 Ecoomerce 应用程序或其他**可以让**您的**用户给出星级评分的**应用程序？好吧，然后使用[smooth_star_rating](http://pub.dev/packages/smooth_star_rating)包快速创建这样的小部件。它非常易于使用，绝对值得推荐。

```
SmoothStarRating(
  allowHalfRating: false,
  onRated: (v) {
    //Your action
  },
  starCount: 5,
  rating: rating,
  size: 40.0,
  isReadOnly:true,
  fullRatedIconData: Icons.blur_off,
  halfRatedIconData: Icons.blur_on,
  color: Colors.green,
  borderColor: Colors.green,
  spacing:0.0
)
```

# Flutter Chat UI

与此同时，越来越多的应用程序**内置了聊天功能**。无论是纯粹的通信应用程序，还是通过聊天提供支持的其他应用程序，应用程序的可能性都非常大。然而，为它设计和开发一个像样的 UI 通常是非常乏味的。为了避免这个过程，有[flutter_chat_ui](http://pub.dev/packages/flutter_chat_ui)包。这为您提供了一个不错的预设，既现代又易于使用。

# Flutter Staggered Gridview