---
title: "Flutter带有多个尾随图标按钮的ListTitle"
date: 2021-09-16T15:06:57+08:00
draft: true
---

在某些情况下，您可能希望向 ListTIle 小部件添加多个尾随图标按钮。许多 Flutter 开发人员会在几秒钟内得到解决方案：使用 Row 小部件并在其中放置一些图标按钮，如下所示：

```dart
ListTile(
            title: Text('The Title'),
            subtitle: Text('The Subtitle'),
            trailing: Row(
              children: [
                IconButton(onPressed: () {}, icon: Icon(Icons.favorite)),
                IconButton(onPressed: () {}, icon: Icon(Icons.edit)),
                IconButton(onPressed: () {}, icon: Icon(Icons.delete)),
              ],
            ),
          )
```

如果这样做，您可能会遇到以下错误：

```dart
══╡ EXCEPTION CAUGHT BY RENDERING LIBRARY ╞═══
The following assertion was thrown during performLayout():
Trailing widget consumes entire tile width. Please use a sized widget, or consider replacing
ListTile with a custom widget
```

**为了解决这个问题，我们有不止一个选择：**

1.将Row 小部件的**mainAxisSize**属性设置为**MainAxisSize.min**：

```dart
ListTile(
            title: Text('The Title'),
            subtitle: Text('The Subtitle'),
            trailing: Row(
              mainAxisSize: MainAxisSize.min,
              children: [
                IconButton(onPressed: () {}, icon: Icon(Icons.favorite)),
                IconButton(onPressed: () {}, icon: Icon(Icons.edit)),
                IconButton(onPressed: () {}, icon: Icon(Icons.delete)),
              ],
            ),
          )
```

它按预期工作：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/Screen-Shot-2021-07-29-at-04.27.21.jpg)



2.第二种选择是将 Row 小部件包装在一个 SizedBox 或一个固定宽度的 Container 小部件中，如下所示：



```dart
 ListTile(
            title: Text('The Title'),
            subtitle: Text('The Subtitle'),
            trailing: SizedBox(
              width: 150,
              child: Row(
                children: [
                  IconButton(onPressed: () {}, icon: Icon(Icons.favorite)),
                  IconButton(onPressed: () {}, icon: Icon(Icons.edit)),
                  IconButton(onPressed: () {}, icon: Icon(Icons.delete)),
                ],
              ),
            ),
          )
```

它也工作得很好。
