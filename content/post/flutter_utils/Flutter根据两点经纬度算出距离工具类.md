---
title: "Flutter根据两点经纬度算出距离工具类"
date: 2021-08-18T11:22:20+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---





```dart
import 'dart:math';

/// 根据两点经纬度 使用math 算出之间距离
/// 导入import 'dart:math';
int getDistance(double lat1, double lng1, double lat2, double lng2) {
  /// 单位：米
  /// def ：地球半径
  double def = 6378137.0;
  double radLat1 = _rad(lat1);
  double radLat2 = _rad(lat2);
  double a = radLat1 - radLat2;
  double b = _rad(lng1) - _rad(lng2);
  double s = 2 *
      asin(sqrt(pow(sin(a / 2), 2) +
          cos(radLat1) * cos(radLat2) * pow(sin(b / 2), 2)));
  return (s * def).roundToDouble().toInt();
}

double _rad(double d) {
  return d * pi / 180.0;
}

```



```
var dis = await Amap.getDistanceAndDuration(
          from: MapInfo(lat: Config.lat, lng: Config.lng), to: _local);
```

