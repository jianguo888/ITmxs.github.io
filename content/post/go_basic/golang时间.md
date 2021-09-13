---
title: "Golang时间"
date: 2021-09-13T16:39:22+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

go语言的time包，提供时间处理函数，方便我们获取时间戳、时间格式化、处理日期。

在go语言中内置的时间类型是：Time, 我们可以将字符串时间转换成Time类型，也能将Time类型转换成字符串时间。

> 提示: 使用时间函数，需要导入time包。

## 1.获取当前时间

```
// 获取当前时间，返回的是Time类型
t := time.Now()

fmt.Println(t)
```

## 2.获取时间戳

1970-01-01开始经过的秒数。

```
// 获取当前时间的时间戳 
s := time.Now().Unix()

// 获取某个Time时间的时间戳。
// t是Time类型对象
s := t.Unix()
```

## 3.格式化时间

如何将Time类型转换成我们想要的字符串时间格式。

```
// 获取当前时间
t := time.Now()

// 按指定格式，格式化时间
s := t.Format("2006-01-02 15:04:05")

fmt.Println(s)

// 返回年月日
year, month, day := t.Date()

// 返回是时分秒
hour,minute,sec := t.Clock()

// 返回星期几，从0开始
weekday := t.Weekday()
// 可以转换成整数
fmt.Println(int(weekday))
```

输出:

```
2019-09-15 10:43:14
```

格式化时间主要通过Time对象的Format函数处理。

函数定义:

```
func (t Time) Format(layout string) string
```

只有一个参数layout，layout就是指定我们需要格式化时间的模版。

下面是Time包预定于的时间模版：

```
const (
    ANSIC       = "Mon Jan _2 15:04:05 2006"
    UnixDate    = "Mon Jan _2 15:04:05 MST 2006"
    RubyDate    = "Mon Jan 02 15:04:05 -0700 2006"
    RFC822      = "02 Jan 06 15:04 MST"
    RFC822Z     = "02 Jan 06 15:04 -0700" // 使用数字表示时区的RFC822
    RFC850      = "Monday, 02-Jan-06 15:04:05 MST"
    RFC1123     = "Mon, 02 Jan 2006 15:04:05 MST"
    RFC1123Z    = "Mon, 02 Jan 2006 15:04:05 -0700" // 使用数字表示时区的RFC1123
    RFC3339     = "2006-01-02T15:04:05Z07:00"
    RFC3339Nano = "2006-01-02T15:04:05.999999999Z07:00"
)
```

使用例子:

```
t := time.Now()
// 使用预定义的时间模版time.RFC3339
fmt.Println(t.Format(time.RFC3339))
```

输出:

```
2019-09-15T10:50:03+08:00
```

时间模版里面的参数不能随便书写，建议大家根据上面预定义的时间模版，进行修改。

例子:

```
// 获取当前时间
t := time.Now()

// 只需要格式化年月日
tpl1 := "2006-01-02"
fmt.Println(t.Format(tpl1))


// 只需要格式化输出时间
tpl2 := "15:04:05"
fmt.Println(t.Format(tpl2))
```

## 4.时间戳和日期互转

go的Time时间类型可以转换成时间戳，也能转换成字符串日期类型，因此我们只要将时间戳或者日期时间（字符串）转换成Time类型，即可完成自由转换。

### 4.1. 时间戳转日期

```
// 将时间戳转换成Time类型
// 第一个参数是时间戳（毫秒），第二个参数是纳秒，设置0即可
t := time.Unix(1568516266,0)

// 将Time转换成我们想要的日期格式。
s := t.Format("2006-01-02 15:04:05")

fmt.Println(s)
```

### 4.2. 日期转时间戳

```
// 将日期转换成Time类型
// 获取本地时区，重要
loc, _ := time.LoadLocation("Local")
// 指定我们要解析的时间模版
l := "2006-01-02 15:04:05"
// 调用ParseInLocation函数，将日期格式转换成Time类型
t, _ := time.ParseInLocation(l, "2019-09-15 10:57:46", loc)
	
// 将Time转换成时间戳
s := t2.Unix()

fmt.Println(s)
```

## 5.时间计算

项目中经常需要计算时间的前一天、后一天、前一周、上一月。

Time类型常用的时间计算函数：

- Add - 返回增加指定时间的Time
- AddDate - 返回增加指定的年份、月份和天数的时间点Time
- Sub - 返回两个Time类型相减的时间间隔（Duration类型）

### 5.1. Add

函数定义：

```
func (t Time) Add(d Duration) Time
```

Add返回时间点t+d。

例子：

```
t := time.Now()
// 计算前一分钟，当前时间加上负一分钟
t1 := t.Add(-time.Minute)
```

### 5.2. AddDate

函数定义：

```
func (t Time) AddDate(years int, months int, days int) Time
```

AddDate返回增加了给出的年份、月份和天数的时间点Time。

例子:

```
t := time.Now()
// 计算前一天, 当前时间加上0年，0月，-1天
t1 := t.AddDate(0,0,-1)
	
// 计算上一个月时间,当前时间加上0年，-1月，0天
t1 := t.AddDate(0,-1,0)
```

### 5.3. Sub

函数定义：

```
func (t Time) Sub(u Time) Duration
```

返回一个时间段t-u。如果结果超出了Duration可以表示的最大值/最小值，将返回最大值/最小值。要获取时间点t-d（d为Duration），可以使用t.Add(-d)。

例子：

```
t := time.Now()

// 计算前一天
t1 := t.AddDate(0,0,-1)

// 两个时间相减
s := t.Sub(t1)

// 因为时间间隔使用的是Duration类型，我们可以将时间单位转换成小时
h := int(s/time.Hour)

// 也可以换成成其他单位，只要除于指定的时间常量即可:
// - Second 秒
// - Minute 分钟
// - Hour  小时

fmt.Println(h)
```

## 6. 时间比较

Time类型常用的时间比较函数如下：

- Equal - 比较两个时间是否相等
- Before - 相当于小于比较
- After - 相当于大于比较

例子：

```
t := time.Now()

// 计算前一天
t1 := t.AddDate(0,0,-1)

if t.Equal(t1) {
	fmt.Println("时间相等")
}

if t1.Before(t) {
	fmt.Println("时间t1 < t")
}

if t.After(t1) {
	fmt.Println("时间t > t1")
}
```
