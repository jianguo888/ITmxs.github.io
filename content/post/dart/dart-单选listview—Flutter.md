---
title: "Dart 单选listview—Flutter"
date: 2021-08-31T09:02:00+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","dart"]
categories: ["flutter","dart"]
---

我试图在我的应用程序中实现`listView`单一选择，以便一旦点击列表中的某个项目，从而使按下的项目颜色状态与其他项目不同。我已经尽力了，但是效果不好。问题在于，即使我的实现在按下时更新了每个项目状态，也不会将其他状态重置为初始状态。
[![expected output](https://i.stack.imgur.com/6c761m.png)](https://i.stack.imgur.com/6c761m.png)

```
class BoxSelection{
  bool isSelected;
  String title;
  String options;
  BoxSelection({this.title, this.isSelected, this.options});
}


class _AddProjectState extends State<AddProject> {
  List<BoxSelection> projectType = new List();
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    projectType
        .add(BoxSelection(title: "Building", isSelected: false, options: "A"));
    projectType
        .add(BoxSelection(title: "Gym House", isSelected: false, options: "B"));
    projectType
        .add(BoxSelection(title: "School", isSelected: false, options: "C"));
  }

  child: ListView.builder(
                      itemCount: projectType.length,
                      itemBuilder: (BuildContext context, int index) {
                        return GestureDetector(
                          onTap: () {
                            setState(() {
       //here am trying to implement single selection for the options in the list but it don't work well
                              for(int i = 0; i < projectType.length; i++) {
                                if (i == index) {
                                  setState(() {
                                    projectType[index].isSelected = true;
                                  });
                                } else {
                                  setState(() {
                                    projectType[index].isSelected = false;
                                  });
                                }
                              }
                            });
                          },
                          child: BoxSelectionButton(
                            isSelected: projectType[index].isSelected,
                            option: projectType[index].options,
                            title: projectType[index].title,
                          ),
                        );
                      },
                    ),
```



**最佳答案**

您的问题是您正在使用`index`来访问`projectType`元素，但是您应该使用`i`

```
if (i == index) {
    setState(() {
    projectType[i].isSelected = true;
    });
} else {
    setState(() {
    projectType[i].isSelected = false;
    });
}  
```

无论如何，我认为您的代码可以改进，因为它没有效率那么高。您要遍历整个列表，并在每次迭代中两次调用`setState`，一次完成一次就不必要地重新创建了小部件树很多次。

将当前选择保存在类级别变量中

```
BoxSelection _selectedBox
```

简化代码，使其直接作用于迭代整个列表的当前选择

```
 onTap: () =>
   setState(() {
     if (_selectedBox != null) {
         _selectedBox.isSelected = false;
     }
     projectType[index].isSelected = !projectType[index].isSelected;
     _selectedBox = projectType[index];
   });   
```
