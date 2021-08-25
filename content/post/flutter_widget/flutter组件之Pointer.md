---
title: "Flutter组件之Pointer"
date: 2021-08-24T17:06:29+08:00
draft: true
---



### AbsorbPointer、IgnorePointer

当时想解决穿透暗色背景，和背景后面的控件互动的时候，我几乎立马想到这俩个控件，先了解下这俩个控件吧

- AbsorbPointer
  - 阻止子树接收指针事件，`AbsorbPointer`本身可以响应事件，消耗掉事件
  - `absorbing` 属性（默认true）
    - true：拦截向子Widget传递的事件   false：不拦截

```dart
AbsorbPointer(
    absorbing: true,
    child: Listener(
        onPointerDown: (event){
            print('+++++++++++++++++++++++++++++++++');
        },
    )
)
复制代码
```

- IgnorePointer

  - 阻止子树接收指针事件，`IgnorePointer`本身无法响应事件，其下的控件可以接收到点击事件（父控件）

  - ```
    ignoring
    ```

     属性（默认true）

    - true：拦截向子Widget传递的事件   false：不拦截

```dart
IgnorePointer(
    ignoring: true,
    child: Listener(
        onPointerDown: (event){
            print('----------------------------------');
        },
    )
)
复制代码
```

**分析**

- 这里来分析下，首先`AbsorbPointer`这个控件是不合适的，因为`AbsorbPointer`本身会消费触摸事件，事件被`AbsorbPointer`消费掉，会导致背景后的页面无法获取到触摸事件；`IgnorePointer`本身无法消费触摸事件，又由于`IgnorePointer`和`AbsorbPointer`都具有屏蔽子Widget获取触摸事件的作用，这个貌似靠谱，这里试了，可以和背景后面的页面互动！但是又存在一个十分坑的问题
- 因为使用`IgnorePointer`屏蔽子控件的触摸事件，而`IgnorePointer`本身又不消耗触摸事件，会导致无法获取到背景的点击事件！这样点击背景会无法关闭dialog弹窗，只能手动关闭dialog；各种尝试，实在没办法获取到背景的触摸事件，此种穿透背景的方案只能放弃

### Listener、behavior

这种方案，成功实现想要的穿透效果，这里了解下`behavior`的几种属性

- deferToChild：仅当一个孩子被命中测试击中时，屈服于其孩子的目标才会在其范围内接收事件
- opaque：不透明目标可能会受到命中测试的打击，导致它们既在其范围内接收事件，又在视觉上阻止位于其后方的目标也接收事件
- translucent：半透明目标既可以接收其范围内的事件，也可以在视觉上允许目标后面的目标也接收事件

有戏了！很明显translucent是有希望的，尝试了几次，然后成功实现了想要的效果

注意，这边有几个坑点，提一下

- 务必使用`Listener`控件来使用behavior属性，使用GestureDetector中behavior属性会存在一个问题，一般来说：都是Stack控件里面的Children，里面有俩个控件，分上下层，在此处，GestureDetector设置behavior属性，俩个GestureDetector控件上下叠加，会导致下层GestureDetector获取不到触摸事件，很奇怪；使用`Listener`不会产生此问题
- 我们的背景使用`Container`控件，我这里设置了`Colors.transparent`，直接会导致下层接受不到触摸事件，color为空才能使下层控件接受到触摸事件，此处不要设置color即可

下面是写的一个验证小示例

```dart
class TestLayoutPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return _buildBg(children: [
      //下层
      Listener(
        onPointerDown: (event) {
          print('下层蓝色区域++++++++');
        },
        child: Container(
          height: 300,
          width: 300,
          color: Colors.blue,
        ),
      ),

      //上层 事件穿透
      Listener(
        behavior: HitTestBehavior.translucent,
        onPointerDown: (event) {
          print('上层区域---------');
        },
        child: Container(
          height: 200,
          width: 200,
        ),
      ),
    ]);
  }

  Widget _buildBg({List<Widget> children}) {
    return Scaffold(
      appBar: AppBar(title: Text('测试布局')),
      body: Center(
        child: Stack(
          alignment: Alignment.center,
          children: children,
        ),
      ),
    );
  }
}
复制代码
```

## Toast和Loading冲突

- 这个问题，从理论上肯定会存在的，因为一般Overlay库只会使用一个OverlayEntry控件，这会导致，全局只能存在一个浮窗布局，Toast本质是一个全局弹窗，Loading也是一个全局弹窗，使用其中一个都会导致另一个消失
- Toast明显是应该独立于其他弹窗的一个消息提示，封装在网络库中的关闭弹窗的dismiss方法，也会将Toast消息在不适宜的时候关闭，在实际开发中就碰到此问题，只能多引用一个Toast三方库来解决，在规划这个dialog库的时候，就想到必须解决此问题
  - 此处内部多使用了一个OverlayEntry来解决该问题，提供了相关参数来分别控制，完美使Toast独立于其它的dialog弹窗
  - 多增加一个OverlayEntry都会让内部逻辑和方法使用急剧复杂，维护也会变得不可预期，故额外只多提供一个OverlayEntry；如果需要更多，可copy本库，自行定义，实现该库相关源码，都力求能让人看明白，相信大家copy使用时不会感到晦涩难懂
- FlutterSmartDialog提供`OverlayEntry`和`OverlayEntryExtra`可以高度自定义，相关实现，可查看内部实现
- FlutterSmartDialog内部已进行相关实现，使用`show()`方法中的`isUseExtraWidget`区分

