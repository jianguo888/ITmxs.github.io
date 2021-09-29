---
title: "使用缓存管理器在Flutter中下载和缓存文件"
date: 2021-09-29T09:06:02+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍使用缓存管理器在 Flutter 中下载和缓存文件

本指南将向您展示如何使用 Flutter 缓存管理器下载和缓存文件。

## 安装包

打开您的 pubspec 并添加`flutter_cache_manager`包。

```dart
flutter_cache_manager: ^0.3.2
```

在主文件中，我们将创建一个简单的 UI 来显示一些反馈，以便我们知道发生了什么。我们将有一个材料应用程序并将主页小部件设置为等于我们在下面创建的 HomeView。HomeView 是有状态的，并有一个字符串标题作为成员变量，并在屏幕中央显示该标题。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(home: HomeView());
  }
}

class HomeView extends StatefulWidget {
  HomeView({Key key}) : super(key: key);

  _HomeViewState createState() => _HomeViewState();
}

class _HomeViewState extends State<HomeView> {
  String title = 'Waiting to download';

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      floatingActionButton: FloatingActionButton(
        onPressed: () {},
      ),
      body: Center(child: Text(title)),
    );
  }
}
```

我们还有一个带有空 onPressed 的浮动操作按钮。这就是设置。

## 下载并缓存文件

缓存的工作方式是下载文件并将路径返回给您，它还将该文件缓存在设备的临时文件夹中一段时间。如果您在文件仍然有效时再次请求该文件，则它会立即返回它。如果文件已过期，则再次下载该文件。在按下时，我们将标题设置为“正在下载...”并获取一个文件。文件完成后，我们将在标题中显示文件在磁盘上的路径。

```dart
// Add the url
 String url = 'https://firebasestorage.googleapis.com/v0/b/filledstacks.appspot.com/o/filledstacks_tutorials.pdf?alt=media&token=a5e671e7-5acd-4bc4-a167-8d8483954d2a';

 ...
 floatingActionButton: FloatingActionButton(
    onPressed: () async {
      setState(() => title = 'Downloading...');
      var fetchedFile = await DefaultCacheManager().getSingleFile(url);
      setState(() => title = 'File fetched: ${fetchedFile.path}');
    },
  ),
...
```

如果您点击该按钮，那么您将第一次看到“正在下载...”，然后是“文件已获取：文件/路径/”消息。这是多么容易。现在，当您再次点击下载时，您几乎会立即看到获取的文件。这意味着它已被缓存，现在只需通过请求即可轻松访问。

如果您担心每次都创建 DefaultCacheManager 的新实例，则不必担心。它是使用单例模式实现的，因此它只会创建一次对象，并在您构造它时将该实例返回给您。

```dart
// Package code
static DefaultCacheManager _instance;
 factory DefaultCacheManager() {
    if (_instance == null) {
      _instance = new DefaultCacheManager._();
    }
    return _instance;
  }
```

所以无需担心。继续使用`DefaultCacheManager()`.

## 自定义缓存管理器

您可能需要一些不同的缓存设置。您可能希望限制您的应用程序可以缓存的对象数量以节省用户空间，您可能希望设置更短的缓存持续时间（默认 30 天），甚至提供您自己的文件处理程序。让我们来看看如何做到这一点。

该包为您提供`BaseCacheManager`了可以在您自己的代码中扩展和实现的功能。创建一个扩展 BaseCacheManager 的新类 CustomCacheManager。有一种必需的方法`getFilePath`可以返回将用于缓存的文件夹的基本路径。您还必须为超类提供一个缓存键来标识您的缓存。通过这种方式，您可以让不同的缓存指向不同的文件夹并且具有唯一的键。我们将我们的密钥存储为 const 并将其提供给 super。

```dart
...
import 'package:path/path.dart' as path;
import 'package:path_provider/path_provider.dart';

class CustomCacheManager extends BaseCacheManager {
  static const key = "pdfCache";

  CustomCacheManager() : super(key);

  @override
  Future<String> getFilePath() {
    return null;
  }
}
```

对于该`getFilePath`函数，我们将返回我们正在运行的设备的临时目录的路径，并将我们的密钥附加到末尾，以便我们可以根据需要识别磁盘上的缓存。

```dart
...
@override
Future<String> getFilePath() async {
  var directory = await getTemporaryDirectory();
  return path.join(directory.path, key);
}
...
```

这就是自定义缓存**所需**的全部内容，现在我们可以添加一些其他设置。假设我们只希望最多缓存 10 个文件，并且每个文件只应缓存 30 秒。这是我们如何实现这一目标。

```dart
...
// Add const values at the top
static const int maxNumberOfFiles = 10;
static const Duration cacheTimeout = Duration(seconds: 30);

// pass values into super
CustomCacheManager()
    : super(
        key,
        maxNrOfCacheObjects: maxNumberOfFiles,
        maxAgeCacheObject: cacheTimeout
      );
...
```

我们可以用我们的 CustomCacheManager 交换 DefaultCacheManager。我们将在 build 方法之外创建一个实例，因此它只创建一次。

```dart
// Create instance 
CustomCacheManager cacheManager = CustomCacheManager();

... 
// use in the floating action button on pressed
floatingActionButton: FloatingActionButton(
  onPressed: () async {
    setState(() => title = 'Downloading...');
    var fetchedFile = await cacheManager.getSingleFile(url);
    setState(() => title = 'File fetched: ${fetchedFile.path}');
  },
),
...
```

点击 FAB 仍然会给你相同的结果，但现在你会看到你的缓存的键也在路径中。这表明它现在正在通过您的缓存。如果您等待 30 秒并点击，您会看到它再次需要很长时间，这是因为文件已过期。十分简单。

您还可以通过为返回 FileFetcherResponse 对象的 fileFetcher 属性提供 Future 来实现您自己的文件提取器。

```dart
CustomCacheManager()
  : super(
      key,
      maxNrOfCacheObjects: maxNumberOfFiles,
      maxAgeCacheObject: cacheTimeout,
      fileFetcher: _customHttpGetter
    );

static Future<FileFetcherResponse> _customHttpGetter(String url, {Map<String, String> headers}) async {
  // Do things with headers, the url or whatever.
  return HttpFileFetcherResponse(...);
}
```

这里的所有都是它的。您现在应该能够有效地处理 Flutter 应用程序中的缓存。我建议使用 DefaultCacheManager 实现与上面显示的相同的单例模式。这样您就不会两次构造相同的缓存。