---
title: "利用Gin框架获取form参数"
date: 2021-09-05T23:10:11+08:00
draft: false
tags: ["go","gin"]
categories: ["go","gin"]
 
---


**利用Gin框架获取form参数**

除了通过URL查询参数提交数据到服务器外，常用的还有通过Form表单的方式。Form表单相比URL查询参数，用户体验好，可以承载更多的数据，尤其是文件上传，所以也更为方便。Form 表单对于Form表单，我们不会陌生，比如input文本框、密码框等等，可以让我们输入一些数据，然后点击「保存」、「提交」等按钮，把数据提交到服务器的。对于Form表单来说，有两种提交方式GET和POST。其中GET方式就是我们前两篇文章的URL查询参数的方式，参考即可获得对应的参数键值对，这篇文章主要介绍POST的方式的表单，而Gin处理的也是这种表单。

主函数：

```
package main

import (
	"github.com/gin-gonic/gin"
	"net/http"
)

func main()  {
	//Default返回一个默认的路由引擎
	r := gin.Default()
	r.LoadHTMLFiles("./login.html","./index.html")
	r.GET("/login", func(c *gin.Context) {
		c.HTML(http.StatusOK,"login.html",nil)
	})
	//login post
	r.POST("/login", func(c *gin.Context) {
		//获取Form表单提交的数据
		//username:=c.PostForm("username")
		//password:=c.PostForm("password")
		username:=	c.DefaultPostForm("username","somebody")
		password:=c.DefaultPostForm("password","***")//改为页面中没有定义的，则会返回***
		c.HTML(http.StatusOK,"index.html",gin.H{
			"Name": username,
			"Password":password,

		})
	})

	r.Run(":8080")
}


```

login页面

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>login</title>
</head>
<body>
<form action="/login" method="post" navalidate autocomplete="off" u>
    <div>
        <label for="username"> username:</label>
        <input type="text" name="username" id="username">

    </div>
    <div>
        <label for="password"> password:</label>
        <input type="password" name="password" id="password">

    </div>
    <div>
        <input type="submit" value="登陆">

    </div>
</form>
</body>
</html>
```

index页面

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>index</title>
</head>
<body>
<h1> hello {{.Name}}!</h1>
<p>你的密码是{{.Password}}</p>
</body>
</html>
```



没有定义时访问http://localhost:8080/login

![image-20200813150247941](https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200813150247941.png)

![image-20200813150012069](https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200813150012069.png)

正确定义后：

![image-20200813150447342](https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200813150447342.png)

取不到的时候。注意观察细节

![image-20200813151106093](https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200813151106093.png)

![image-20200813151123214](https://mxszs.oss-cn-beijing.aliyuncs.com/img/image-20200813151123214.png)