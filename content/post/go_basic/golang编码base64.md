---
title: "Golang编码base64"
date: 2021-09-13T16:41:42+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

go语言的encoding/base64包提供了base64编码函数。

```
// 导入包
import "encoding/base64"
```

## 1.base64编码

```
// 待编码字符串，因为base64编码函数接受的参数是字节数组，因此需要转换下类型
data := []byte("any + old & data")
// base64编码
str := base64.StdEncoding.EncodeToString(data)
fmt.Println(str)
```

## 2.base64解码

```
str := "c29tZSBkYXRhIHdpdGggACBhbmQg77u/"
// 对字符串进行base64解码， 返回的结果是字节数组，自己根据需要转换类型
data, err := base64.StdEncoding.DecodeString(str)
if err != nil {
    fmt.Println("error:", err)
    return
}
fmt.Printf("%q\n", data)
```
