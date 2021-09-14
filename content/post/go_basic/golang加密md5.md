---
title: "Golang加密md5"
date: 2021-09-13T16:41:01+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

go语言内置的crypto/md5包，提供了md5加密处理的函数库，下面介绍golang如何进行md5加密。

例子:

```go
package main

import (
	"crypto/md5"
	"fmt"
)

func main() {
	// 待加密字符串
	s := "https://space.bilibili.com/480883651"

	// 进行md5加密，因为Sum函数接受的是字节数组，因此需要注意类型转换
	srcCode := md5.Sum([]byte(s))

	// md5.Sum函数加密后返回的是字节数组，需要转换成16进制形式
	code := fmt.Sprintf("%x", srcCode)

	fmt.Println(string(code))
}
```

![image-20210913171814341](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210913171814341.png)



dart 加密mds

dart有内置的md5加密包，先引入头文件：

```dart
import 'dart:convert';
import 'package:crypto/crypto.dart';

void main() {

  // 待加密字符串
  var s = "https://space.bilibili.com/480883651";

  var content = new Utf8Encoder().convert(s);
  var digest = md5.convert(content);
  print(digest);
}
```

![image-20210913171828452](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210913171828452.png)





```go

  static String generateMD5(String data) {
    var content = new Utf8Encoder().convert(data);
    var digest = md5.convert(content);
    // 这里其实就是 digest.toString()
    return hex.encode(digest.bytes);
  }
```

