---
title: "《Flutter之precacheImage（）解析》"
subtitle: ""
date: 2021-07-31T21:12:42+08:00
lastmod: 2021-07-31T21:12:42+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍Flutter 中 `precacheImage()` 方法是如何提高图片加载速度的？

<!--more-->





Flutter 文档中提到调用 [precacheImage()](https://api.flutter.dev/flutter/widgets/precacheImage.html) 预先加载图片加载到缓存，后续如果这张图片被 `Image` 控件用到的话，加载起来会更快。

> Prefetches an image into the image cache.
>
> If the image is later used by an [Image] or [BoxDecoration] or [FadeInImage], it will probably be loaded faster.

`precacheImage()` 代码如下：

```
Future<void> precacheImage(
  ImageProvider provider,
  BuildContext context, {
  Size size,
  ImageErrorListener onError,
}) {
  final ImageConfiguration config = ...;
  final Completer<void> completer = Completer<void>();
  final ImageStream stream = provider.resolve(config);
  ...
  return completer.future;
}
```

`precacheImage()` 是如何加快图片加载速度的？我们可以从如下两个角度来考虑这个问题：

- 写缓存 - 如何将图片加入缓存
- 读缓存 - 如何从缓存取出图片

# 写缓存

先上结论：**`precacheImage()` 调用 `ImageProvider.resolve()` 从缓存中取出图片**。

`precacheImage()` 主要的调用关系如下：

```
precacheImage() ->
 ImageProvider.resolve() ->
  ImageCache.putIfAbsent() ->
   ImageProvider.load() ->
```

## `ImageProvider.resolve()`

```
 ImageStream resolve(ImageConfiguration configuration) {
    ...;
    final ImageStream stream = ImageStream();
    T obtainedKey;

    key = obtainKey(configuration);
    key.then<void>((T key) {
      obtainedKey = key;
      final ImageStreamCompleter completer = PaintingBinding.instance.imageCache.putIfAbsent(
        key,
        () => load(key, PaintingBinding.instance.instantiateImageCodec),
        onError: handleError,
      );
      if (completer != null) {
        stream.setCompleter(completer);
      }
    }).catchError(handleError);
    return stream;
}
```

注意 `imageCache.putIfAbsent()` 第二个参数形式如下：

```
ImageStreamCompleter putIfAbsent(
  Object key,
  ImageStreamCompleter loader(), 
  { ImageErrorListener onError })
```

所以这里的 `loader` 参数实际上是对 `load()` 方法的封装。

## `ImageCache.putIfAbsent()`

`ImageCache` 用于缓存图片的类。这个类实现了 LRU 算法，最多可以保存1000个图片或者100MB。

```
class ImageCache {
  final Map<Object, _PendingImage> _pendingImages = <Object, _PendingImage>{};
  final Map<Object, _CachedImage> _cache = <Object, _CachedImage>{};
```

- `_pendingImages` - 保存正在加载中的图片
- `_cache` - 保存已加载到缓存的图片

`putIfAbsent()` 方法是 `ImageCache` 主要的入口方法，代码如下：

```
ImageStreamCompleter putIfAbsent(Object key, ImageStreamCompleter loader()) {
  ImageStreamCompleter result = _pendingImages[key]?.completer;
  // Nothing needs to be done because the image hasn't loaded yet.
  if (result != null)
    return result;
  // Remove the provider from the list so that we can move it to the
  // recently used position below.
  final _CachedImage image = _cache.remove(key);
  if (image != null) {
    _cache[key] = image;
    return image.completer;
  }
  try {
    result = loader();
  } catch (error, stackTrace) {
    ...
  }
  void listener(ImageInfo info, bool syncCall) {
    // Images that fail to load don't contribute to cache size.
    final int imageSize = info?.image == null ? 0 : info.image.height * info.image.width * 4;
    final _CachedImage image = _CachedImage(result, imageSize);
    ...
    final _PendingImage pendingImage = _pendingImages.remove(key);
    if (pendingImage != null) {
      pendingImage.removeListener();
    }

    _cache[key] = image;
    ...
  }
  if (maximumSize > 0 && maximumSizeBytes > 0) {
    final ImageStreamListener streamListener = ImageStreamListener(listener);
    _pendingImages[key] = _PendingImage(result, streamListener);
    // Listener is removed in [_PendingImage.removeListener].
    result.addListener(streamListener);
  }
  return result;
}
```

其过程总结如下：

- 如果 `_pendingImages` 存在对应的 key，说明正在加载该图片，此时什么也不用做所以直接返回

- 如果 `_cache` 存在对应的 key，说明已经加载过该图片，此时将图片调整到 LRU 位置并返回

- 否则，调用

   

  ```
  loader()
  ```

   

  来加载图片。

  ```
  loader()
  ```

   

  的返回值为

   

  ```
  result
  ```

  - 为 `result` 创建新对象并保存到 `_pendingImages` 中
  - 监听图片加载过程。加载成功后从 `_pendingImages` 移除对应的 key，并在 `_cache` 中保存加载结果

## `ImageProvider.load()`

`ImageProvider` 是一个抽象类，它的 `load()` 和 `obtainKey()` 是抽象方法，需要 `ImageProvider` 的子类实现。

以 `AssetBundleImageProvider` 为例，`load()` 方法如下：

```
abstract class AssetBundleImageProvider extends ImageProvider<AssetBundleImageKey> {
  @override
  ImageStreamCompleter load(AssetBundleImageKey key, DecoderCallback decode) {
    return MultiFrameImageStreamCompleter(
      codec: _loadAsync(key, decode),
      scale: key.scale,
      informationCollector: () sync* {
        yield DiagnosticsProperty<ImageProvider>('Image provider', this);
        yield DiagnosticsProperty<AssetBundleImageKey>('Image key', key);
      },
    );
  }
  
  @protected
  Future<ui.Codec> _loadAsync(AssetBundleImageKey key, DecoderCallback decode) async {
    final ByteData data = await key.bundle.load(key.name);
    if (data == null)
      throw 'Unable to read data';
    return await decode(data.buffer.asUint8List());
  }
}
```

从代码不难看出这里的 `_loadAsync()` 即底层的 asset 图片加载过程，它由 `load()` 方法调用。而 `load()` 是前面 `result = loader();` 的具体执行过程。

至此可知，调用 `precache()` 可将指定 key 对应的 `_CachedImage` 保存到 `ImageCache` 中。

# 读缓存

先上结论：**`Image` 调用 `ImageProvider.resolve()` 从缓存中取出图片**。

`Image` 控件用于显示图片。有多种方式显示图片：

- `Image.asset` - 使用 key 从 `AssetBundle` 图片
- `Image.network()` - 使用 url 从网络加载图片
- `Image.file` - 从文件加载图片
- `Image.memory` - 从内存加载图片

这些显示方式都有各自的 `ImageProvider`：

- `AssetImage`
- `NetworkImage`
- `FileImage`
- `MemoryImage`

上述这些 Image 都继承自 `ImageProvider`。

```
abstract class ImageProvider<T> {
  ImageStream resolve(ImageConfiguration configuration) {}
  Future<bool> evict({ ImageCache cache, ImageConfiguration configuration = ImageConfiguration.empty }) async {}
  Future<T> obtainKey(ImageConfiguration configuration);
  ImageStreamCompleter load(T key, DecoderCallback decode);
}

abstract class AssetBundleImageProvider extends ImageProvider<AssetBundleImageKey> {}

abstract class NetworkImage extends ImageProvider<NetworkImage> {

class FileImage extends ImageProvider<FileImage> {

class MemoryImage extends ImageProvider<MemoryImage> {}
```

`Image` 它是一个 `StatefulWidget`，它依赖于其 `ImageProvider` 成员，主要代码如下：

```
class Image extends StatefulWidget {
 final ImageProvider image;
  _ImageState createState() => _ImageState();
}

class _ImageState extends State<Image> with WidgetsBindingObserver {
  @override
  void didChangeDependencies() {
    ...
    _resolveImage();
    ...
    super.didChangeDependencies();
  }

  @override
  void didUpdateWidget(Image oldWidget) {
    super.didUpdateWidget(oldWidget);
    if (widget.image != oldWidget.image)
      _resolveImage();    
  }
  
  void _resolveImage() {
    final ImageStream newStream =
      widget.image.resolve(createLocalImageConfiguration(
        context,
        size: widget.width != null && widget.height != null ? Size(widget.width, widget.height) : null,
      ));
    _updateSourceStream(newStream);
  }
}
```

注意这里的 `_resolveImage()` 方法，它调用 `ImageProvider.resolve()`。所以可以认为是由 `ImageProvider.resolve()` 从缓存中取出图片(前面分析过 `ImageProvider.resolve()` 与图片缓存的关系)。

# 总结

`precacheImage()` 提升图片加载速度的关键其实在于主动调用 `ImageProvider.resolve()` 来提前将图片加载到 `ImageCache`，示意图如下(这里假设从本地读取图片)：

![-w850](https://luckly007.oss-cn-beijing.aliyuncs.com/img/15814253180199.jpg)

如果后续有 `Image` 显示同一个 key 对应的图片，再次调用 `ImageProvider.resolve()` 的速度将大为提升(网络图片会尤其明显)，当然前提是该图片仍然在图片缓存中。

# 参考

- [precacheImage function - widgets library - Dart API](https://api.flutter.dev/flutter/widgets/precacheImage.html)