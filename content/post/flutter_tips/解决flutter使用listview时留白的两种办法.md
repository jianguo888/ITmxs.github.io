---
title: "解决flutter使用listview时留白的两种办法"
date: 2021-09-02T10:43:35+08:00
draft: true
---

Flutter ListView解决底部或顶部留白问题
在没有设置padding的情况下，上下都有留白

# 方法一：

```c
ListView.builder(
      padding: EdgeInsets.zero,
      itemCount: ...,
      itemBuilder: ...,
    );

```
后来发现，listview不和appbar一起使用时，会有一个默认的padding
想要去掉这个padding，

# 在外层使用MediaQuery.removePadding即可

```c
MediaQuery.removePadding(
  context: context,
  removeTop: true,
  removeBottom: true,
  child: ListView.builder(
    itemBuilder: 
    itemCount: ,
    shrinkWrap: true,
 
  ),
);
```

# flutter中gridview顶上有空白的解决方法

这个间隔是gridview自带的，我们需要去掉它。很简单我们只需要加一行代码就行：padding: EdgeInsets.zero。
