---
title: "flutter开发tips1"
date: 2021-08-18T14:05:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---

https://github.com/londonappbrewery/Flutter-Course-Resources

# Syncfusion DataGrid (DataTable)

这个[包](https://pub.dev/packages/syncfusion_flutter_datagrid#datagrid-features)用于以表格形式**显示和编辑数据**表。它的构建非常高效(就像 Syncfusion 的每个包一样)并提供了许多自定义选项。

# Syncfusion Localizations

[syncfusion_loc一个lizations](https://pub.dev/packages/syncfusion_localizations)提供**本地化文本**超过**77培养物**可在任何Syncfusion插件使用。可以在包页面上找到所有支持语言的完整列表

# Syncfusion OfficeChart

该[包](https://pub.dev/packages/syncfusion_officechart)用于根据数据(逻辑上)创建**Excel 图表**。从下面的示例中可以看出，您确实可以创建任何类型的图表。强烈推荐用于使用大量统计数据的应用程序。

# Syncfusion Charts

嗯，最后一个包相对单调，只是真正适用于 Excel 等。但是如果您想基于数据创建**特殊的、动画的**和非常**漂亮的图表**怎么办？那你不用Syncfusion OfficeChart包，

# Syncfusion Datepicker

好的，我们可能都知道 Google 的 Material Datapicker，但是如果您需要新的设计和功能，**Date range picker**呢？好吧，那么您只需使用包[syncfusion_flutter_datepicker](https://pub.dev/packages/syncfusion_flutter_datepicker)。

# Syncfusion Calendar

我认为每个人都应该知道像 Google 日历这样的应用程序。您可以在**特定时间**、**不同日期**等放置**事件**。现在您可能无法使用此包开发应用程序来代替 Google 日历，但在各种应用程序中，您通常需要一个日历，必须在其中输入事件。好吧，谁能想到，

# Syncfusion barcodes



也许您很少会遇到这个问题，但是如果您正在开发一个更大的应用程序，您有时可能需要**生成二维**码或条形码，以便用户更轻松地访问某个网站。

该插件可以创建一维条码，也可以创建二维条码。其中，有许多不同的类型，您可以在包页面上找到所有这些类型。

为了轻松解决这个功能，你可以使用[syncfusion_flutter_barcodes](https://pub.dev/packages/syncfusion_flutter_barcodes)插件！

它非常易于使用，您可以从以下内容中看出：

```
SfBarcodeGenerator(
  value: 'https://tomicriedel.medium.com'), //Visit it ;)
)
```



# Syncfusion Maps

# Syncfusion SignaturePad

好吧，又是一个罕见的用例，但你永远不应该说永远。如果您需要在您的应用程序或网站中希望用户签名的区域，您可以使用此[包](https://pub.dev/packages/syncfusion_flutter_signaturepad)。顾名思义，它为您提供**签名板**。

```
SfSignaturePad(
	minimumStrokeWidth: 1,
  maximumStrokeWidth: 3,
  strokeColor: Colors.blue,
  backgroundColor: Colors.grey,
),
```



# Syncfusion PDF Viewer

实际上非常不言自明，您可以使用此[包](https://pub.dev/packages/syncfusion_flutter_pdfviewer)**查看 PDF**。但好消息是，您还内置了许多功能，例如您可以跳转到一个页面，并且当前页面也会显示给您。

就像几乎所有 Syncfusion 软件包一样，这个软件包非常易于使用，只需要几行代码：

```dart
SfPdfViewer.network(
	'https://tomicriedel.medium.com/never_gonna_give_you_up.pdf', //This website does not exist, but do visit tomicriedel.medium.com
)
```


