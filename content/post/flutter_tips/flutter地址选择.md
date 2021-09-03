---
title: "Flutter地址选择"
date: 2021-08-26T11:04:30+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---




areaId:"330282"

areaName:"慈溪市"

cityId:"330200"

cityName:"宁波市"

provinceId:"330000"

provinceName:"浙江省



封装工具类

```dart


import 'package:flutter/material.dart';
import 'package:city_pickers/city_pickers.dart';

class LocationSelector extends StatelessWidget {
  /// 触发对象
  final Widget target;

  /// 显示类型
  final ShowType showType;

  /// 确认
  final ValueChanged<Result> onConfirm;

  /// initResult type[Result]
  final Result? initResult;

  LocationSelector(
      {this.showType = ShowType.pca,
      this.initResult,
      required this.target,
      required this.onConfirm});

  show(BuildContext context) async {
    Result? result = await CityPickers.showCityPicker(
        context: context,
        locationCode: initResult != null
            ? initResult!.areaId ??
                initResult!.cityId ??
                initResult!.provinceId ??
                '110000'
            : '110000',
        showType: showType);
    if (result != null) {
      onConfirm(result);
    }
  }

  @override
  Widget build(BuildContext context) {
    return InkWell(
      onTap: () {
        show(context);
      },
      child: Container(
        color: Colors.black12,
        alignment: Alignment.center,
        margin: EdgeInsets.all(3.0),
        child: target,
      ),
    );
  }
}

```









```dart
//
// Created with Android Studio.
// User: 三帆
// Date: 12/02/2019
// Time: 16:53
// email: sanfan.hx@alibaba-inc.com
// tartget:  xxx
//

import 'package:flutter/material.dart';
import 'package:flutter/cupertino.dart';
import 'package:city_pickers/city_pickers.dart';

import '../src/location_selector.dart';




class ShowFullPageCityPicker extends StatefulWidget {
  @override
  _ShowFullPageCityPickerState createState() => _ShowFullPageCityPickerState();
}

class _ShowFullPageCityPickerState extends State<ShowFullPageCityPicker> {

  Result resultAttr = new Result();







  Widget _buildDefaultLocation() {
    return Row(
      children: <Widget>[


        LocationSelector(
          target: Text("${resultAttr.provinceName  }${resultAttr.cityName}${resultAttr.areaName}",
              maxLines: 1, overflow: TextOverflow.ellipsis),
          showType: ShowType.pca,
          initResult: resultAttr,
          onConfirm: (Result result) {
            if (result.provinceId != null) {
              this.setState(() {
                resultAttr = result;
              });
            }
          },
        ), Icon(
          Icons.keyboard_arrow_right,
          color: Color.fromARGB(255, 135, 137, 154),
        )


      ],
    );
  }



  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("省市县三级全屏联动44"),
      ),
      body: Column(
        children: <Widget>[

          _buildDefaultLocation()



        ],
      ),
    );
  }
}

```

