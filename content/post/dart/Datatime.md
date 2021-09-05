---
title: "datatime"
date: 2021-08-30T16:46:23+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

| 方法名称                                                     | 说明                                     |
| ------------------------------------------------------------ | ---------------------------------------- |
| now()                                                        | 命名构造，获取当前时间                   |
| millisecondsSinceEpoch                                       | DateTime转时间戳                         |
| fromMillisecondsSinceEpoch                                   | 时间戳转DateTime                         |
| parse(string)                                                | 字符串转DateTime                         |
| isBefore(date)                                               | 时间比较---在之前                        |
| isAfter(date)                                                | 时间比较---在之后                        |
| isAtSameMomentAs(date)                                       | 时间比较---相等                          |
| compareTo(date)                                              | 大于返回1；等于返回0；小于返回-1         |
| add(Duration)                                                | 时间增加                                 |
| subtract(Duration)                                           | 时间减少                                 |
| difference(date)                                             | 时间差 两个时间相差 小时数               |
| timeZoneName                                                 | 本地时区简码                             |
| timeZoneOffset                                               | 返回UTC与本地时差 小时数                 |
| year、month、day、hour、minute、second、millisecond、microsecond | 返回  年、月、日、时、分、秒、毫秒、微妙 |
| weekday                                                      | 返回星期几                               |

# 代码示例

```dart
var today = DateTime.now();
            print('当前时间是：$today');
            var date1 = today.millisecondsSinceEpoch;
            print('当前时间戳：$date1');
            var date2 = DateTime.fromMillisecondsSinceEpoch(date1);
            print('时间戳转日期：$date2');
            //拼接成date
            var dentistAppointment = new DateTime(2019, 6, 20, 17, 30,20);
            print(dentistAppointment);
            // 字符串转date
            DateTime date3 = DateTime.parse("2019-06-20 15:32:41");
            print(date3);
            // 时间比较
            print(today.isBefore(date3));// 在之前
            print(today.isAfter(date3)); // 在之后
            print(date3.isAtSameMomentAs(date3));// 相同
 
            print(date3.compareTo(today));// 大于返回1；等于返回0；小于返回-1。
            // print(DateTime.now().toString());
            // print(DateTime.now().toIso8601String());
 
            //时间增加
            var fiftyDaysFromNow = today.add(new Duration(days: 5));
            print('today加5天：$fiftyDaysFromNow');
            //时间减少
            DateTime fiftyDaysAgo = today.subtract(new Duration(days: 5));
            print('today减5天：$fiftyDaysAgo');
            //时间差   两个时间相差 小时数
            print('比较两个时间 差 小时数：${fiftyDaysFromNow.difference(fiftyDaysAgo)}');
 
            print('本地时区简码：${today.timeZoneName}');
 
            print('返回UTC与本地时差 小时数：${today.timeZoneOffset}');
 
            print('获取年月日：${today.year}');//month、day、hour、minute、second、millisecond、microsecond
 
            print('星期：${today.weekday}');// 返回星期几
```

#  效果图

![img](https://mxszs.oss-cn-beijing.aliyuncs.com/img/20190620173826299.png)