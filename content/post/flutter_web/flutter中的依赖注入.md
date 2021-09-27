---
title: "Flutter中的依赖注入"
date: 2021-09-27T17:03:15+08:00
draft: true
---

 掘金

# Flutter 中的依赖注入

在 Flutter 中，向小部件提供对象/服务的默认方式是通过 InheritedWidgets。如果您希望小部件或其模型能够访问服务，则该小部件必须是继承的小部件的子代。这会不必要的嵌套。 掘金

这就是[get_it](https://pub.dartlang.org/packages/get_it)用武之地。一个 IoC，它允许您注册您的类类型并从您有权访问容器的任何地方请求它。听起来更好？让我们设置一下。

## pub get

在您的 pubspec 中为 get_it 添加依赖项。

```yaml
  ...

  # dependency injection
 get_it: ^7.2.0

  ...
```

在您的 lib 文件夹中创建一个名为 service_locator.dart 的新文件。导入 get_it 并创建一个名为 locator 的 getIt 新实例。我们将使用全局范围内的所有内容，因此我们可以只导入文件并访问定位器。

创建一个名为 setupLocator 的新函数，我们将在其中注册我们的服务和模型。

```dart
import 'package:get_it/get_it.dart';

GetIt locator = GetIt();

void setupLocator() {
}
```

在`setupLocator`我们运行应用程序之前调用 main.dart 文件。

```dart
import 'service_locator.dart';

void main() {
  setupLocator();
  runApp(MyApp());
}
```

### 设置

在 lib 文件夹中创建一个名为 services 的新文件夹。在该文件夹下创建两个新文件 login_service.dart 和 user_service.dart

**登录服务**

```dart
class LoginService {
    String loginToken = "my_login_token";
}
```

**用户服务**

```dart
class UserService {
    String userName = "filledstacks";
}
```

使用 get_it，可以通过两种方式注册类类型。

**工厂：**当您从服务提供者请求该类型的实例时，您每次都会获得一个新实例。适合注册需要在启动时运行相同逻辑或在视图打开时必须是新逻辑的 ViewModel。

**Singleton**：**Singleton**可以通过两种方式注册。在注册时提供实现或提供将在第一次请求实例时调用的 lamda (LazySingleton)。定位器保留您注册类型的单个实例，并将始终返回该实例。

### 注册类型

转到您的服务定位器并导入两个新服务。我们将 注册`UserService`为单例，并将注册`LoginService`为工厂。

```dart
import './services/user_service.dart';
import './services/login_service.dart';

...

void setupLocator() {
  locator.registerSingleton(UserService());
  locator.registerFactory<LoginService>(() => LoginService());
}
```

现在，无论您在何处需要该服务，您都将导入 service_locator.dart 文件并请求如下所示的类型。

```dart
import 'package:my_project/service_locator.dart';

...

var userService = locator<UserService>();

...
```

您不需要包装任何小部件来继承任何东西，也不需要任何地方的上下文。您所做的就是导入 service_locator 文件并使用定位器来解析您的类型。这意味着在没有上下文的地方，即使应用程序的 UI 结构发生变化，您仍然可以注入正确的服务和值。
