---
title: "Golang信号处理"
date: 2021-09-13T16:28:12+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

signal包实现了对输入信号的访问。

在开发服务端程序的时候，常常需要通过linux系统信号通知程序做一些任务，例如重启、重新加载配置、停止接收新的请求、退出程序前清理工作。

在go语言中，通过通道（channel）可以接收系统信号，通过Notify函数可以注册我们需要监听的系统信号。

例子:

```go
package main

import (
	"fmt"
	"os"
	"os/signal"
)

func main() {
	// 创建一个信号通道，用于接收信号，通道容量为 1
	c := make(chan os.Signal, 1)
	
	// 调用Notify注册，通过通道c接收Interrupt和Kill两种信号
	signal.Notify(c, os.Interrupt, os.Kill)

	// 从通道中读取收到的信号，没有信号则阻塞协程
	switch <-c {
	case os.Interrupt:
		fmt.Println("收到：Interrupt信号")
	case os.Kill:
		fmt.Println("收到：Kill信号")
	default:
		fmt.Println("收到其他信号")

	}
}
```
