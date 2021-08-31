---
title: "Dart在Flutter中创建圆形缓存图像"
date: 2021-08-31T08:45:54+08:00
draft: true
---

我想创建一个圆形图像，其中图像是从网络中获取的，并且也缓存在 Flutter 中。

这是我为从网络获取的圆形图像但未缓存图像的代码。

```dart
new Container(
    width:80.0,
    height: 80.0,
    decoration: new BoxDecoration(
    shape: BoxShape.circle,
        image: new DecorationImage(
            image: new NetworkImage('https://pbs.twimg.com/profile_images/945853318273761280/0U40alJG_400x400.jpg'),
        ),
    ),
),
```


现在我找到了一个用于从网络中获取、缓存和呈现图像的小部件

```
new CachedNetworkImage(imageUrl: 'https://pbs.twimg.com/profile_images/945853318273761280/0U40alJG_400x400.jpg')
```


但是当我用这个 CachedNetworkImage 替换 NetworkImage 小部件时，它给了我一个错误，说 NetworkImage 不是类型图像。

如何实现可以缓存的圆形图像？

编辑:
我按照答案中的建议进行了尝试，但仍然遇到相同的错误:无法将参数类型“CachedNetworkImage”分配给参数类型“DecorationImage”。

```dart
              decoration: new BoxDecoration(
                shape: BoxShape.circle,
                image: new CachedNetworkImage(image: 
                      'https://pbs.twimg.com/profile_images/945853318273761280/0U40alJG_400x400.jpg'),
              ),
```



**最佳答案**

[CachedNetworkImage](https://github.com/renefloor/flutter_cached_network_image/blob/master/lib/src/cached_image_widget.dart)有一个构建器( `ImageWidgetBuilder` )来进一步自定义图像的显示。
试试这个方法:

```dart
CachedNetworkImage(
  imageUrl: 'https://pbs.twimg.com/profile_images/945853318273761280/0U40alJG_400x400.jpg',
  imageBuilder: (context, imageProvider) => Container(
    width: 80.0,
    height: 80.0,
    decoration: BoxDecoration(
      shape: BoxShape.circle,
      image: DecorationImage(
        image: imageProvider, fit: BoxFit.cover),
    ),
  ),
  placeholder: (context, url) => CircularProgressIndicator(),
  errorWidget: (context, url, error) => Icon(Icons.error),
),
```

`placeholder`和 `errorWidget`是小部件，这意味着您可以将任何小部件放入其中并根据需要自定义它们。
