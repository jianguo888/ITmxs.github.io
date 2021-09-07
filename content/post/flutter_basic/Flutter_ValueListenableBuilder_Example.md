---
title: "Flutter_ValueListenableBuilder_Example"
date: 2021-09-07T08:39:47+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

本文将带您**了解**在多页面（或多屏幕）Flutter 应用程序中使用**ValueListenableBuilder、ValueNotifier**和**ValueListenable**的完整示例

## 快速说明

ValueListenableBuilder 小部件使用构建器回调在 ValueListenable 对象触发其通知时重建，为构建器提供对象的值：

```
ValueListenableBuilder({
  Key? key, 
  required ValueListenable<T> valueListenable, 
  required ValueWidgetBuilder<T> builder, 
  Widget? child
})
```

ValueListenable 是一个公开值的接口，可以由 ValueNotifier 实现。ValueNotifier 的 value 属性是通知器中存储的当前值。

这些词可能令人困惑。为了更清楚，请查看下面的完整示例。

## 应用预览

我们要构建的应用程序是一个任务应用程序。它包含 2 个页面（屏幕）：**HomePage**和**ArchivePage**：

- **主页**显示**未完成的**任务。这个还有一个浮动操作按钮，可用于添加新任务。每个任务旁边都会有一个复选框，用于将任务标记为已完成。您可以按“查看已完成按钮”导航到**ArchivePage**。
- **ArchivePage**显示**已完成的**任务。每个任务旁边都有一个图标按钮，用于将该任务置于“未完成”状态。

一个演示胜过千言万语：

<video class="" autoplay="" controls="" loop="" muted="" data-lazy-type="video" data-src="https://www.kindacode.com/wp-content/uploads/2021/07/Flutter-ValueListenable.mp4" src="https://www.kindacode.com/wp-content/uploads/2021/07/Flutter-ValueListenable.mp4" __idm_id__="260825089" style="margin: 0px; padding: 0px; box-sizing: border-box; width: 720px;"></video>

## 编码

创建一个新的 Flutter 项目并添加 2 个新文件：**home_screen.dart**和**archive_screen.dart**。这是目录结构：

```dart
.
├── archive_page.dart
├── home_page.dart
└── main.dart
```

1. 删除**main.dart 中的**所有默认代码并添加以下内容：

```dart
// main.dart
import 'package:flutter/material.dart';

import './home_page.dart';

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
          primarySwatch: Colors.blue,
        ),
        home: HomePage());
  }
}
```



2. HomePage的最终代码：

```dart
// home_page.dart
import 'package:flutter/material.dart';

import './archive_page.dart';

// This screen only displays un-completed tasks
class HomePage extends StatelessWidget {
  // Using "static" so that we can easily access it from other screens
  static final ValueNotifier<List<Map<String, dynamic>>> tasksNotifier =
      ValueNotifier([]);

  // This function will be triggered when the floating button is pressed
  // Add new task
  void _addNewTask() {
    final List<Map<String, dynamic>> tasks = [...tasksNotifier.value];
    tasks.add({
      "id": DateTime.now().toString(),
      "title": "Task ${DateTime.now()}",
      "isDone": false
    });
    tasksNotifier.value = tasks;
  }

  // This function will be triggered when the checkbox next to a task is tapped
  // Finish a task
  // Change a task from "uncompleted" to "completed"
  void _finishTask(String updatedTaskId) {
    final List<Map<String, dynamic>> tasks = [...tasksNotifier.value];

    final int index = tasks.indexWhere((task) => task['id'] == updatedTaskId);
    tasks[index]['isDone'] = true;

    tasksNotifier.value = tasks;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Kindacode.com'),
      ),
      body: ValueListenableBuilder<List<Map<String, dynamic>>>(
        valueListenable: HomePage.tasksNotifier,
        builder: (_, tasks, __) {
          final uncompletedTasks =
              tasks.where((task) => task['isDone'] == false).toList();

          return Padding(
            padding: const EdgeInsets.all(20),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                ElevatedButton(
                    onPressed: () {
                      Navigator.of(context).push(MaterialPageRoute(
                        builder: (BuildContext context) => ArchivePage(),
                      ));
                    },
                    child: Text('View Completed Tasks')),
                SizedBox(
                  height: 20,
                ),
                Text(
                  'You have ${uncompletedTasks.length} uncompleted tasks',
                  style: TextStyle(fontSize: 18),
                ),
                SizedBox(
                  height: 10,
                ),
                Expanded(
                  child: ListView.builder(
                    itemCount: uncompletedTasks.length,
                    itemBuilder: (_, index) => Card(
                        margin: EdgeInsets.symmetric(vertical: 15),
                        elevation: 5,
                        color: Colors.amberAccent,
                        child: ListTile(
                          title: Text(uncompletedTasks[index]['title']),
                          trailing: IconButton(
                            icon: Icon(Icons.check_box_outline_blank),
                            onPressed: () =>
                                _finishTask(uncompletedTasks[index]['id']),
                          ),
                        )),
                  ),
                ),
              ],
            ),
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.add),
        onPressed: _addNewTask,
      ),
    );
  }
}
```

3. ArchivePage 的最终代码：

```dart
// archive_page.dart
import 'package:flutter/material.dart';

import './home_page.dart';

// This screen only display completed tasks
class ArchivePage extends StatelessWidget {
  // Change a task from "completed" to "uncompleted"
  void _uncheckTask(String updatedTaskId) {
    final List<Map<String, dynamic>> tasks = [...HomePage.tasksNotifier.value];

    final int index = tasks.indexWhere((task) => task['id'] == updatedTaskId);
    tasks[index]['isDone'] = false;

    HomePage.tasksNotifier.value = tasks;
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text('Archive Screen'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(20),
        child: ValueListenableBuilder<List<Map<String, dynamic>>>(
          valueListenable: HomePage.tasksNotifier,
          builder: (_, tasks, __) {
            final completedTasks =
                tasks.where((task) => task['isDone'] == true).toList();
            return ListView.builder(
              itemCount: completedTasks.length,
              itemBuilder: (_, index) => Card(
                  margin: EdgeInsets.symmetric(vertical: 15),
                  elevation: 5,
                  color: Colors.pinkAccent,
                  child: ListTile(
                    title: Text(completedTasks[index]['title']),
                    trailing: IconButton(
                      icon: Icon(Icons.check_box),
                      onPressed: () =>
                          _uncheckTask(completedTasks[index]['id']),
                    ),
                  )),
            );
          },
        ),
      ),
    );
  }
}
```

现在运行你的项目并尝试一下看看会发生什么。

## 结论

我们已经介绍了在 Flutter 中实现 ValueListenableBuilder 小部件的端到端示例。
