---
title: "在Flutter中请求权限"
date: 2021-09-29T09:23:51+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍在 Flutter as a Service 中请求权限

当您希望根据权限将用户带到很多路径时，请求权限通常会成为一项麻烦的任务。为了开始所有权限代码的清理过程，我们将其包装在一个具有专用权限请求功能的服务中。

## 设置

在本教程中，我们将使用 permissions_handler 包来请求我们的权限，因此让我们将其添加到 pubspec 中。

```yaml
# Permission checking
permission_handler: ^3.1.0
```

我们将创建函数来请求两种类型的权限，我们将执行位置和联系人。首先，我们需要告诉操作系统我们的应用程序将使用这些权限。

### 安卓

在 AndroidManifest 中`uses-permission`为这两个功能添加标签。

```xml
    <uses-permission android:name="android.permission.READ_CONTACTS" />
    <uses-permission android:name="android.permission.WRITE_CONTACTS" />
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
```

### IOS

在 info.plist 文件中添加密钥和您的消息

```xml
  <key>NSContactsUsageDescription</key>
	<string>This app requires contacts access to function.</string>
	<key>NSLocationWhenInUseUsageDescription</key>
	<string>This app requires access to your location when in use to show relevan information.</string>
	<key>NSLocationAlwaysUsageDescription</key>
	<string>This app requires always on access to to your location to notifiy you when are near a store.</string>
```

## 执行

在`PermissionsService`将有您需要的权限就可以了专用的功能。这样，当用户想要使用需要它的功能时，可以调用它。通过提供回调，为每个函数（组）编写自定义逻辑也很容易。创建一个名为 permissions_service.dart 的新文件，我们将在其中创建一个类，该类具有`PermissionHandler`来自包的的实例。

```dart
import 'package:permission_handler/permission_handler.dart';

class PermissionsService {
  final PermissionHandler _permissionHandler = PermissionHandler();
}
```

然后我们可以添加一个通用函数，它接受 a`PermissionGroup`来请求我们想要的权限。

```dart
...
 Future<bool> _requestPermission(PermissionGroup permission) async {
    var result = await _permissionHandler.requestPermissions([permission]);
    if (result[permission] == PermissionStatus.granted) {
      return true;
    }

    return false;
  }
...
```

使用这个函数，我们可以为每个权限创建特定的函数。

```dart
...
  /// Requests the users permission to read their contacts.
  Future<bool> requestContactsPermission() async {
    return _requestPermission(PermissionGroup.contacts);
  }

  /// Requests the users permission to read their location when the app is in use
  Future<bool> requestLocationPermission() async {
    return _requestPermission(PermissionGroup.locationWhenInUse);
  }
...
```

## 自定义权限逻辑

很多时候，当涉及到权限时，我们想要做一些自定义的事情，或者想要再次提示用户允许我们授予权限。因为我们有专门的功能，我们现在可以以自定义方式处理每个权限请求。假设应用程序需要访问联系人才能工作，例如 whatsapp。我们可以提供一个在权限被拒绝时调用的函数，以便我们可以在外部显示一个对话框。

我们将传入一个`onPermissionDenied`函数，当用户拒绝许可时，该函数将被调用。

```dart
 /// Requests the users permission to read their contacts.
  Future<bool> requestContactsPermission({Function onPermissionDenied}) async {
    var granted = await _requestPermission(PermissionGroup.contacts);
    if (!granted) {
      onPermissionDenied();
    }
    return granted;
  }
```

现在在外面，您可以传入您的函数，在它被拒绝时显示您的对话框，然后如果用户选择这样做，则再次请求它:)

## 有权限

另一件事是检查应用程序是否已经获得许可。我们将创建相同的设置，一个接受 a 的通用函数，`PermissionGroup`然后将专用函数用于特定权限。您不必这样做，我只是发现它更易于维护，并且可以减少外部代码对`PermissionHandler`包的依赖。

```dart
...
  Future<bool> hasContactsPermission() async {
    return hasPermission(PermissionGroup.contacts);
  }

  Future<bool> hasPermission(PermissionGroup permission) async {
    var permissionStatus =
        await _permissionHandler.checkPermissionStatus(permission);
    return permissionStatus == PermissionStatus.granted;
  }
...
```

## 用法

在代码中，我们现在可以使用该服务来请求这样的权限。

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      home: Scaffold(
        body: Center(
          child: MaterialButton(
            color: Colors.yellow[300],
            child: Text('Request contacts permission'),
            onPressed: () {
              PermissionsService().requestContactsPermission(
                  onPermissionDenied: () {
                print('Permission has been denied');
              });
            },
          ),
        ),
      ));
  }
}
```

服务应该被注入或定位，如下所示只使用 provider或在这样的架构中使用 Provider 和 get_it。将您的功能封装在服务中可以消除您的代码与第三方实现细节之间的任何关系，因此它始终是我的首选解决方案。