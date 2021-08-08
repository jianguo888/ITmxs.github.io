---
title: "Textfiled使用"
date: 2021-08-08T19:25:26+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

# Flutter手把手教程UI布局和Widget——TextField使用、搭配InputDecoration和FocusedNode使用

### TextField

TextField是一个常用的控件，同时它也是一个组合控件，由多个控件组合而成。
这是来自Material官方网站的的图片
[![2021_01_07_material_textfield](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2021_01_07_material_textfiled.png)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2021_01_07_material_textfiled.png)
TextField是由7个控件组成，其中有些控件默认不显示，我们可以对各个控件单独设置想要的样式来满足不同的UI展示需求。
下面我们就来列举几种常见的样式：

**1. 简单的TextField**

```
DART
TextField(
    decoration: InputDecoration(
        labelText: "最基本的的TextField",
    ),
)
```

`TextField`接收一个`InputDecoration`作为参数，`InputDecoration`初始化的参数`labelText`可以帮助我们定义placeholder。`labelText`模式会灰色的，选中之后会变为蓝色，并且`TextField`底部会有一条蓝色线条。
[![2020_01_08_textfield_custom](https://luckly007.oss-cn-beijing.aliyuncs.com/img/2020_01_08_textfield_custom.gif)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_custom.gif)

**2. 限制字符的长度**

```
DART
TextField(
    maxLength: 10,
    decoration: InputDecoration(
        labelText: "最多10个字符",
    ),
)
```

`maxLength`可以设置最长字符个数，如果超过这个限制再次输入不会有显示，并且在`TextField`在有右下角有当前字符个数的标记，此处是`10/10`。
[![2020_01_08_textfield_maxlength](https://luckly007.oss-cn-beijing.aliyuncs.com/img/2020_01_08_textfield_maxlength.png)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_maxlength.png)

**3. 限制行数**

```
DART
TextField(
    maxLines: 2,
    decoration: InputDecoration(
        labelText: "两行文字，超出的文字上翻",
    ),
)
```

`maxLines`参数可以设置行数，比如这里设置的是2，默认只会显示两行，超过两行的部分只能通过上下滚动来显示。
[![2020_01_08_textfield_maxline](https://luckly007.oss-cn-beijing.aliyuncs.com/img/2020_01_08_textfield_maxline.png)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_maxline.png)

> 默认行数是1，超过的部分会往左缩进。

**4. labelText设置颜色**

```
DART
TextField(
    decoration: InputDecoration(
        labelText: "labelText 有颜色",
        labelStyle: TextStyle(color: Colors.red),
    ),
)
```

`InputDecoration`可以设置`labelStyle`参数，接收一个`TextStyle`对象，`TextStyle`这个我们比较熟悉，在之前讲解`Text`的文章中已经做了很多详解了。设置颜色之后，当点击`TextField`之后，文字会变小，颜色也是设置好的颜色。
[![2020_01_08_textfield_labelcolor](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_labelcolor.png)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_labelcolor.png)

**5. 左侧Icon**

```
DART
TextField(
    decoration: InputDecoration(
        icon: Icon(Icons.account_box),
        labelText: "左侧有一个Icon",
    ),
)
```

`icon`参数可以传入一个`Icon`对象用来显示在`TextField`的左侧，我们可以传入各式各样的`Icon`，满足我们更丰富的展示需求。
[![2020_01_08_textfield_lefticon](https://luckly007.oss-cn-beijing.aliyuncs.com/img/2020_01_08_textfield_lefticon.png)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_lefticon.png)

**6. 右侧Icon suffix和suffixIcon**

```
DART
TextField(
    decoration: InputDecoration(
        labelText: "右侧的两个Icon suffix 和 suffixIcon",
        suffix: Icon(Icons.account_box),
        suffixIcon: Icon(Icons.add),
    ),
)
```

`suffixIcon`默认是显示在右侧的，`TextField`被点击之后会显示为被选中状态，`suffix`默认不显示，只有当选中`TextField`的时候才会显示出来。
[![2020_01_08_textfield_suffix](https://luckly007.oss-cn-beijing.aliyuncs.com/img/2020_01_08_textfield_suffix.gif)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_suffix.gif)

**7. 辅助提示**

```
DART
TextField(
    decoration: InputDecoration(
        labelText: "下方带有辅助提示的TextField",
        helperText: "我是辅助提示",
        helperStyle: TextStyle(color: Colors.red),
    ),
)
```

`helperText`可以帮助我们在`TextField`下面显示一行提示文字，同样我们也可以使用`helperStyle`来设置这段提示文字的样式。
[![2020_01_08_textfield_helpertext](https://luckly007.oss-cn-beijing.aliyuncs.com/img/2020_01_08_textfield_helpertext.jpeg)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_helpertext.jpeg)

**8. 点击后的提示 hintText**

```
DART
TextField(
    decoration: InputDecoration(
        labelText: "点击后会有提示",
        hintText: "我是点击后的提示",
        hintStyle: TextStyle(color: Colors.red),
    ),
)
```

`hintText`参数可以帮助我们设置一个点击后显示的文字，只有点击之后才可以显示，同样我们可以通过`hintStyle`来设置`hintText`的样式。
[![2020_01_08_textfield_hittext](https://luckly007.oss-cn-beijing.aliyuncs.com/img/2020_01_08_textfield_hittext.gif)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_hittext.gif)

**9. 不显示下划线**

```
DART
TextField(
    decoration: InputDecoration(
        labelText: "选中时没有下划线",
        focusedBorder: InputBorder.none,
    ),
)
```

`focusedBorder`可以帮助我们设置下划线的样式，如果传入`InputBorder.none`则不会显示下划线。
[![2020_01_08_textfield_focusborder](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_focusborder.gif)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_focusborder.gif)

**10. 自定义下划线样式**

```
DART
TextField(
    decoration: InputDecoration(
        labelText: "选中时的下划线颜色",
        focusedBorder: UnderlineInputBorder(
            borderSide: BorderSide(color: Colors.red),
        ),
    ),
)
```

我们可以给`focusedBorder`传入自定义的`UnderlineInputBorder`来自定义下划线的样式，这里我们简单做了个颜色的改变。
[![2020_01_08_textfield_focusborder_color](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_focusborder_color.gif)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_focusborder_color.gif)

### TextField事件监听

日常开发中，我们往往希望在三个地方TextField可以给我们回调。

1. 输入文字的过程中，这样方便我们在用户输入的时候就可以判断输入内容是否合法。
2. 输入完成的时候，这个时候我们可以拿到输入内容做一些操作。
3. 与键盘事件的配合，在必要的时候回收键盘。

`TextField`提供了三个回调方法

- `onChanged` 此方法是在输入有变化的时候就会回调。参数是当前已经输入的内容
- `onSubmitted` 此方法是在我们输入完成后，点击键盘上回车的时候回调。参数是当前的已经输入的内容
- `onEditingComplete`此方法也是在点击键盘上回车的时候回调，它会在`onSubmitted`之前执行。不会带有参数

需要注意是`onEditingComplete`回调方法没有携带参数。如果我们需要在`onEditingComplete`方法中获取到当前的输入值。
那就需要通过`TextEditingController`来捕捉输入内容，`TextField`接收一个`TextEditingController`对象来作为`controller`参数，
通过`TextEditingController`的属性`text`我们也可以获取到当前的输入内容。

**11. 事件回调**

```
DART
TextEditingController controller = TextEditingController();
TextField(
    controller: controller,
    onChanged: (value) {
      print("onChanged value = " + value);
    },
    onSubmitted: (value) {
      print("onSubmitted value = " + value);
    },
    onEditingComplete: () {
      print("onEditingComplete value = " + controller.text);
    },
    decoration: InputDecoration(
      labelText: "输入事件监听",
    ),
)
```

可以看到通过`controller.text`可以获取到当前的输入内容。
**12. 键盘回收**

```
DART
TextField(
    decoration: InputDecoration(
        labelText: "键盘回收",
        suffixIcon: IconButton(
            icon: Icon(Icons.close),
            onPressed: () {
                FocusScope.of(context).requestFocus(FocusNode());
            }),
        ),
    )
```

`FocusNode`可以帮助我们进行键盘的回收，我只需要将`FocusScope`的`requestFocus`方法中传入一个新的`FocusNode`对象即刻。
如果在开发过程中，我们希望通过点击页面上某个按钮来结束`TextField`输入并且获取到当前的输入内容。使用`FocusNode`是很有效的。
[![2020_01_08_textfield_focusbnode](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_focusbnode.gif)](https://cdn.jsdelivr.net/gh/johnson8888/blog_pages/images/2020_01_08_textfield_focusbnode.gif)