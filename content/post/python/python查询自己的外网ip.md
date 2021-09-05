---
title: "Python查询自己的外网ip"
date: 2021-09-05T09:27:39+08:00
draft: false
tags: ["python"]
categories: ["python"]
---

> 本文主要介绍

<!--more-->

 这个也可以哦

http://icanhazip.com/



```python
#!/usr/bin/env python3.9.2
# -*- coding: utf-8 -*-
# @Time    :  2021-09-05 9:21
# @Author  : Luckly
# @FileName: ipwai.py
# @Software: PyCharm
# @Blog    ：https://blog.csdn.net/qq_39132095
import json

import requests
url="http://httpbin.org./ip"
r=requests.get(url)

ip=json.loads(r.text)["origin"]
print(r.text)
print("我的外网ip：",ip)
```

