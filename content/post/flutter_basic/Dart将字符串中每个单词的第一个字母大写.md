---
title: "Dart将字符串中每个单词的第一个字母大写"
date: 2021-09-07T11:14:05+08:00
draft: true
---

# Dart：将字符串中每个单词的第一个字母大写

下面的示例向您展示了如何在 Dart（以及 Flutter）中将字符串中每个单词的第一个字母大写。

**编码：**

```dart
void main() {
  String totTitle(String input) {
    final List<String> splitStr = input.split(' ');
    for (int i = 0; i < splitStr.length; i++) {
      splitStr[i] =
          '${splitStr[i][0].toUpperCase()}${splitStr[i].substring(1)}';
    }
    final output = splitStr.join(' ');
    return output;
  }

  final str1 = 'Kindacode.com is a website about programming.';
  final str2 = 'Today is a raining day!';
  print(totTitle(str1));
  print(totTitle(str2));
}
```

**输出：**

```dart
Kindacode.com Is A Website About Programming.
Today Is A Raining Day!
```

