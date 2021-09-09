---
title: "Go打印一个文件信息"
date: 2021-09-09T23:23:26+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

> 本文主要介绍 打印一个文件的信息

```go
// 打印一个文件的信息
package main

import (
	"fmt"
	"log"
	"os"
)

func main() {
	fileName := "README.md"
	fileStat, err := os.Stat(fileName)
	if err != nil {
		log.Fatal(err)
	}

	fmt.Println("文件名称：", fileStat.Name())
	fmt.Println("文件大小：", fileStat.Size())
	fmt.Println("文件修改时间：", fileStat.ModTime())
}
```

