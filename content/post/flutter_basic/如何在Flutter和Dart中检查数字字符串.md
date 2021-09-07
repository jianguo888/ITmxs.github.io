---
title: "如何在Flutter和Dart中检查数字字符串"
date: 2021-09-07T10:52:34+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

# 如何在 Flutter 和 Dart 中检查数字字符串

数字字符串只是字符串格式的数字。

数字字符串示例：

```dart
'123',
'0.123',
'4.234,345',
'-33.33',
'+44.44'
```

要检查字符串是否为数字字符串，可以使用**double.tryParse()**方法。如果返回值等于**null，**则输入不是数字字符串，否则是。

```dart
if(double.tryParse(String input) == null){
   print('The input is not a numeric string');
} else {
   print('Yes, it is a numeric string');
}
```

**例子**

编码：

```dart
void main(){
  if(double.tryParse('-33.230393399') == null){
    print('False');
  } else {
    print('True');
  }
}
```

输出：

```dart
True
```
