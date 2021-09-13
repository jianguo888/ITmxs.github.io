---
title: "Golang系统函数"
date: 2021-09-13T16:40:14+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

go语言os包提供了操作系统函数的不依赖平台的接口。

下面是golang常用的系统函数.

## 1. Hostname

函数定义:

```
func Hostname() (name string, err error)
```

Hostname返回内核提供的主机名。

## 2. Environ

函数定义:

```
func Environ() []string
```

返回所有的环境变量，的格式为"key=value"的字符串的切片拷贝。

## 3. Getenv

函数定义:

```
func Getenv(key string) string
```

Getenv检索并返回名为key的环境变量的值。如果不存在该环境变量会返回空字符串。

## 4. Setenv

函数定义:

```
func Setenv(key, value string) error
```

Setenv设置名为key的环境变量。如果出错会返回该错误。

## 5. Exit

函数定义:

```
func Exit(code int)
```

Exit让当前程序以给出的状态码code退出。一般来说，状态码0表示成功，非0表示出错。程序会立刻终止，defer的函数不会被执行。

## 6. Getuid

函数定义:

```
func Getuid() int
```

Getuid返回调用者的用户ID。

## 7. Getgid

函数定义:

```
func Getgid() int
```

Getgid返回调用者的组ID。

## 8. Getpid

函数定义:

```
func Getpid() int
```

Getpid返回调用者所在进程的进程ID。

## 9. Getwd

函数定义:

```
func Getwd() (dir string, err error)
```

Getwd返回一个对应当前工作目录的根路径。如果当前目录可以经过多条路径抵达（因为硬链接），Getwd会返回其中一个。

## 10. Mkdir

函数定义:

```
func Mkdir(name string, perm FileMode) error
```

Mkdir使用指定的权限和名称创建一个目录。如果出错，会返回*PathError底层类型的错误。

例子:

```
os.Mkdir("/var/tizi", 0666)
```

## 11. MkdirAll

函数定义:

```
func MkdirAll(path string, perm FileMode) error
```

MkdirAll创建目录包括子目录，这是跟Mkdir最大的区别

## 12. Remove

函数定义:

```
func Remove(name string) error
```

Remove删除name指定的文件或目录。如果出错，会返回*PathError底层类型的错误。

RemoveAll函数，跟Remove用法一样，区别是会递归的删除所有子目录和文件
