---
title: "Python实现聊天轰炸机器人"
date: 2021-09-05T10:52:53+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->



第一步安装python环境

第二步

cmd命令下输入

```
C:\Users\Luckly>python
Python 3.9.2 (tags/v3.9.2:1a79785, Feb 19 2021, 13:44:55) [MSC v.1928 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import pyautogui
>>> pyautogui.position()

```



会打印当前鼠标的位置



```python
#!/usr/bin/env python3.9.2
# -*- coding: utf-8 -*-
# @Time    :  2021-09-05 10:35
# @Author  : Luckly
# @FileName: auto_send_message.py
# @Software: PyCharm
# @Blog    ：https://blog.csdn.net/qq_39132095
import  pyautogui
import  pyperclip
import  time

from itchat import content
content ="""
数据存储到方式
半结构化json text 文本
本地格式化： csv，excel
远程服务器 mysql数据库
"""

time.sleep(5)
for line in list(content.split("\n"))*100:
    if line:
        print(line)
        pyautogui.click(445,624)
        pyperclip.copy(line)
        pyautogui.hotkey("ctrl","v")
        pyautogui.typewrite("\n")
        time.sleep(1)

```





python自动写文档

```python
#!/usr/bin/env python3.9.2
# -*- coding: utf-8 -*-
# @Time    :  2021-09-05 10:35
# @Author  : Luckly
# @FileName: auto_send_message.py
# @Software: PyCharm
# @Blog    ：https://blog.csdn.net/qq_39132095
import  pyautogui
import  pyperclip
import  time

from itchat import content
content ="""
我写了个脚本
马上就会轰炸你
3
2
1
哈哈
"""

time.sleep(5)
pyautogui.click(445, 624)
for line in content:
    if line:
        print(line)

        pyperclip.copy(line)   ##复制剪切版
        pyautogui.hotkey("ctrl","v")

        # pyautogui.typewrite("\n")
        time.sleep(1)

```

