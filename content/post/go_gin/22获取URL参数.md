---
title: "获取url参数"
date: 2021-09-05T23:10:11+08:00
draft: false
tags: ["go",""gin]
categories: ["go","gin"]
 
---


在博客 分类中经常用到

```
package main

import (
   "github.com/gin-gonic/gin"
   "net/http"
)

func main(){
   //Default返回一个默认的路由引擎
   r := gin.Default()
   r.GET("/user/search/:username/:address", func(c *gin.Context) {
      //获取路径参数
      username := c.Param("username")
      address := c.Param("address")
      //输出json结果给调用方
      c.JSON(http.StatusOK, gin.H{
         "message":  "ok",
         "username": username,
         "address":  address,
      })
      r.GET("/user/blog/:mouth/:year", func(c *gin.Context) {
         mouth:=c.Param("mouth")
         year :=c.Param("year")
         c.JSON(http.StatusOK,gin.H{
            "mouth":mouth,
            "year":year,
         })
      })
   })

   r.Run(":8080")
}
```