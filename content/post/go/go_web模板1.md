---
title: "Go_web模板1"
date: 2021-08-20T22:47:59+08:00
draft: false
tags: ["Go"]
categories: ["Go"]
---

> 本文主要介绍

<!--more-->概述

**模板引擎**是 Web 编程中必不可少的一个组件。**模板**能分离逻辑和数据，使得逻辑简洁清晰，并且模板可复用。引用第二篇文章[《程序结构》](https://darjun.github.io/2019/12/05/goweb/structure/)一文中的图示，我们可以看到模板引擎在 Web 程序结构中的位置：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/structure1.png)



模板引擎按照功能可以划分为两种类型：

- **无逻辑模板引擎**：此类模板引擎只进行字符串的替换，无其它逻辑；
- **嵌入逻辑模板引擎**：此类模板引擎可以在模板中嵌入逻辑，实现流程控制/循环等。

这两类模板引擎都比较极端。无逻辑模板引擎需要在处理器中额外添加很多逻辑用于生成替换的文本。而嵌入逻辑模板引擎则在模板中混入了大量逻辑，导致维护性较差。实用的模板引擎一般介于这两者之间。

在Go 语言中，`text/template`和`html/template`两个库实现模板功能。

模板内容可以是 UTF-8 编码的任何内容。其中用`{{`和`}}`包围的部分称为**动作**，`{{}}`外的其它文本在输出保持不变。模板需要应用到数据，模板中的动作会根据数据生成响应的内容来替换。

模板解析之后可以多次执行，也可以并行执行，但是注意使用同一个`Writer`会导致输出交替出现。

模板的内容较多，我将分为两篇文章介绍。本文介绍`text/template`，包括 Go 模板的基本概念，用法和注意点。下篇文章介绍`html/template`。

## 初体验

使用模板引擎一般有 3 个步骤：

- 定义模板（直接使用字符串字面量或文件）；
- 解析模板（使用`text/template`或`html/template`中的方法解析）；
- 传入数据生成输出。

```golang
package main

import (
	"log"
	"os"
	"text/template"
)

type User struct {
	Name string
	Age  int
}

func stringLiteralTemplate() {
	s := "My name is {{ .Name }}. I am {{ .Age }} years old.\n"
	t, err := template.New("test").Parse(s)
	if err != nil {
		log.Fatal("Parse string literal template error:", err)
	}

	u := User{Name: "darjun", Age: 28}
	err = t.Execute(os.Stdout, u)
	if err != nil {
		log.Fatal("Execute string literal template error:", err)
	}
}

func fileTemplate() {
	t, err := template.ParseFiles("test")
	if err != nil {
		log.Fatal("Parse file template error:", err)
	}

	u := User{Name: "dj", Age: 18}
	err = t.Execute(os.Stdout, u)
	if err != nil {
		log.Fatal("Execute file template error:", err)
	}
}

func main() {
	stringLiteralTemplate()

	fileTemplate()
}
```

在可执行程序目录中新建模板文件`test`，并写入下面的内容：

```
My name is {{ .Name }}. I am {{ .Age }} years old.

```

首先调用`template.New`创建一个模板，参数为模板名。

然后调用`Template`类型的`Parse`方法，解析模板字符串，生成模板主体。这个方法返回两个值。如果模板语法正确，则返回模板对象本身和一个 nil 值。 如果有语法错误，则返回一个 error 类型的值作为第二个返回值，这时不应该使用第一个返回值。

最后，调用模板对象的`Execute`方法，传入参数。`Execute`执行模板中的动作，将结果输出到`os.Stdout`，即**标准输出**。最终我们看到模板中`{{ .Name }}`被`u`的`Name`字段替换，`{{ .Age }}`被`u`的`Age`字段替换，标准输出中显示下面一行字符串：

```fallback
My name is darjun. I am 28 years old.
```

上面代码中，`fileTemplate`函数还演示了如何从文件中加载模板。其中`template.ParseFiles`方法会创建一个模板，并将用户指定的模板文件名用作这个新模板的名字：

```
t, err := template.ParseFiles("test")
```

相当于：

```golang
t := template.New("test")
t, err := t.ParseFiles("test")
```

## 动作

Go 模板中的动作就是一些嵌入在模板里面的命令。动作大体上可以分为以下几种类型：

- 点动作；
- 条件动作；
- 迭代动作；
- 设置动作；
- 包含动作。

### 点动作

在介绍其它的动作之前，我们先看一个很重要的动作，**点动作**（`{{ . }}`）。它其实代表是传递给模板的数据，其他动作或函数基本上都是对这个数据进行处理，以此来达到格式化和内容展示的目的。

对前面的代码示例稍作修改：

```
func main() {
	s := "The user is {{ . }}."
	t, err := template.New("test").Parse(s)
	if err != nil {
		log.Fatal("Parse error:", err)
	}

	u := User{Name: "darjun", Age: 28}
	err = t.Execute(os.Stdout, u)
	if err != nil {
		log.Fatal("Execute error:", err)
	}
}
```

运行程序，标准输出显示：

```fallback
The user is {darjun 28}.
```

实际上，`{{ . }}`会被替换为传给给模板的数据的字符串表示。这个字符串与以数据为参数调用`fmt.Sprint`函数得到的内容相同。我们可以为`User`结构编写一个方法：

```golang
func (u User) String() string {
	return fmt.Sprintf("(name:%s age:%d)", u.Name, u.Age)
}
```

这样替换的字符串就是格式化之后的内容了：

```
The user is (name:darjun age:28).
```

注意：为了使用的方便和灵活，在模板中不同的上下文内，`.`的含义可能会改变，下面在介绍不同的动作时会进行说明。

### 条件动作

在介绍动作的语法时，我采用 Go 标准库中的写法。我觉得这样写更严谨。 其中`pipeline`表示管道，后面会有详细的介绍，现在可以将它理解为一个值。 `T1/T2`等形式表示**语句块**，里面可以嵌套其它类型的动作。**最简单的语句块就是不包含任何动作的字符串**。

条件动作的语法与编程语言中的`if`语句语法类似，有几种形式：

形式一：

```
{{ if pipeline }} T1 {{ end }}
```

如果管道计算出来的值不为空，执行`T1`。否则，不生成输出。下面都表示空值：

- `false`、0、空指针或接口；
- 长度为 0 的数组、切片、map或字符串。

形式二：

```
{{ if pipeline }} T1 {{ else }} T2 {{ end }}
```

如果管道计算出来的值不为空，执行`T1`。否则，执行`T2`。

形式三：

如果管道`pipeline1`计算出来的值不为空，则执行`T1`。反之如果管道`pipeline2`的值不为空，执行`T2`。如果都为空，执行`T3`。

举个栗子：

```golang
type AgeInfo struct {
	Age           int
	GreaterThan60 bool
	GreaterThan40 bool
}

func main() {
	t, err := template.ParseFiles("test")
	if err != nil {
		log.Fatal("Parse error:", err)
	}

	rand.Seed(time.Now().Unix())
	age := rand.Intn(100)
	info := AgeInfo {
		Age:           age,
		GreaterThan60: age > 60,
		GreaterThan40: age > 40,
	}
	err = t.Execute(os.Stdout, info)
	if err != nil {
		log.Fatal("Execute error:", err)
	}
}
```

在可执行程序的目录下新建模板文件`test`，键入下面的内容：

```fallback
Your age is: {{ .Age }}
{{ if .GreaterThan60 }}
Old People!
{{ else if .GreaterThan40 }}
Middle Aged!
{{ else }}
Young!
{{ end }}
```