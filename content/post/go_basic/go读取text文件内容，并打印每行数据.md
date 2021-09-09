---
title: "Go读取text文件内容，并打印每行数据"
date: 2021-09-09T23:25:34+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍Go读取text文件内容，并打印每行数据



```go
package main

import (
	"bufio"
	"fmt"
	"log"
	"os"
	"strconv"
)

func ReadFileFloats(fileName string) ([3]float64, error) {

	var numbers [3]float64

	file, err := os.Open(fileName)
	if err != nil {
		return numbers, err
	}

	scanner := bufio.NewScanner(file)
	i := 0
	for scanner.Scan() {
		number, err := strconv.ParseFloat(scanner.Text(), 64)
		if err != nil {
			return numbers, err
		}
		numbers[i] = number
		i += 1
	}

	err = file.Close()
	if err != nil {
		return numbers, err
	}

	if scanner.Err() != nil {
		return numbers, scanner.Err()
	}

	return numbers, nil
}

func main() {
	fileName := "p012_go_read_file.txt"
	numbers, err := ReadFileFloats(fileName)
	if err != nil {
		log.Fatal(err)
	}
	fmt.Println(numbers)
}
```

