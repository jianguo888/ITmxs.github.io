---
title: "在Flutter中设置更好的Logging的指南"
date: 2021-10-05T17:22:58+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

今天，我们将研究可以极大减少应用程序调试时间的任务之一。一旦您习惯了在您的应用程序中以某种方式运行的日志，您将很快能够注意到为什么某些东西不起作用。您可以查看应用程序的流程，如果需要，还可以查看更多内容。

我们将使用 logger 包进行所有日志记录。

## 设置

将记录器包添加到您的项目中

```yaml
logger: ^0.6.0
```

## 用法

要使用记录器，您可以在类中创建一个新记录器并使用其中一个方法调用进行记录。

```dart
final logger = Logger();

logger.v('You don\'t always want to see all of these');
logger.d('Logs a debug message');
logger.i('Public Function called');
logger.w('This might become a problem');
logger.e('Something has happened');
```

默认情况下，这些调用将输出以下日志。

![默认日志记录示例](https://luckly007.oss-cn-beijing.aliyuncs.com/img/037-default.7884e05.fe4f85d82cd7b37056f6e4e5ac9c05ca.jpg)

这可能不是每个人都喜欢的。我个人不是所有打印行的忠实粉丝，有些东西我想删除，所以让我们提供一个`PrettyPrinter`实例并对其进行一些自定义。

我想删除上面打印的方法计数，当异常具有堆栈跟踪时，我想在该跟踪中最多看到 5 个方法。我希望原木周围的线条减少，我想保留颜色以提供视觉反馈。表情符号保留，我想禁用时间戳。

```dart
final logger = Logger(
  printer: PrettyPrinter(
    methodCount: 0,
    errorMethodCount: 5,
    lineLength: 50,
    colors: true,
    printEmojis: true,
    printTime: false,
  )
);
```

这导致以下输出

![漂亮的日志调整](https://luckly007.oss-cn-beijing.aliyuncs.com/img/037-pretty.57f1376.50253b65f4a2c44a65830142a4609f2f.jpg)

## 自定义日志打印机

在某些情况下，甚至这对人们来说可能太过分了。老实说，我唯一喜欢的是每个日志的颜色，前面有表情符号。我喜欢使用可视化队列来帮助我更快地调试。正如我之前提到的，在给定特定场景的情况下，您开始了解应用程序中的日志流，而可视化队列将对此提供更多帮助。记录器缺少的一件事是它正在打印的类的名称。我希望将其作为第一条信息。

创建一个名为 log_printer.dart 的新文件，用于打印传入的消息。这是最基本的打印机类型，没有什么特别之处。

```dart
import 'package:logger/logger.dart';

class SimpleLogPrinter extends LogPrinter {
  @override
  void log(Level level, message, error, StackTrace stackTrace) {
    println(message);
  }

}
```

然后我们将设置`SimpleLogPrinter`为我们的 Logger 的打印机。

```dart
  final logger = Logger(printer: SimpleLogPrinter());
```

适合我的日志类型是具有颜色并知道哪个类正在打印日志。所以`SimpleLogPrinter`会取一个名字来显示，并会使用定义的颜色`PrettyPrinter`来打印日志。我们想要以下格式

```text
[emoji] [ClassName] - [Message]

💡 LocationService - Request Location Update
```

让我们快速实施。

```dart
class SimpleLogPrinter extends LogPrinter {
  final String className;
  SimpleLogPrinter(this.className);  

  @override
  void log(Level level, message, error, StackTrace stackTrace) {
    var color = PrettyPrinter.levelColors[level];
    var emoji = PrettyPrinter.levelEmojis[level];
    println(color('$emoji $className - $message'));
  }
}
```

这是输出。

![自定义日志打印机结果](https://luckly007.oss-cn-beijing.aliyuncs.com/img/037-custom.04abc84.5d9ae2d44bc8cc1673743473efabaffb.jpg)

我喜欢。有时我会在某些级别周围添加间距。信息消息尤其是因为它通常是后续其他日志的入口点。我用来记录公共方法调用的信息，因此很容易了解您的代码在做什么。

我们就这样离开吧。您可以根据自己的喜好对其进行更多自定义。创建记录器的代码目前如下所示，这对我来说有点太多了。

```dart
 final logger = Logger(printer: SimpleLogPrinter('PermissionService'));
```

我通常使用顶级函数来为我创建记录器。创建一个名为 logger 的新文件并将其添加到其中。

```dart
import 'package:logger/logger.dart';
import 'log_printer.dart';

Logger getLogger(String className) {
  return Logger(printer: SimpleLogPrinter(className));
}
```

现在在你的代码中你所做的就是这个。

```dart
final log = getLogger('PostService');
```

最后要做的是设置日志记录级别，以便您不会一直看到所有日志。在您的主文件中设置应用程序运行之前的级别。

```dart
void main() {
  Logger.level = Level.verbose;
  runApp(MyApp());
}
```

这就是日志记录。