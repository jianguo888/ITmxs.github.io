---
title: "Flutter倒计时工具类"
date: 2021-08-25T13:24:19+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---





```dart

//获取活动内容
  Widget _getContent() {
    if (_info == null) {
      return Container();
    }
    DateTime now = DateTime.now();
    DateTime endTime =
        DateTime.fromMillisecondsSinceEpoch(_info.endTime * 1000);
    DateTime startTime =
        DateTime.fromMillisecondsSinceEpoch(_info.beginTime * 1000);

    DateTime residue = null;
    if (now.millisecondsSinceEpoch ~/ 1000 < _info.endTime) {
      residue = DateTime.fromMillisecondsSinceEpoch(
          _info.endTime * 1000 - now.millisecondsSinceEpoch);
    }

      
      
      
      Padding(
            padding: EdgeInsets.only(left: 32.w, right: 32.w),
            child: residue == null
                ? Text("活动已结束")
                : DescUtils.getBitmapTimeBunch(
                    day: residue.day,
                    hours: residue.hour,
                    min: residue.minute,
                    sec: residue.second,
                    endWord: "后结束",
                    colorType: 2),
          ),
      
      
      
      
      
      
      
      
      
      
      
      
      
      
        @override
  void initState() {
    super.initState();
    _timer = Timer.periodic(Duration(seconds: 1), (timer) {
      setState(() {});
    });
    _getDetailInfo();
    _getUsers();
    _getLocal();
  }
      
      
      
      
      
      
      
      
      
      @override
  void dispose() {
    super.dispose();
    _timer.cancel();
  }
```





时间戳是什么？
当我第一次遇到的时候，我说这后端给我传的什么东西，还专门去问了一下，现在想想老脸通红，然后去百度查了查，通俗点说就是将时间格式转换为通信及保存都很方便的格式，比如2020-08-15 15:07:17转化为时间戳(毫秒)1597475237894<-就是转化后的

在flutter 中如何转化两者之间
1.将时间戳转化为 常见的日期格式：DateTime。fromMillisecondsSinceEpoch

```dart
 setState(() {
              String stime = data["data"][i]["start_time"];
              String etime = data["data"][i]["end_time"];//上面两个为我请求接口拿到的时间戳
              var strtime =
                  DateTime.fromMillisecondsSinceEpoch(int.parse(stime));//将拿到的时间戳转化为日期
              var endtime =
                  DateTime.fromMillisecondsSinceEpoch(int.parse(etime));
              var sdatetime = strtime.toLocal().toString().substring(0, 16);
              var edatetime = endtime.toLocal().toString().substring(0, 16);
              //substring为Dart截断字符串的方法,因为不需要显示到毫秒所以将后面的截取掉
              datatime.add({"stratime": sdatetime, "endtime": edatetime});
              //向时间数组中添加已经转化好的时间戳方便使用
            });

```

2.在发送请求时将获取到的时间转为时间戳，发给后端：millisecondsSinceEpoch

```dart

String _time = "2021-8-25  16:00:00.00000";//注意格式否则在DateTime.parse()时会报错
    var _strtimes = DateTime.parse(_time );//首先先将字符串格式的时间转为DateTime类型，因为millisecondsSinceEpoch方法转换要求为该格式
    var _intendtime = _strtimes .millisecondsSinceEpoch;//方法一
    var _intendtime =  DateTime.fromMillisecondsSinceEpoch(_strtimes );//方法二
    //_intendtime 为以及转换好的时间戳

```

2.在发送请求时将获取到的时间转为时间戳，发给后端：millisecondsSinceEpoch





```
   var _diffDate = DateTime.fromMillisecondsSinceEpoch(1630888000000);
          var now = DateTime.now();
// var days = endDate.difference(startDate).inDays;
// var hours = endDate.difference(startDate).inHours;
// var minutes = endDate.difference(startDate).inMinutes;
```

