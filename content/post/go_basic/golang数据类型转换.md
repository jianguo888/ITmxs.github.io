---
title: "Golang数据类型转换"
date: 2021-09-13T16:38:15+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

# golang 数据类型转换

下面是go语言中各种数据类型之间互相转换的方法。

## 1. 基本数据类型转换

语法:

```
t := 新类型(变量)
```

例子:

```
var a int = 1
// 将int类型转换成int32类型
b := int32(a)
	
// 将int32转换成float64类型
c := float64(b)
```

数值类型之间可以互相转换。

## 2. 数字转字符串

```
a := 100
// 将int类型转换成字符串, 支持int, int32, int64
s := strconv.FormatInt(int64(a), 10)

var a1 uint = 100
// 将uint无符号类型转换成字符串，支持uint、uint32、uint64
s1 := strconv.FormatUint(uint64(a1), 10)
```

使用Sprintf将数字类型转换成字符串

```
// 将浮点数类型，转换成字符串，并且保留2位小数 (%.2f)
s2 := fmt.Sprintf("%.2f", 25.222)
// 将整数转换成字符串
s3 := fmt.Sprintf("%d", 1000)
```

## 3. 字符串转数字

```
a := "100"
d, _ := strconv.Atoi(a)
fmt.Println(d)
```

## 4. interface类型转换

语法:

```
t := v.(type)
```

v是interface{}类型，type是我们要转换的类型。

> 注意: interface类型转换，只有类型完全匹配的时候才能转换，例如: interface变量保存的是int类型，只能转换成int类型，不能转换成int32类型。

例子:

```
var d int = 1000
// 将d的值保存到interface变量中
var i interface{} = d

// 将interface类型转换成原来的类型	
v, ok := i.(int)
// 通过ok判断转换是否成功
if ok {
	fmt.Println("转换成功", v)
} else {
	fmt.Println("转换失败")
}
```

## 5. string转byte数组

```
s := "我是字符串"

// 转换成功字节数组
bs := []byte(s)
```

## 6. byte数组转string

```
data := []byte("我是字节数组")

// 转换成字符串
s := string(data)
```
