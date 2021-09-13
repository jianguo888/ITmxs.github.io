---
title: "Golang生成随机数"
date: 2021-09-13T16:42:04+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

go语言rand包实现了伪随机数生成器。

导入包：

```
import "math/rand"
```

获取随机数的例子：

```
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	// 使用当前的纳秒作为随机数种子
	rand.Seed(time.Now().UnixNano())
	// 返回大于等于0且小于100的随机数
	fmt.Println(rand.Intn(100))
}
```

> 注意: 上面的例子，使用默认的随机数生成器，生成随机数；在并发高的时候会因为加锁问题，并发性能不高，为避免加锁问题，可以分别为协程新建单独的随机数生成器，避开加锁问题。

使用随机数生成器，生成随机数的例子：

```
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	// 使用当前的纳秒作为随机数种子, 创建一个随机数生成器
	rnd := rand.New(rand.NewSource(time.Now().UnixNano()))

	// 返回大于等于0且小于100的随机数
	fmt.Println(rnd.Intn(100))
}
```
