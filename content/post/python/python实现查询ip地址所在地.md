---
title: "Python实现查询ip地址所在地"
date: 2021-09-05T08:39:32+08:00
draft: false
tags: ["python"]
categories: ["python"]
---

> 本文主要介绍Python实现查询ip地址所在地

免费的淘宝IP地址库RESTAPI接口

https://ip.taobao.com/instructions.html





```dart
#!/usr/bin/env python3.9.2
# -*- coding: utf-8 -*-
# @Time    :  2021-09-05 8:44
# @Author  : Luckly
# @FileName: ip.py
# @Software: PyCharm
# @Blog    ：https://blog.csdn.net/qq_39132095
# -*- coding:gbk -*-
import json

import  requests


#方法
def get_ip_location(ip):
    url=f"http://ip.taobao.com/outGetIpInfo?ip={ip}&accessKey=alibaba-inc"
    r=requests.get(url)
    data=json.loads(r.text)
    country,region,city,isp=(
    data["data"]["country"],
    data["data"]["region"],
    data["data"]["city"],
    data["data"]["isp"],

    )
    return     country,region,city,isp
print(get_ip_location("58.250.137.36"))

```





我相信一定有很多的人跟我一样，经常忘记Python安装的路径，每当用到的时候，最笨的办法就是在全局电脑里，直接查找Python，这样是肯定能查到的，但是如果你的电脑文件超级多，这将是一个工厂量很大的事情，你要等好久的。

便捷的方法时：

打开我们的cmd命令

输入Python

输入 import sys

输入 print(sys.path)

列表中的第五个将是你的安装路径

二.

最快的直接在cmd 里直接

where python 即可