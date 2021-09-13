---
title: "Golang解析json"
date: 2021-09-13T16:40:00+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

go语言中json包实现了json对象的编解码，通常在go语言中对于json的解析操作都是通过结构体（struct）和json字符串之间互相转换实现。

**常用的json函数:**

- Marshal - 将struct结构体转换成json字符串
- Unmarshal - 将json字符串转换成struct结构体类型

## 1. Marshal

函数定义:

```
func Marshal(v interface{}) ([]byte, error)
```

Marshal函数的作用是将结构体对象转换成json字符串。

例子:

```
package main

import (
	"encoding/json"
	"fmt"
)
func main() {
	// 定义结构体
	type ColorGroup struct {
		ID     int
		Name   string
		Colors []string
	}
	// 创建一个struct对象
	group := ColorGroup{
		ID:     1,
		Name:   "Reds",
		Colors: []string{"Crimson", "Red", "Ruby", "Maroon"},
	}
	// 将struct对象转换成json
	b, err := json.Marshal(group)
	if err != nil {
		// 转换失败，错误处理
		fmt.Println("error:", err)
	}
	
	// 因为Marshal返回的是字节数组，所以这里需要转换成字符串
	fmt.Println(string(b))
}
```

输出:

```
{"ID":1,"Name":"Reds","Colors":["Crimson","Red","Ruby","Maroon"]}
```

通过上面例子，我们知道如何将一个struct转换成json，默认json字段名和struct字段名是一样的，如果我们需要自定义json的字段名怎么处理？

通过struct标签定义json属性:

```
下面是标签定义的基本用法举例：

// 字段被忽略
Field int `json:"-"`
// 字段在json里的键为"myName"
Field int `json:"myName"`
// 字段在json里的键为"myName"且如果字段为空值将在对象中省略掉
Field int `json:"myName,omitempty"`
// 字段在json里的键为"Field"（默认值），但如果字段为空值会跳过；注意前导的逗号
Field int `json:",omitempty"`
```

上面介绍json标签的语法，下面看一个例子:

```
package main

import (
	"encoding/json"
	"fmt"
)
func main() {
	// 定义结构体
	type User struct {
		Id int
		UserName string `json:"user_name"` // 定义json字段为user_name
		Email string `json:"-"` // 忽略email字段
	}
	// 创建一个struct对象
	u := User{
		Id:100,
		UserName:"tizi",
		Email:"tizi@tizi365.com",
	}
	// 将struct对象转换成json
	b, err := json.Marshal(u)
	if err != nil {
		// 转换失败，错误处理
		fmt.Println("error:", err)
	}

	// 因为Marshal返回的是字节数组，所以这里需要转换成字符串
	fmt.Println(string(b))
}
```

输出:

```
{"Id":100,"user_name":"tizi"}
```

UserName字段名改变为user_name， Email字段被忽略了。

## 2. Unmarshal

函数定义:

```
func Unmarshal(data []byte, v interface{}) error
```

Unmarshal函数解析json编码的数据并将结果存入v指向的值, Unmarshal和Marshal做相反的操作。

例子:

```
package main

import (
	"encoding/json"
	"fmt"
)
func main() {
	// 定义结构体
	type User struct {
		Id int
		UserName string `json:"user_name"` // 定义json字段为user_name
		Email string `json:"-"` // 忽略email字段
	}
	
	// json数据，因为Unmarshal函数接受的是字节数组，所以json数据以字节数组的形式保存
	jsonData := []byte(`{"Id":100,"user_name":"tizi"}`)

	//定义一个struct对象, 用来保存json解析后的数据
	u := User{}
	
	// 将struct对象转换成json
	err := json.Unmarshal(jsonData, &u)
	if err != nil {
		// 转换失败，错误处理
		fmt.Println("error:", err)
	}

	fmt.Println(u)
}
```

json和struct互相转换支持复杂的数据类型，例如：json嵌套、struct嵌套。
