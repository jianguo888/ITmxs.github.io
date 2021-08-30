---
title: "Flutter 在Row中设置Element之间的间距"
date: 2021-08-30T20:52:59+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

代码:

```
new Container(
          alignment: FractionalOffset.center,
          child: new Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: <Widget>[
              new FlatButton(
                child: new Text('Don\'t have an account?', style: new TextStyle(color: Color(0xFF2E3233))),
              ),
              new FlatButton(
                child: new Text('Register.', style: new TextStyle(color: Color(0xFF84A2AF), fontWeight: FontWeight.bold),),
                onPressed: moveToRegister,
              )
            ],
          ),
        ),  
```


结果如下:


如何修复这两个 `FlatButton`元素在屏幕宽度的中心之间并排没有空间？



**最佳答案**

删除空间-:

```
new Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: <Widget>[
              GestureDetector(
                child: new Text('Don\'t have an account?',
                    style: new TextStyle(color: Color(0xFF2E3233))),
                onTap: () {},
              ),
              GestureDetector(
                onTap: (){},
                  child: new Text(
                'Register.',
                style: new TextStyle(
                    color: Color(0xFF84A2AF), fontWeight: FontWeight.bold),
              ))
            ],
          ),
```


或

```
GestureDetector(
            onTap: (){},
            child: new Row(
              mainAxisAlignment: MainAxisAlignment.center,
              children: <Widget>[
                new Text('Don\'t have an account?',
                    style: new TextStyle(color: Color(0xFF2E3233))),
                new Text(
                  'Register.',
                  style: new TextStyle(
                  color: Color(0xFF84A2AF), fontWeight: FontWeight.bold),
                )
              ],
            ),
          ),
```