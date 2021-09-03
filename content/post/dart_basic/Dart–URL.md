---
title: "Dart–URL"
date: 2021-09-01T17:07:41+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---



Uri 类支持对用于 URI（您可能称为 URL）的字符串进行编码和解码的函数。这些函数控制 URI 唯一的字符，例如 & 和 =。此类还解析和公开 URI 的组件 — 主机、端口、方案等。

### 编码和解码完全限定的 URI

所述***encodeFull（）***和***decodeFull（）***方法被用于编码和解码的字符除了那些在URI特殊含义（如/，：，＆，＃）。

**例子：** 

```dart
void main(){
	
var uri = 'https://example.org/api?foo=some message';

var encoded = Uri.encodeFull(uri);
assert(encoded ==
	'https://example.org/api?foo=some%20message');

var decoded = Uri.decodeFull(encoded);
print(uri == decoded);
}

```

### 编码和解码 URI 组件

要编码和解码在 URI 中具有特殊含义的所有字符串字符，包括（但不限于）/、& 和 :，请使用***encodeComponent()***和***decodeComponent()***方法。

```dart
void main(){
	
var uri = 'https://example.org/api?foo=some message';

var encoded = Uri.encodeComponent(uri);
assert(encoded ==
	'https%3A%2F%2Fexample.org%2Fapi%3Ffoo%3Dsome%20message');

var decoded = Uri.decodeComponent(encoded);
print(uri == decoded);
}

```

### 解析 URI

如果您有 URI 对象或 URI 字符串，则可以使用 URI 字段（如路径）获取其部分。要从字符串创建 URI，请使用***parse() 静态方法。***

**例子：**

```dart
void main(){
	
var uri =
	Uri.parse('https://example.org:8080/foo/bar#frag');

assert(uri.scheme == 'https');
assert(uri.host == 'example.org');
assert(uri.path == '/foo/bar');
assert(uri.fragment == 'frag');
print(uri.origin == 'https://example.org:8080');
}

```

https://dartpad.cn/?null_safety=true&id

### 构建 URI

您可以使用 Uri() 构造函数从各个部分构建 URI

**例子：**

```dart
void main(){
	
var uri = Uri(
	scheme: 'https',
	host: 'example.org',
	path: '/foo/bar',
	fragment: 'frag');
print(
	uri.toString() == 'https://example.org/foo/bar#frag');
}

```

