---
title: "Flutter倒计时工具类"
date: 2021-08-25T13:24:19+08:00
draft: true
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

