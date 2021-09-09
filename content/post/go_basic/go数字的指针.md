---
title: "Go数字的指针"
date: 2021-09-09T23:22:09+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

> 本文主要介绍数字的指针

```go
/ 数字的指针
package main

import "fmt"

func doubleNumber(number *int) {
	*number *= 2
}

func main() {
	var myInt int
	var myIntPointer *int

	myInt = 6
	myIntPointer = &myInt

	fmt.Println(myIntPointer, *myIntPointer)

	// 数字加倍指针版本
	fmt.Println(myInt)
	doubleNumber(&myInt)
	fmt.Println(myInt)
}
```

