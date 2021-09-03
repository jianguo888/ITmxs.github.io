---
title: "《在Flutter中制作地图应用程序》"
subtitle: ""
date: 2021-07-17T22:54:16+08:00
lastmod: 2021-07-17T22:54:16+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍使用 Flutter 制作地图应用

<!--more-->

在本文中，我将向您展示如何使用 Flutter 向您的应用程序添加映射功能。对于本教程，您将不需要 google maps API，因此您无需支付任何费用，因为我们将使用另一个免费 API，所以不用多说，让我们深入研究它。

## 依赖关系 

创建一个新的 Flutter 项目，然后添加一些我们将要使用的依赖项。打开您的**pubspec.yaml**文件并在依赖项中添加这些行。

```yaml
 flutter_map: any
  geocoding: ^1.0.5
  geocoder: ^0.2.1
  tuple: ^1.0.2
  latlong: ^0.6.1
  positioned_tap_detector_2: ^1.0.0
  transparent_image: ^1.0.0
  async: ^2.1.0
  flutter_image: ^3.0.0
  vector_math: ^2.0.0
  proj4dart: ^1.0.4
  meta: ^1.1.0
  collection: ^1.14.0
```



现在让我们开始构建我们的应用程序

```dart
import 'package:flutter/material.dart';
import 'package:flutter_map/flutter_map.dart';
import 'package:geocoder/geocoder.dart';
import 'package:latlong/latlong.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: MapApp(),
    );
  }
}

class MapApp extends StatefulWidget {
  @override
  _MapAppState createState() => _MapAppState();
}

class _MapAppState extends State<MapApp> {
  double long = 49.5;
  double lat = -0.09;
  LatLng point = LatLng(49.5, -0.09);
  var location = [];

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        FlutterMap(
          options: MapOptions(
            onTap: (p) async {
              location = await Geocoder.local.findAddressesFromCoordinates(
                  new Coordinates(p.latitude, p.longitude));

              setState(() {
                point = p;
                print(p);
              });

              print(
                  "${location.first.countryName} - ${location.first.featureName}");
            },
            center: LatLng(49.5, -0.09),
            zoom: 5.0,
          ),
          layers: [
            TileLayerOptions(
                urlTemplate:
                    "https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png",
                subdomains: ['a', 'b', 'c']),
            MarkerLayerOptions(
              markers: [
                Marker(
                  width: 80.0,
                  height: 80.0,
                  point: point,
                  builder: (ctx) => Container(
                    child: Icon(
                      Icons.location_on,
                      color: Colors.red,
                    ),
                  ),
                )
              ],
            ),
          ],
        ),
        Padding(
          padding: EdgeInsets.symmetric(horizontal: 16.0, vertical: 34.0),
          child: Column(
            mainAxisAlignment: MainAxisAlignment.spaceBetween,
            children: [
              Card(
                child: TextField(
                  decoration: InputDecoration(
                    contentPadding: EdgeInsets.all(16.0),
                    hintText: "Search for your localisation",
                    prefixIcon: Icon(Icons.location_on_outlined),
                  ),
                ),
              ),
              Card(
                child: Padding(
                  padding: const EdgeInsets.all(8.0),
                  child: Column(
                    children: [
                      Text(
                          "${location.first.countryName},${location.first.locality}, ${location.first.featureName}"),
                    ],
                  ),
                ),
              ),
            ],
          ),
        ),
      ],
    );
  }
}
```

