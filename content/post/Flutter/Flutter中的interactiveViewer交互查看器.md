---
title: "Flutter中的interactiveViewer交互查看器"
date: 2021-08-15T12:39:29+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---





使用 InteractiveViewer 包裹您的更大或超大的超大部件，以进行缩放、挤压、限制、笔画和自定义等操作。

```dart
 InteractiveViewer(
                transformationController: transformationController,
                boundaryMargin: EdgeInsets.all(20.0),
                minScale: 0.1,
                maxScale: 1.6,
// You can off zooming by setting scaleEnable to false
//scaleEnabled: false,
                onInteractionStart: (_) => print("Interaction Started"),
                onInteractionEnd: (details) {
                  setState(() {
                    transformationController.toScene(Offset.zero);
                  });
                },
                onInteractionUpdate: (_) => print("Interaction Updated"),
                child: ClipRRect(
                    borderRadius: BorderRadius.circular(15),
                    child: Image.network(
                        "https://images.unsplash.com/photo-1555156806-4619c2286156?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=500&q=60")),
              ),
```

现在我们讨论他们提供什么适当的，所以首先是，

1. **有约束的**

默认情况下，f you set any image there，hey take this as squeeze way(they try to show full image in this small screen)

因此，如果您只需要显示图像的某些部分，则根据您的要求设置 false。

2. **ScaleEnable**

如果为 false，则将阻止用户缩放。

意味着他们设置了移动限制。

3. **边距**

子级可见边界的边距。
任何导致视口能够在边界外查看的变换都将在边界处停止。边界不随场景的其余部分旋转，因此它们始终与视口对齐。
要完全不产生边界，请传递无限的 EdgeInsets。

比如，asEdgeInsets.all(double.infinity)。

4. **onInteractionStart**

这用于注册回调，

当用户在小部件上开始平移或缩放手势时调用它们。
即使使用 panEnabled 或 scaleEnabled 禁用交互，也会调用。

5. **变换控制器**

通过使用这些，您可以为编程笔和缩放提供自己的控制器。

换句话说，

一个 TransformationController 用于对孩子执行的转换。
每当子对象被转换时，Matrix4value 都会更新并通知所有侦听器。如果设置了该值，InteractiveViewer 将更新以遵守新值。

因此，如果您的小部件比您的视图大，请尝试使用这个**InteractiveViewer**。