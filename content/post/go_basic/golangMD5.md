go语言内置的crypto/md5包，提供了md5加密处理的函数库，下面介绍golang如何进行md5加密。

例子:

```
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
