---
title: "Flutter_DropdownButton示例"
date: 2021-09-23T15:50:43+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-09-22-at-15.52.22.jpg)

本文将引导您完成在 Flutter中实现**DropdownButton**小部件的端到端完整示例。



## 概述

### 快速说明

当 DropdownButton 小部件被按下时，将显示一个包含项目的菜单，让用户从中选择一个项目。当菜单消失时，所选项目将显示在按钮中。广告



DropdownButton 的项目是通过使用**DropdownMenuItem**小部件列表实现的：

```
DropdownButton<T>(
  value: T?,
  items: List<DropdownMenuItem<T>>?,
)
```

的类型的**值**，并专用于各类型**DropdownMenuItem**必须是相同的。

### 设置下拉按钮的宽度

您可以通过将其**isExpanded**参数设置为**true**并将其包装在固定大小的 Container 或 SizedBox 中来设置 DropdownButton 的宽度。

### 隐藏下划线

下面是删除 DropdownButton 下划线的两种不同方法。

1. 将**underline**参数设置为空**Container**：

```
underline: Container()
```

2. 将 DropdownButton 小部件放在**DropdownButtonHideUnderline**小部件中：

```
DropdownButtonHideUnderline(
            child: DropdownButton(/* ... */),
)
```



您可以在[文档中](https://api.flutter.dev/flutter/material/DropdownButton-class.html)找到有关小部件的更多信息。

文字可能很无聊和令人困惑。下面的例子将帮助你更好地理解。

## 这个例子

### 预览

我们将要制作的演示应用程序包含一个带有白色提示文本的圆形下拉按钮。它的宽度是 300px，它的下划线是不可见的。

下拉菜单为琥珀色，文本为深色。选定的项目将有一个自定义的文本样式，这要归功于**selectedItemBuilder**。



<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-Dropdown-Button-Example.mp4" playsinline="" src="https://www.kindacode.com/wp-content/uploads/2021/09/Flutter-Dropdown-Button-Example.mp4" __idm_id__="467133441" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

### 完整代码

**main.dart 中**的完整源代码和解释：

```
// main.dart
import 'package:flutter/material.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
        // Remove the debug banner
        debugShowCheckedModeBanner: false,
        title: 'Kindacode.com',
        theme: ThemeData(
          primarySwatch: Colors.indigo,
        ),
        home: HomePage());
  }
}

class HomePage extends StatefulWidget {
  const HomePage({Key? key}) : super(key: key);

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage> {
  List<String> _animals = ["Dog", "Cat", "Crocodile", "Dragon"];

  String? _selectedColor;

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: Center(
        child: Container(
          width: 300,
          padding: EdgeInsets.symmetric(vertical: 5, horizontal: 15),
          decoration: BoxDecoration(
              color: Theme.of(context).primaryColor,
              borderRadius: BorderRadius.circular(30)),
          child: DropdownButton<String>(
            onChanged: (value) {
              setState(() {
                _selectedColor = value;
              });
            },
            value: _selectedColor,

            // Hide the default underline
            underline: Container(),
            hint: Center(
                child: Text(
              'Select the aniaml you love',
              style: TextStyle(color: Colors.white),
            )),
            icon: Icon(
              Icons.arrow_downward,
              color: Colors.yellow,
            ),
            isExpanded: true,

            // The list of options
            items: _animals
                .map((e) => DropdownMenuItem(
                      child: Container(
                        alignment: Alignment.centerLeft,
                        child: Text(
                          e,
                          style: TextStyle(fontSize: 18),
                        ),
                      ),
                      value: e,
                    ))
                .toList(),

            // Customize the selected item
            selectedItemBuilder: (BuildContext context) => _animals
                .map((e) => Center(
                      child: Text(
                        e,
                        style: TextStyle(
                            fontSize: 18,
                            color: Colors.amber,
                            fontStyle: FontStyle.italic,
                            fontWeight: FontWeight.bold),
                      ),
                    ))
                .toList(),
          ),
        ),
      ),
    );
  }
}
```

## 结论



我们已经探索了Flutter 中**DropdownButton**和**DropdownMenuItem**小部件的基础知识
