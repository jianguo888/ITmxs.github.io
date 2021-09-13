---
title: "Golang处理url"
date: 2021-09-13T16:40:36+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

golang语言net/url包，提供了url解析，url参数处理的函数。

## 1.url地址解析

```go
package main

import (
	"fmt"
	"net/url"
)

func main() {
	// 解析url地址
	u, err := url.Parse("http://bing.com/search?q=flutter")
	if err != nil {
		panic(err)
	}

	// 打印格式化的地址信息
	fmt.Println(u.Scheme) // 返回协议
	fmt.Println(u.Host) // 返回域名
	fmt.Println(u.Path) // 返回路径部分
	fmt.Println(u.RawQuery) // 返回url的参数部分

	params := u.Query() // 以url.Values数据类型的形式返回url参数部分,可以根据参数名读写参数

	fmt.Println(params.Get("q")) // 读取参数q的值
}
```

## 2.url参数编码(encode)

url包提供了url.Values类型，专门用来处理url参数。

```go
// 定一个Values
v := url.Values{}

// 设置url参数
v.Set("name", "Ava")
// 添加参数，类似set，区别是add函数会将值追加到同一个参数的值后面，形成数组
v.Add("friend", "Jess")
v.Add("friend", "Sarah")
v.Add("friend", "Zoe")
// 调用Encode函数，对参数进行编码
fmt.Println(v.Encode())
// 输出："name=Ava&friend=Jess&friend=Sarah&friend=Zoe"

// 下面是读取url参数的方式
fmt.Println(v.Get("name"))
fmt.Println(v.Get("friend"))
fmt.Println(v["friend"])
```

## 3.url参数解码(decode)

```go
package main

import (
	"fmt"
	"net/url"
)

func main() {
	// url参数
	q := "username=tizi&password=12345&type=100"
	// 解析url参数
	values, err := url.ParseQuery(q)

	if err != nil {
		panic(err)
	}

	// 根据参数名查询参数值
	fmt.Println(values.Get("username"))
	fmt.Println(values.Get("password"))
	fmt.Println(values.Get("type"))
}
```

[❮ 上一页](https://space.bilibili.com/480883651/archives/351.html)
