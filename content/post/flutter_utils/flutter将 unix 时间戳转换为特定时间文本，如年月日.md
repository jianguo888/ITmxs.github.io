---
title: "flutter将 unix 时间戳转换为特定时间文本，如年月日"
date: 2021-08-18T19:49:40+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---









```dart
class TimeUtil {
  //将 unix 时间戳转换为特定时间文本，如年月日
  static String convertTime(int timestamp) {
    DateTime msgTime = DateTime.fromMillisecondsSinceEpoch(timestamp);
    DateTime nowTime = DateTime.now();

    if (nowTime.year == msgTime.year) {
      //同一年
      if (nowTime.month == msgTime.month) {
        //同一月
        if (nowTime.day == msgTime.day) {
          //同一天 时:分
          return msgTime.hour.toString() + ":" + msgTime.minute.toString();
        } else {
          if (nowTime.day - msgTime.day == 1) {
            //昨天
            return "昨天";
          } else if (nowTime.day - msgTime.day < 7) {
            return _getWeekday(msgTime.weekday);
          }
        }
      }
    }
    return msgTime.year.toString() +
        "/" +
        msgTime.month.toString() +
        "/" +
        msgTime.day.toString();
  }

  ///是否需要显示时间，相差 5 分钟
  static bool needShowTime(int sentTime1, int sentTime2) {
    return (sentTime1 - sentTime2).abs() > 5 * 60 * 1000;
  }

  static String _getWeekday(int weekday) {
    switch (weekday) {
      case 1:
        return "星期一";
      case 2:
        return "星期二";
      case 3:
        return "星期三";
      case 4:
        return "星期四";
      case 5:
        return "星期五";
      case 6:
        return "星期六";
      default:
        return "星期日";
    }
  }
}

//转化年龄工具类
class AgeByBirthUtils {
  static String getAge(DateTime brt) {
    int age = 0;
    DateTime dateTime = DateTime.now();
    if (dateTime.isBefore(brt)) {
      //出生日期晚于当前时间，无法计算
      return '出生日期不正确';
    }
    int yearNow = dateTime.year; //当前年份
    int monthNow = dateTime.month; //当前月份
    int dayOfMonthNow = dateTime.day; //当前日期

    int yearBirth = brt.year;
    int monthBirth = brt.month;
    int dayOfMonthBirth = brt.day;
    age = yearNow - yearBirth; //计算整岁数
    if (monthNow <= monthBirth) {
      if (monthNow == monthBirth) {
        if (dayOfMonthNow < dayOfMonthBirth) age--; //当前日期在生日之前，年龄减一
      } else {
        age--; //当前月份在生日之前，年龄减一
      }
    }
    return age.toString();
  }
}

```









































```
Text(
        TimeUtil.convertTime(int.parse(sentTime)),
        
      ),
```

