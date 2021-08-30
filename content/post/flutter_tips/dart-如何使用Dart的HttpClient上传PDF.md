---
title: "Dart 如何使用Dart的HttpClient上传PDF"
date: 2021-08-30T21:14:06+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

我需要将PDF文件发布到远程REST API，但我终生无法发现它。无论我做什么，服务器都会响应我尚未将对象与`file`参数关联。假设我有一个名为`test.pdf`的PDF。到目前为止，这是我一直在做的事情:



```dart
// Using an HttpClientRequest named req

req.headers.contentType = new ContentType('application', 'x-www-form-urlencoded');
StringBuffer sb = new StringBuffer();
String fileData = new File('Test.pdf').readAsStringSync();
sb.write('file=$fileData');
req.write(sb.toString());
return req.close();
```


到目前为止，我已经尝试将`write()`编码到请求的数据进行每种组合和编码，但都无济于事。我尝试将其发送为`codeUnits`，尝试使用`UTF8.encode`进行编码，尝试使用`Latin1Codec`进行编码，所有操作都已完成。我很困惑

任何帮助将不胜感激。



**最佳答案**

您可以使用[MultipartRequest](https://api.dartlang.org/apidocs/channels/stable/#http/http.MultipartRequest)中的[http package](http://pub.dartlang.org/packages/http):



```dart
var uri = Uri.parse("http://pub.dartlang.org/packages/create");
var request = new http.MultipartRequest("POST", url);
request.fields['user'] = 'john@doe.com';
request.files.add(new http.MultipartFile.fromFile(
    'package',
    new File('build/package.tar.gz'),
    contentType: new ContentType('application', 'x-tar'));
request.send().then((response) {
  if (response.statusCode == 200) print("Uploaded!");
});
```