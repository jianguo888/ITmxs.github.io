---
title: "获取querystring参数"
date: 2021-09-05T23:10:11+08:00
draft: false
tags: ["go","gin"]
categories: ["go","gin"]
 
---


[李文周的博客](https://www.liwenzhou.com/posts/Go/Gin_framework/)



**获取querystring参数**

`querystring`指的是URL中`?`后面携带的参数，例如：`/user/search?username=ITmxs&address=兰州`。 获取请求的querystring参数的方法如下：

```
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main()  {
	r:=gin.Default()
	r.GET("/user/search", func(c *gin.Context) {
		username := c.DefaultQuery("username", "我爱Gin")//娶不到就用默认值
		//username := c.Query("username")
		address := c.Query("address")
		//输出json结果给调用方
		c.JSON(http.StatusOK, gin.H{
			"message":  "ok",
			"username": username,
			"address":  address,
		})
	})
	r.Run(":9000")

}

```

