---
title: "Flutter使用“FocusableActionDetector”构建自定义控件"
date: 2021-09-06T15:12:34+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---

在 Flutter 中构建自定义 UI 控件时，很容易只使用 a`GestureDetector`并称其为一天，但这样做将是错误的！特别是如果您打算使用鼠标或键盘等替代输入来支持用户。



事实证明，为各种输入制作一个行为“正确”的 UI 控件比仅仅检测点击要复杂得多。一般来说，您创建的每个控件都需要以下功能：

- 悬停状态
- 焦点状态（用于 Tab 键或箭头键遍历）
- 改变鼠标光标的区域
- [Space] 和 [Enter] 键（或其他键）的键处理程序

传统上，你可以创建一个通过组成部件包括一个大的块`Focus`，`Actions`，`Shortcuts`和`MouseRegion`。这有效，但有很多样板和缩进。幸运的是，Flutter 为此提供了一个专门的小部件：`FocusableActionDetector`.

`FocusableActionDetector`联合收割机 `Focus`，`Actions`，`Shortcuts`和`MouseRegion` 为一体，并且使用相当严重内部扑SDK，包括材料`DatePicker`，`Switch`，`Checkbox`，`Radio`和`Slider`控制。

自己使用它是相当简单的。让我们看看我们如何使用这个小部件构建一个完全自定义的按钮。

***注意：**要遵循代码，请在此处查看要点：[https](https://gist.github.com/esDotDev/04a6301a3858769d4baf5ab1230f7fa2) : [//gist.github.com/esDotDev/04a6301a3858769d4baf5ab1230f7fa2](https://gist.github.com/esDotDev/04a6301a3858769d4baf5ab1230f7fa2)*

## 我的自定义按钮

首先，创建一个`StatefulWidget`可以容纳您`_isFocused`和`_isHovered`状态的对象。我们还将创建一个`FocusNode`我们可以使用它来请求对新闻的关注，这是按钮的常见行为。还要创建一些典型`onPressed`和`label`字段来配置按钮。

```
class MyCustomButton extends StatefulWidget {
  @override
  State<MyCustomButton> createState() => _MyCustomButtonState();
}
class _MyCustomButtonState extends State<MyCustomButton> {
  bool _isHovered = false;
  bool _isFocused = false;
  FocusNode _focusNode = FocusNode();
  @override
  Widget build(BuildContext context) {
     // TODO:
  }
  void _handlePressed() {
    _focusNode.requestFocus();
    widget.onPressed();
  }
}
```

因为我们正在制作一个按钮，所以我们将添加一些基本的配置选项：

```
 const MyCustomButton({Key? key, required this.onPressed, required this.label}) : super(key: key);
  final VoidCallback onPressed;
  final String label;
```

现在剩下的就是填写`build()`方法。

```
Widget build(BuildContext context) {
// Change visuals based on focus/hover state
Color outlineColor = _isFocused ? Colors.black : Colors.transparent;
Color bgColor = _isHovered ? Colors.blue.shade100 : Colors.white;
// Use `GestureDetector` to handle taps
return GestureDetector(
  onTap: _handlePressed,
  // Focus Actionable Detector
  child: FocusableActionDetector(
    focusNode: _focusNode,
    // Set mouse cursor
    mouseCursor: SystemMouseCursors.click,
    // Rebuild with hover/focus changes
    onShowFocusHighlight: (v) => setState(() => _isFocused = v),
    onShowHoverHighlight: (v) => setState(() => _isHovered = v),
    // Button Content
    child: Container(
      padding: const EdgeInsets.all(8),
      child: Text(widget.label),
      decoration: BoxDecoration(
        color: bgColor,
        border: Border.all(color: outlineColor, width: 1),
      ),
    ),
  ),
);
```

在上面的代码中，请注意您仍然需要包装 a`GestureDetector`来检测点击。此外，请注意如何根据悬停/焦点状态更改 ui 的外观。

### 键盘操作

任何控件的最后一步是键盘绑定。**默认情况下，大多数操作系统控件支持 [Space] 和 [Enter] 以“提交”**。您可以通过`ActivateIntent`在`.actions`现场连接内置模块来做到这一点：

```
FocusableActionDetector(
  // Hook up the built-in `ActivateIntent` to submit on [Enter] and [Space]
  actions: {
    ActivateIntent: CallbackAction<Intent>(onInvoke: (_) => _handlePressed()),
  },
  child: ...
),
```

***注意：**您可以创建自己的自定义[操作和意图，](https://api.flutter.dev/flutter/widgets/Action-class.html)但大多数情况下默认`ActivateIntent`就足够了。*

您还可以使用`.shortcuts`参数添加其他键绑定。在这里，我们将 [Ctrl + X] 绑定到相同的`ActivateIntent`. 现在我们的控件将在 [Enter]、[Space] 和 [Ctrl + X] 上提交！

```
FocusableActionDetector(
  // Add 'Ctrl + X' key to the default [Enter] and [Space]
  shortcuts: {
    SingleActivator(LogicalKeyboardKey.keyX, control: true): ActivateIntent(),
  },
  child: ...
),
```

有了它，您就拥有了一个完全可以在桌面、Web 和移动设备上正常工作的控件！



<video controls="" src="https://blog.gskinner.com/wp-content/uploads/2021/06/QkwRw94d3L1.mp4" __idm_id__="202999809" style="box-sizing: border-box; max-width: 100%;"></video>

TAB、ENTER、SPACE、TAPS 都按预期工作！

以下是新自定义按钮的完整代码：

```
class MyCustomButton extends StatefulWidget {
  const MyCustomButton({Key? key, required this.onPressed, required this.label}) : super(key: key);
  final VoidCallback onPressed;
  final String label;
  @override
  State<MyCustomButton> createState() => _MyCustomButtonState();
}
class _MyCustomButtonState extends State<MyCustomButton> {
  bool _isHovered = false;
  bool _isFocused = false;
  FocusNode _focusNode = FocusNode();
  @override
  Widget build(BuildContext context) {
// Change visuals based on focus/hover state
    Color outlineColor = _isFocused ? Colors.black : Colors.transparent;
    Color bgColor = _isHovered ? Colors.blue.shade100 : Colors.white;
    return GestureDetector(
      onTap: _handlePressed,
      child: FocusableActionDetector(
        actions: {
          ActivateIntent: CallbackAction<Intent>(onInvoke: (_) => _handlePressed()),
        },
        shortcuts: {
          SingleActivator(LogicalKeyboardKey.keyX, control: true): ActivateIntent(),
        },
        child: Container(
          padding: const EdgeInsets.all(8),
          child: Text(widget.label),
          decoration: BoxDecoration(
            color: bgColor,
            border: Border.all(color: outlineColor, width: 1),
          ),
        ),
      ),
    );
  }
  void _handlePressed() {
    _focusNode.requestFocus();
    widget.onPressed();
  }
}
```

要查看此运行示例以及自定义复选框示例，请查看此处：[https](https://gist.github.com/esDotDev/04a6301a3858769d4baf5ab1230f7fa2) : [//gist.github.com/esDotDev/04a6301a3858769d4baf5ab1230f7fa2](https://gist.github.com/esDotDev/04a6301a3858769d4baf5ab1230f7fa2)

### **更进一步……**

虽然`FocusableActionDetector`处理了一些样板文件，但对于按钮、复选框、开关等常见用例，它仍然留下了相当多的内容。 具体来说：

- 添加 `GestureDetector`
- 添加`ActivateIntent`映射
- 维护`isHovered`和`isFocused`状态
- 管理提交时的焦点请求

**在我们看来，所有这些常见的行为都可以用某种构建器来处理。所以我们继续做了一个！**

它被称为`FocusableControlBuilder`并在此处作为一个包提供：[https](https://pub.dev/packages/focusable_control_builder) : [//pub.dev/packages/focusable_control_builder](https://pub.dev/packages/focusable_control_builder)

`FocusableControlBuilder`烘焙支持`GestureDetector`，管理`FocusNode`，添加`ActivateIntent`键盘支持并提供一种`builder`方法，该方法可以访问`control.isFocused`和`control.isHovered`用于构建视图。*呼！*

对于大多数用例，这将消除大部分样板文件。按钮、复选框、切换、滑块等都可以基于这个构建器，通过不同的手势或状态来增强它。

如果我们转换前面的例子，它看起来像：

```
class MyCustomButton extends StatelessWidget {
  const MyCustomButton(this.label, {Key? key, required this.onPressed}) : super(key: key);
  final String label;
  final VoidCallback onPressed;
  @override
  Widget build(BuildContext context) {
    return FocusableControlBuilder(
        onPressed: onPressed,
        builder: (_, FocusableControlState control) {
          Color outlineColor = control.isFocused ? Colors.black : Colors.transparent;
          Color bgColor = control.isHovered ? Colors.blue.shade100 : Colors.grey.shade200;
          return Container(
            padding: const EdgeInsets.all(8),
            child: Text(label),
            decoration: BoxDecoration(
              color: bgColor,
              border: Border.all(color: outlineColor, width: 1),
            ),
          );
        });
  }
}
```

不错！我们希望你觉得这个包有用，如果你觉得有用，请在下面告诉我们！
