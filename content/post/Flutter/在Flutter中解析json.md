---
title: "在Flutter中解析json"
date: 2021-08-15T20:06:29+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

使用 API？响应通常采用 JSON 格式！

![img](https://miro.medium.com/max/700/0*ppa8ABpzkqqLSjmy.png)

图片版权归各自所有者所有

因此，每当您使用 API 时，我们通常会收到 JSON 格式的响应。JSON 格式如下所示：

```
{ 
    "name": "Abhishek", 
    "age": 22, 
    "technology": [ 
        { 
            "name": "Flutter", 
            "experience": 2 
        }, 
        { 
            "name": "GIT", 
            "experience": 2 
        } 
    ] 
}
```

现在解析 JSON 的一种方法是手动解析。但是，当您处理长响应时，它变得困难。

最好使用模型类。所以我们有一个网站，我们只需要在其中提供我们的整个 JSON。您可以在以下位置查看：[https](https://app.quicktype.io/) : [//app.quicktype.io](https://app.quicktype.io/)并选择语言作为`Dart`

对于上面的 JSON，我们将得到以下模型类：

```dart
import 'dart:convert';
Welcome welcomeFromJson(String str) => Welcome.fromJson(json.decode(str));
String welcomeToJson(Welcome data) => json.encode(data.toJson());
class Welcome {
    Welcome({
        this.name,
        this.age,
        this.technology,
    });
String name;
    int age;
    List<Technology> technology;
factory Welcome.fromJson(Map<String, dynamic> json) => Welcome(
        name: json["name"],
        age: json["age"],
        technology: List<Technology>.from(json["technology"].map((x) => Technology.fromJson(x))),
    );
Map<String, dynamic> toJson() => {
        "name": name,
        "age": age,
        "technology": List<dynamic>.from(technology.map((x) => x.toJson())),
    };
}
class Technology {
    Technology({
        this.name,
        this.experience,
    });
String name;
    int experience;
factory Technology.fromJson(Map<String, dynamic> json) => Technology(
        name: json["name"],
        experience: json["experience"],
    );
Map<String, dynamic> toJson() => {
        "name": name,
        "experience": experience,
    };
}
```

所以你可以看到它变得如此简单易用quicktype生成模型类。但是，我们应该总是再添加一件事，即 json.containsKey 以及数据是否为空。所以在添加之后，我们将拥有以下模型类：

```dart
import 'dart:convert';
Welcome welcomeFromJson(String str) => Welcome.fromJson(json.decode(str));
String welcomeToJson(Welcome data) => json.encode(data.toJson());
class Welcome {
    Welcome({
        this.name,
        this.age,
        this.technology,
    });
String name;
    int age;
    List<Technology> technology;
factory Welcome.fromJson(Map<String, dynamic> json) => Welcome(
        name: json.containsKey("name") && json["name"]!=null ? json["name"] : "",
        age: json.containsKey("age") && json["age"] ? json["age"] : 0,
        technology: json.containsKey("technology") && json["technology"] ? List<Technology>.from(json["technology"].map((x) => Technology.fromJson(x))) : [],
    );
Map<String, dynamic> toJson() => {
        "name": name,
        "age": age,
        "technology": List<dynamic>.from(technology.map((x) => x.toJson())),
    };
}
class Technology {
    Technology({
        this.name,
        this.experience,
    });
String name;
    int experience;
factory Technology.fromJson(Map<String, dynamic> json) => Technology(
        name: json.containsKey("name") && json["name"]!=null ? json["name"] : "",
        experience: json.containsKey("experience") && json["experience"]!=null ? json["experience"] : "",
    );
Map<String, dynamic> toJson() => {
        "name": name,
        "experience": experience,
    };
}
```

即使响应错过了某个键或任何值为空，这也会使您的应用程序正常工作。

因此，最好使用 QuickType，因为它使您的开发速度更快。

**注意：**您当然可以使用**Null Safety**来检查 null 条件。例如：

```
name: json.containsKey("name") ? json["name"] ?? "" : "",
```

现在，如果您想解析您的 API 响应，只需将我们的函数调用为：

```
Welcome _data = welcomeFromJson(response.body);
```

访问数据：

```
print(_data.name);
```

希望你喜欢这篇文章！