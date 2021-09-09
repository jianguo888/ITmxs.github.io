---
title: "Go计算一个数组的平均值"
date: 2021-09-09T23:20:24+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

> 本文主要介绍计算一个数组的平均值



```go
// 计算一个数组的平均值

package main

import "fmt"

func GetArraySum(numbers [3]float64) float64 {
	var sumv float64 = 0.0
	for _, number := range numbers {
		sumv += number
	}
	return sumv
}

func getArraySum2(numbers [3]float64) float64 {
	var sumv float64 = 0.0
	for i := 0; i < len(numbers); i++ {
		sumv += numbers[i]
	}
	return sumv
}

func main() {
	var numbers = [3]float64{3.14, 2.67, 6.12}
	fmt.Println("numbers", numbers)

	// 计算总数
	sumValue := getArraySum2(numbers)
	fmt.Println("numbers.sum", sumValue)

	// 计算平均数
	avgValue := sumValue / float64(len(numbers))
	fmt.Printf("numbers.avg: %.2f\n", avgValue)
}
```

