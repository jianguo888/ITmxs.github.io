---
title: "Golang数字函数"
date: 2021-09-13T16:41:55+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

go语言math包提供了基本的数学常数和数学函数。

下面是常用的数学函数：

## 1.Ceil

```
func Ceil(x float64) float64
```

返回不小于x的最小整数（的浮点值），特例如下：

```
Ceil(±0) = ±0
Ceil(±Inf) = ±Inf
Ceil(NaN) = NaN
```

例子:

```
fmt.Println(math.Ceil(5.1)) // 输出: 6
```

## 2.Floor

```
func Floor(x float64) float64
```

返回不大于x的最大整数（的浮点值）

例子：

```
fmt.Println(math.Floor(5.1)) // 输出: 5
```

## 3.Abs

```
func Abs(x float64) float64
```

返回x的绝对值

```
d := math.Abs(-5.1) // 返回: 5.1
```

## 4.Max

```
func Max(x, y float64) float64
```

返回x和y中最大值

例子：

```
fmt.Println(math.Max(1,10)) // 输出: 10
```

## 5.Min

```
func Min(x, y float64) float64
```

返回x和y中最小值

例子:

```
fmt.Println(math.Min(1,10)) // 输出: 1
```

## 6.Sqrt

```
func Sqrt(x float64) float64
```

返回x的二次方根

例子：

```
fmt.Println(math.Sqrt(4)) // 输出: 2
```

## 7.Pow

```
func Pow(x, y float64) float64
```

计算x的y次方

例子：

```
fmt.Println(math.Pow(2,10)) // 输出: 1024
```
