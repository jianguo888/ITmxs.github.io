---
title: "Func"
date: 2021-09-12T15:51:34+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

> 本文主要介绍func



# func

![image-20210912155140848](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210912155140848.png)



## 匿名

![image-20210912155159245](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210912155159245.png)

![image-20210912155357595](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210912155357595.png)

## 不定项参数

![image-20210912155503668](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210912155503668.png)

![image-20210912155553301](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210912155553301.png)

## 闭包

![image-20210912160019267](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210912160019267.png)

## 延迟调用

![image-20210912160128895](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210912160128895.png)

![image-20210913201643325](C:/Users/Luckly/AppData/Roaming/Typora/typora-user-images/image-20210913201643325.png)

![image-20210913201702759](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210913201702759.png)

# 接口

```go
package main

import "fmt"

type Animal interface {
	Eat()
	Run()
}

type Cat struct {
	Name string
	Sex  bool
}
type Dog struct {
	Name string
}

func (d Dog) Run() {
	fmt.Println(d.Name, "开始跑")
}
func (d Dog) Eat() {
	fmt.Println(d.Name, "开始吃")
}
func (d Cat) Run() {
	fmt.Println(d.Name, "开始跑")
}
func (d Cat) Eat() {
	fmt.Println(d.Name, "开始吃")
}
func main() {
	//var a Animal
	//c := Cat{
	//	Name: "tom",
	//	Sex:  true,
	//}
	//
	//a = c
	//a.Eat()
	//a.Run()
	//fmt.Println(c)
	c := Cat{
		Name: "tom",
		Sex:  true,
	}
	MYfunc(c)

}

func MYfunc(a Animal) {
	a.Run()
	a.Eat()

}

```







```go
package main

import "fmt"

type Animal interface {
	Eat()
	Run()
}

type Cat struct {
	Name string
	Sex  bool
}
type Dog struct {
	Name string
}

func (d Dog) Run() {
	fmt.Println(d.Name, "开始跑")
}
func (d Dog) Eat() {
	fmt.Println(d.Name, "开始吃")
}
func (d Cat) Run() {
	fmt.Println(d.Name, "开始跑")
}
func (d Cat) Eat() {
	fmt.Println(d.Name, "开始吃")
}

var L Animal

func main() {
	//var a Animal
	//c := Cat{
	//	Name: "tom",
	//	Sex:  true,
	//}
	//
	//a = c
	//a.Eat()
	//a.Run()
	//fmt.Println(c)
	c := Cat{
		Name: "tom",
		Sex:  true,
	}
	MYfunc(c)
	L.Run()

}

func MYfunc(a Animal) {
	L = a

}

```



并发神器





断言