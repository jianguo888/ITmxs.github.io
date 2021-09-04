---
title: "探索Flutter_Image显示Webp逻辑"
date: 2021-09-05T07:13:22+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

# 简介

最近探索了一下新增Flutter的Image widget对webp做一个stopAnimation的拓展的Api，顺便了解一下Image整个结构和对一些多帧图片的处理。 我们先看看Image的一个类图结构。

![image-20210905071500687](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210905071500687.png)

 其中：



- **ImageProvider** 提供加载图片的入口，不同的图片资源加载方式不一样，只要重写其load方法即可。同样，缓存图片的key值也有其生成。
- **FileImage** 负责读取文件图片的数据，读取到的文件数据转化成ui.Codec对象交给ImageStreamCompleter去处理解析。
- **ImageStreamCompleter**就是逐帧解析图片的类，生成之后会加入ImageCache，下载可以从缓存中得到。
- **ImageStream**是处理Image Resource的，ImageState通过ImageStream与ImageStreamCompleter建立联系。ImageStream里也存储着图片加载完毕的监听回调。
- **MultiFrameImageStreamCompleter**就是多帧图片解析器。 Flutter imgae支持的图片格式为：JPEG, PNG, GIF, Animated GIF, WebP, Animated WebP, BMP, and WBMP。Flutter Image是显示图片的一个Widget。 Flutter Image的几个构造方法：

| 方法                          | 释义                                                         |
| :---------------------------- | :----------------------------------------------------------- |
| Image()                       | 从ImageProvider中获取图片，从本质上看，下面的几个方法都是他的具体实现。 |
| Image.asset(String name)      | 从AssetBundler中获取图片                                     |
| Image.network(String src)     | 显示网络图片                                                 |
| Image.file(File file)         | 从文件中获取图片                                             |
| Image.memory(Uint8List bytes) | 从Uint8List获取数据显示图片                                  |

# Image

从Image的构造体上看，ImageProvider才是图片提供方，所以我们后面会看看ImageProvider究竟是要做点什么的。 其他的参数是一些图片的属性和一些builder。

# ImageState

关键代码：

```dart
void didUpdateWidget(Image oldWidget) {
    super.didUpdateWidget(oldWidget);
    if (_isListeningToStream &&
        (widget.loadingBuilder == null) != (oldWidget.loadingBuilder == null)) {
      _imageStream.removeListener(_getListener(oldWidget.loadingBuilder));
      _imageStream.addListener(_getListener());
    }
    if (widget.image != oldWidget.image)
      _resolveImage();
  }
```

# ImageProvider

其实ImageProvider是一个抽象类，让需要定制的子类去做一些实现。

比如：FileImage、MemoryImage、ExactAssetImage等等。其中对FileImage的代码进行了一些分析。

```dart
class FileImage extends ImageProvider<FileImage> {
  /// Creates an object that decodes a [File] as an image.
  ///
  /// The arguments must not be null.
  const FileImage(this.file, { this.scale: 1.0 })
      : assert(file != null),
        assert(scale != null);
 
  /// The file to decode into an image.
  final File file;
 
  /// The scale to place in the [ImageInfo] object of the image.
  final double scale;
 
  @override
  Future<FileImage> obtainKey(ImageConfiguration configuration) {
    return new SynchronousFuture<FileImage>(this);
  }
 
  @override
  ImageStreamCompleter load(FileImage key) {
    return new MultiFrameImageStreamCompleter(
      codec: _loadAsync(key),
      scale: key.scale,
      informationCollector: (StringBuffer information) {
        information.writeln('Path: ${file?.path}');
      }
    );
  }
 
  Future<ui.Codec> _loadAsync(FileImage key) async {
    assert(key == this);
 
    final Uint8List bytes = await file.readAsBytes();
    if (bytes.lengthInBytes == 0)
      return null;
 
    return await ui.instantiateImageCodec(bytes);
  }
 
  @override
  bool operator ==(dynamic other) {
    if (other.runtimeType != runtimeType)
      return false;
    final FileImage typedOther = other;
    return file?.path == typedOther.file?.path
        && scale == typedOther.scale;
  }
 
  @override
  int get hashCode => hashValues(file?.path, scale);
 
  @override
  String toString() => '$runtimeType("${file?.path}", scale: $scale)';
}
```

FileImage重写了 obtainKey、load的方法。但是在什么地方会调用这两个重写的方法呢？那肯定是ImageProvider这个父类了。

```dart
@optionalTypeArgs
abstract class ImageProvider<T> {
  /// Abstract const constructor. This constructor enables subclasses to provide
  /// const constructors so that they can be used in const expressions.
  const ImageProvider();
 
  /// Resolves this image provider using the given `configuration`, returning
  /// an [ImageStream].
  ///
  /// This is the public entry-point of the [ImageProvider] class hierarchy.
  ///
  /// Subclasses should implement [obtainKey] and [load], which are used by this
  /// method.
  ImageStream resolve(ImageConfiguration configuration) {
    assert(configuration != null);
    final ImageStream stream = new ImageStream();
    T obtainedKey;
    obtainKey(configuration).then<void>((T key) {
      obtainedKey = key;
      stream.setCompleter(PaintingBinding.instance.imageCache.putIfAbsent(key, () => load(key)));
    }).catchError(
      (dynamic exception, StackTrace stack) async {
        FlutterError.reportError(new FlutterErrorDetails(
          exception: exception,
          stack: stack,
          library: 'services library',
          context: 'while resolving an image',
          silent: true, // could be a network error or whatnot
          informationCollector: (StringBuffer information) {
            information.writeln('Image provider: $this');
            information.writeln('Image configuration: $configuration');
            if (obtainedKey != null)
              information.writeln('Image key: $obtainedKey');
          }
        ));
        return null;
      }
    );
    return stream;
  }
 
  /// Converts an ImageProvider's settings plus an ImageConfiguration to a key
  /// that describes the precise image to load.
  ///
  /// The type of the key is determined by the subclass. It is a value that
  /// unambiguously identifies the image (_including its scale_) that the [load]
  /// method will fetch. Different [ImageProvider]s given the same constructor
  /// arguments and [ImageConfiguration] objects should return keys that are
  /// '==' to each other (possibly by using a class for the key that itself
  /// implements [==]).
  @protected
  Future<T> obtainKey(ImageConfiguration configuration);
 
  /// Converts a key into an [ImageStreamCompleter], and begins fetching the
  /// image.
  @protected
  ImageStreamCompleter load(T key);
 
  @override
  String toString() => '$runtimeType()';
}
```

该方法的作用就是创建一个ImageStream，并且ImageConfiguration作为key从ImageCache中获取ImageCompleter，设置到ImageStream上面。而ImageCompleter是为了设置一些回调和帮助ImageStream设置图片的一个类。

ImageConfiguration是对于ImageCompleter的一些配置。

ImageCache是对于ImageCompleter的缓存。 ImageStreamCompleter putIfAbsent(Object key, ImageStreamCompleter loader(), { ImageErrorListener onError }) 这个方法在resolve方法中是一个关键方法。

```dart
ImageStreamCompleter putIfAbsentImageStreamCompleter putIfAbsent(Object key, ImageStreamCompleter loader()) {
    assert(key != null);
    assert(loader != null);
    ImageStreamCompleter result = _cache[key];
    if (result != null) {
      // Remove the provider from the list so that we can put it back in below
      // and thus move it to the end of the list.
      _cache.remove(key);
    } else {
      if (_cache.length == maximumSize && maximumSize > 0)
        _cache.remove(_cache.keys.first);
      result = loader();
    }
    if (maximumSize > 0) {
      assert(_cache.length < maximumSize);
      _cache[key] = result;
    }
    assert(_cache.length <= maximumSize);
    return result;
 }
```

这个方法是在imageCache里面的，提供的是内存缓存api的入口方法，putIfAbsent会先通过key获取之前的ImageStreamCompleter对象，这个key就是NetworkImage对象，当然我们也可以重写obtainKey方法自定义key，如果存在则直接返回，如果不存在则执行load方法加载ImageStreamCompleter对象，并将其放到首位（最少最近使用算法）。 也就是说ImageProvider已经实现了内存缓存：默认缓存图片的最大个数是1000，默认缓存图片的最大空间是10MiB。 第一次加载图片肯定是没有缓存的，所以会调用loader方法，那就是方法外面传进去的load()方法。

FileImage的load方法

```dart
@override
ImageStreamCompleter load(FileImage key) {
  return new MultiFrameImageStreamCompleter(
    codec: _loadAsync(key),
    scale: key.scale,
    informationCollector: (StringBuffer information) {
      information.writeln('Path: ${file?.path}');
    }
  );
}
 
Future<ui.Codec> _loadAsync(FileImage key) async {
  assert(key == this);
 
  final Uint8List bytes = await file.readAsBytes();
  if (bytes.lengthInBytes == 0)
    return null;
 
  return await ui.instantiateImageCodec(bytes);
}
```

load方法中使用了一个叫MultiFrameImageStreamCompleter的类：

```dart
MultiFrameImageStreamCompleter({
  @required Future<ui.Codec> codec,
  @required double scale,
  InformationCollector informationCollector
}) : assert(codec != null),
     _informationCollector = informationCollector,
     _scale = scale,
     _framesEmitted = 0,
     _timer = null {
  codec.then<void>(_handleCodecReady, onError: (dynamic error, StackTrace stack) {
    FlutterError.reportError(new FlutterErrorDetails(
      exception: error,
      stack: stack,
      library: 'services',
      context: 'resolving an image codec',
      informationCollector: informationCollector,
      silent: true,
    ));
  });
}
```

MultiFrameImageStreamCompleter是ImageStreamCompleter的子类，为了处理多帧的图片加载，Flutter的Image支持加载webp，通过MultiFrameImageStreamCompleter可以对webp文件进行解析，MultiFrameImageStreamCompleter拿到外面传入的codec数据对象,通过**handleCodecReady**来保存Codec，之后调用**decodeNextFrameAndSchedule**方法，从Codec获取下一帧图片数据和把数据通知回调到Image，并且开启定时解析下一帧图片数据。

到此为止，基本dart流程就走完了，所以需要做stopAnimation和startAnimation的改造就应该这这个MultiFrameImageStreamCompleter入手了。

# 最后

整个在Dart层面Image解析webp的流程就这样，