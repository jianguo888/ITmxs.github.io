---
title: "Flutter检测list_map是否为空"
date: 2021-08-18T11:32:44+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---





```
/// 字符串不为空
bool strNoEmpty(String value) {
  if (value == null) return false;

  return value.trim().isNotEmpty;
}

/// 字符串不为空
bool mapNoEmpty(Map value) {
  if (value == null) return false;
  return value.isNotEmpty;
}

///判断List是否为空
bool listNoEmpty(List list) {
  if (list == null) return false;

  if (list.length == 0) return false;

  return true;
}

```

