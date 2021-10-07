---
title: "flutter开发tips5"
date: 2021-08-18T14:05:48+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---



# Device Preview





```dart
import 'package:device_preview/device_preview.dart';
void main() => runApp(
  DevicePreview(
    enabled: !kReleaseMode,
    builder: (context) => MyApp(), // Wrap your app
  ),
);
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      locale: DevicePreview.locale(context), // Add the locale here
      builder: DevicePreview.appBuilder, // Add the builder here
      home: HomePage(),
    );
  }
}
```



# Fvm

# Sidekick



# intl

# Quicktype

# Google Maps for Flutter

# Connectivity

```dart
var result = await Connectivity.checkConnectivity();
switch (result) {
	case ConnectivityResult.wifi:
		/* ... */
	case ConnectivityResult.mobile:
		/* ... */
	case ConnectivityResult.none:
		/* ... */
}
```



# Permission Handler

# Share Plus

```dart
Share.share('check out my website: <https://tomicriedel.medium.com>', subject: 'visit it ;)')
```



