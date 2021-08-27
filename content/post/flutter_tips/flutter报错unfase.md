---
title: "Flutter报错unfase"
date: 2021-08-27T14:37:27+08:00
draft: true
---

flutter:报错之： Looking up a deactivated widget’s ancestor is unsafe

# 我理解的错误原因

找不到靠谱的上下文，也就是我所传递的上下文无效了。

### 1.出现错误的地方

```
 void countdownAlertTime(BuildContext context) {
      print("进入倒计时");
    new Timer(new Duration(seconds: 2), () {
      print("进入倒计时回调中");
      Navigator.of(context).pop();  //就是这句出的问题
      print("第一次页面pop");
      showDialog(
          context: context,
          builder: (context) {
            return new AlertDialog(
                title: new Text("恭喜"),
                content: new Text("操作成功!"),
                actions: <Widget>[
                  new FlatButton(
                    child: new Text("确定"),
                    onPressed: () {
                      Navigator.of(context).pop();
                    },
                  )
                ]);
          });
          setState(() {
        _alertTime = _tmpAlertTime;
      });
    });
  }
```

### 2.countdownAlertTime()在此处被调用

![在这里插入图片描述](https://luckly007.oss-cn-beijing.aliyuncs.com/image/20200423225142265.png)

### 3.showInputDialog()在此处被调用

![在这里插入图片描述](https://luckly007.oss-cn-beijing.aliyuncs.com/image/20200423225339577.png)

### 分析 纯自己理解

C =>B=>A
C被B调用，B被A调用，其中的context (BuildContext对象) 已经指代不明确了。就是报错的意思，上下文有丢失的意思。

## 解决方案。

在A调用时 传递 this.context 能正确指代 最初的context上下文。
![在这里插入图片描述](https://luckly007.oss-cn-beijing.aliyuncs.com/image/20200423225648121.png)

## 结语

好了 继续去撸代码了。



# 看第二个demo

```dart
show(BuildContext context){

    var dialog = Dialog(
      child: Container(
        margin: EdgeInsets.all(8.0),
        child: Form(
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: <Widget>[
              TextFormField(
                decoration: InputDecoration(
                    labelText: "Insira o número de telefone",
                    border: OutlineInputBorder(
                        borderRadius: BorderRadius.all(Radius.circular(2.0)))),
              ),
              Row(
                mainAxisAlignment: MainAxisAlignment.end,
                children: <Widget>[
                  FlatButton(
                      onPressed: () {
                        Navigator.of(context).pop();
                      },
                      child: Text("Cancelar")),
                  FlatButton(
                      onPressed: () {
                        Navigator.of(context).pop();
                      },
                      child: Text("Aceitar"))
                ],
              )
            ],
          ),
        ),
      ),
    );

    showDialog(context: context,builder: (context){
      return dialog;
    });
  }
```

这是我的代码。

```dart
I/flutter (31032): Looking up a deactivated widget's ancestor is unsafe.
I/flutter (31032): At this point the state of the widget's element tree is no longer stable. To safely refer to a
I/flutter (31032): widget's ancestor in its dispose() method, save a reference to the ancestor by calling
I/flutter (31032): inheritFromWidgetOfExactType() in the widget's didChangeDependencies() method.
I/flutter (31032): 
```

声明一个全局变量

```dart
    final GlobalKey<ScaffoldState> _scaffoldKey = GlobalKey<ScaffoldState>();
```

然后在您的小部件构建的脚手架上注册key，例如

```dart
    @override
    Widget build(BuildContext context) {
     return Scaffold(
       key: _scaffoldKey,
       ...
```

然后在对话框上

```dart
show(BuildContext context){

var dialog = Dialog(
  child: Container(
    margin: EdgeInsets.all(8.0),
    child: Form(
      child: Column(
        mainAxisSize: MainAxisSize.min,
        children: <Widget>[
          TextFormField(
            decoration: InputDecoration(
                labelText: "Insira o número de telefone",
                border: OutlineInputBorder(
                    borderRadius: BorderRadius.all(Radius.circular(2.0)))),
          ),
          Row(
            mainAxisAlignment: MainAxisAlignment.end,
            children: <Widget>[
              FlatButton(
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                  child: Text("Cancelar")),
              FlatButton(
                  onPressed: () {
                    Navigator.of(context).pop();
                  },
                  child: Text("Aceitar"))
            ],
          )
        ],
      ),
    ),
  ),
);
```

将该脚手架上下文传递给 showDialog 方法

```dart
showDialog(context: _scaffoldKey.currentContext ,builder: (context){
  return dialog;
 });
}
```



# 看第三个demo

为对话框提供不同的上下文名称

```dart
 showDialog(context: context,builder: (dialogContex){
              return Dialog(
                child: Container(
                  margin: EdgeInsets.all(8.0),
                  child: Form(
                    child: Column(
                      mainAxisSize: MainAxisSize.min,
                      children: <Widget>[
                        TextFormField(
                          decoration: InputDecoration(
                              labelText: "Insira o número de telefone",
                              border: OutlineInputBorder(
                                  borderRadius: BorderRadius.all(Radius.circular(2.0)))),
                        ),
                        Row(
                          mainAxisAlignment: MainAxisAlignment.end,
                          children: <Widget>[
                            FlatButton(
                                onPressed: () {
                                  Navigator.of(dialogContex).pop();
                                },
                                child: Text("Cancelar")),
                            FlatButton(
                                onPressed: () {
                                  Navigator.of(context).pop();
                                },
                                child: Text("Aceitar"))
                          ],
                        )
                      ],
                    ),
                  ),
                ),
              );
            });
```

# 看第四个demo

我在尝试打开对话框时遇到了同样的错误，我在这里找到了解决方案：[github flutter issues](https://github.com/flutter/flutter/issues/19462#issuecomment-478284020)。具体来说，我遵循了海报的建议，即创建一个`GlobalKey`并将其与`Scaffold`小部件关联，并在创建对话框时使用该键的上下文。就我而言，我有一个全局可访问的对象，其中包含`GlobalKey`：

```dart
MyGlobals myGlobals = MyGlobals();
class MyGlobals {
  GlobalKey _scaffoldKey;
  MyGlobals() {
    _scaffoldKey = GlobalKey();
  }
  GlobalKey get scaffoldKey => _scaffoldKey;
}
```

在`Scaffold`小部件构造函数调用中：

```dart
Scaffold(
  appBar: ...,
  body: ...,
  drawer: ...,
  key: myGlobals.scaffoldKey,
)
```

在`showDialog`通话中：

```dart
showDialog<String>(
  barrierDismissible: ...,
  builder: ...,
  context: myGlobals.scaffoldKey.currentContext,
);
```

# 看第五个demo

这个：

```dart
Navigator.of(context,rootNavigator: true).pop();
```

代替

```dart
Navigator.of(context).pop();
```



这多么方案，有你想要的吗？
