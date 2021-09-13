---
title: "Golang字符串操作"
date: 2021-09-13T16:41:19+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

go语言的strings包实现了处理字符串的简单函数，例如：字符串搜索、字符串分割、裁剪等等。

下面介绍常用的字符串操作函数

## 1.Contains

函数定义：

```
func Contains(s, substr string) bool
```

判断字符串s是否包含子串substr。

例子：

```
fmt.Println(strings.Contains("seafood", "foo"))
fmt.Println(strings.Contains("seafood", "bar"))
fmt.Println(strings.Contains("seafood", ""))
fmt.Println(strings.Contains("", ""))
```

输出:

```
true
false
true
true
```

## 2.Count

函数定义：

```
func Count(s, sep string) int
```

返回字符串s中有几个不重复的sep子串

例子：

```
// 输出：3， 字符e，在cheese中出现3次
fmt.Println(strings.Count("cheese", "e"))
```

## 3.Index

函数定义:

```
func Index(s, sep string) int
```

字符串查找函数，含义是子串sep在字符串s中第一次出现的位置，不存在则返回-1

例子：

```
fmt.Println(strings.Index("chicken", "ken"))
fmt.Println(strings.Index("chicken", "dmr"))
```

输出：

```
4
-1
LastIndex函数的作用和用法跟Index类似，区别是LastIndex返回最后一个匹配到的子字符串，不存在则返回-1。
```

## 4.字符串转换成大写字母

```
s := strings.ToUpper("Gopher")
```

## 5.字符串转换成小写字母

```
s := strings.ToLower("Gopher")
```

## 6.Trim

函数定义:

```
func Trim(s string, cutset string) string
```

将s字符串前后位置的所有cutset字符都去掉，例如，去掉字符串前后的空格, 第二个参数就是我们要去掉的字符

```
s := strings.Trim(" www.tizi365.com    ", " ")
```

## 7.TrimSpace

将s前后端所有空白（unicode.IsSpace指定）都去掉的字符串。

```
fmt.Println(strings.TrimSpace(" \t\n a lone gopher \n\t\r\n"))
```

输出：

```
a lone gopher
```

## 8.Replace

函数定义:

```
func Replace(s, old, new string, n int) string
```

将s中前n个不重叠old子串都替换为new的新字符串，如果n<0会替换所有old子串。

例子:

```
fmt.Println(strings.Replace("oink oink oink", "k", "ky", 2))
fmt.Println(strings.Replace("oink oink oink", "oink", "moo", -1))
```

输出:

```
oinky oinky oink
moo moo moo
```

## 9.Split

函数定义：

```
func Split(s, sep string) []string
```

用sep字符串去切割s字符串，最后返回一个切割后的字符串数组

例子：

```
fmt.Printf("%q\n", strings.Split("a,b,c", ","))
```

输出:

```
["a" "b" "c"]
```

## 10.Join

函数定义：

```
func Join(a []string, sep string) string
```

将一系列字符串连接为一个字符串，字符串之间用sep来分隔，Join函数就是Split函数的反向操作。

例子：

```
s := []string{"foo", "bar", "baz"}
fmt.Println(strings.Join(s, ", "))
```

输出:

```
foo, bar, baz
```
