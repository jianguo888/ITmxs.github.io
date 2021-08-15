---
title: "Flutter中的bloc2"
date: 2021-08-15T16:33:23+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---





在这一部分，我们将使用 Bloc 模式实现一个天气应用程序。您还将深入了解如何使用 http 包进行 API 调用🥳
所以，让我们开始吧！！！！

# **我们将使用的软件包：**

- [equatable](https://pub.dev/packages/equatable)：`Equatable`覆盖`==`并`hashCode`为您服务，因此您不必浪费时间编写大量样板代码。
- [flutter_bloc](https://pub.dev/packages/flutter_bloc)：`flutter_bloc`是我们的主要包，旨在使我们的任务在 Flutter 中轻松实现 bloc。它适用于官方[软件包：bloc](https://pub.dev/packages/bloc)
- [http](https://pub.dev/packages/http)：该包包含一组高级函数和类，可以轻松使用 HTTP 资源。它是多平台的，支持移动、桌面和浏览器。所以，我们主要将这个包用于 HTTP 请求。

![img](https://miro.medium.com/max/700/0*z1JzXz9288RtGTyA.jpg)

在 Bloc 中，将有 3 个主要文件：

- **bloc 文件：**该文件包含主要的业务逻辑
- **事件文件：**此文件说明您的应用程序中存在的所有事件。
- **状态文件：**此文件包含您的应用程序经历的所有状态。

**flutter_bloc 包提供了 4 个主要类，我们可以将它们包装在一个小部件上：**

- **BlocProvider：** BlocProvider 是将 Bloc 实例提供给子子树的类/小部件。在大多数情况下，`BlocProvider`应该用于创建新的块，这些块可供子树的其余部分使用。在这种情况下，由于`BlocProvider`负责创建 bloc，它将自动处理关闭它。句法：

```dart
BlocProvider( 
  create: (BuildContext context) => BlocA(), 
  child: ChildA(), 
);
```

- **BlocBuilder：** BlocBuilder 小部件负责在状态发生变化时构建/重新构建子子树。它需要一个`bloc`和一个`builder`函数。`BlocBuilder`非常相似，`StreamBuilder`但有一个更简单的 API 来减少所需的样板代码量。句法：

```
BlocBuilder<BlocA, BlocAState>( 
  builder: (context, state) { 
    // 根据 BlocA 的状态在此处返回小部件
  } 
)
```

- **BlocListener：** BlocListener 是一个 Flutter 小部件，它采用 a`BlocWidgetListener`和 an 可选`bloc`并调用`listener`以响应 bloc 中的状态更改。它应该用于需要在每次状态更改时发生一次的功能，例如导航、显示 a `SnackBar`、显示 a`Dialog`等......`listener`与`builder`in不同的`BlocBuilder`是，每次状态更改（不包括初始状态）只调用一次并且是一个`void`函数。句法：

```dart
BlocListener<BlocA, BlocAState>(
  listener: (context, state) {
    // do stuff here based on BlocA's state
  },
  child: Container(),
)
```

- **BlocConsumer：** BlocConsumer 是 BlocBuilder 和 BlocListener 的混合体。它暴露了`builder`和`listener`以对新状态做出反应。`BlocConsumer`类似于嵌套`BlocListener`，`BlocBuilder`但减少了所需的样板数量。`BlocConsumer`仅在需要重建 UI 并执行其他响应以对`bloc`. 句法：

```
BlocConsumer<BlocA, BlocAState>(
  listener: (context, state) {
    // do stuff here based on BlocA's state
  },
  builder: (context, state) {
    // return widget here based on BlocA's state
  }
)
```

# 理论讲得太多了，不是吗？所以现在，让我们做一些真正的事情！！



**步骤0：在开始之前，这次我们将通过分离我们项目中的bloc、模型、屏幕和服务来创建一个更好的项目架构。您也可以最初创建此文件夹。完成后，这就是我们的项目结构的样子：**

![img](https://miro.medium.com/max/604/1*TO0uHdQG6t7v3m_l__kPEw.png)

**第 1 步：****在 pubspecy.yaml 中导入**[equatable](https://pub.dev/packages/equatable)**、[flutter_bloc](https://pub.dev/packages/flutter_bloc)和[http](https://pub.dev/packages/http)

```
equatable: ^1.2.5
flutter_bloc: ^6.1.1 
http: ^0.12.2
```

**第 2 步：**创建一个名为的文件夹`bloc`并在其中创建 3 个文件。在这里，我们将命名它们`weather_bloc`,`weather_event`和`weather_state`。

**第 3 步：**在本教程中，我们将使用伪造的 Weather API。您也可以使用任何您喜欢的真实产品。

```
Base URL: https://01b0f7a1-9563-47f5-b628-f14c51464ec5.mock.pstmn.io/api/get-weather-success?city=
```

**第 4 步：**让我们首先检查 API 的响应并为其创建一个模型。当我们从我们的 API 得到响应时，我们将使用这个模型。上述 API 给出了以下响应：

![image-20210815163859938](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815163859938.png)

由于这是一个伪造的 API，每个城市名称都会给出相同的响应 🤣
现在，为模型创建一个文件**lib/model/weather.dart。**模型文件将如下所示：

```dart
class Weather {
  int temperature;
  int yesterdayMax;
  int yesterdayMin;
  int todayMax;
  int todayMin;
  String city;
  Weather({
    this.city,
    this.temperature,
    this.yesterdayMax,
    this.yesterdayMin,
    this.todayMax,
    this.todayMin,
  });
}
```

**第 5 步：**下一步是创建我们的 ApiService，然后继续。首先，首先在 lib 文件夹中创建一个名为 services 的文件夹，即**lib/services。**在 services 文件夹中，创建一个名为**api_service.dart**的文件**。**您的文件结构将如下所示：

![image-20210815165608878](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815165608878.png)

在 api_service.dart 中，您可以使用 http 包编写 API 请求代码。这是相同的代码：

```dart
import 'dart:convert';
import 'package:bloc_weather_example/screens/constants.dart';
import 'package:bloc_weather_example/model/weather.dart';
import 'package:http/http.dart' as http;
class ApiService {
  static Future getWeather(String city) async {
    String requestUrl = https://01b0f7a1-9563-47f5-b628-f14c51464ec5.mock.pstmn.io/api/get-weather-success?city=$city;
    http.Response response = await http.get(requestUrl);
    if (response.statusCode == 200) {
      var decodedData = jsonDecode(response.body);
      Weather _weather = Weather(
          city: city,
          temperature: decodedData['temperature'],
          yesterdayMax: decodedData['yesterdayMax'],
          yesterdayMin: decodedData['yesterdayMin'],
          todayMax: decodedData['todayMax'],
          todayMin: decodedData['todayMin']);
      return _weather;
    } else {
      print(response.statusCode);
    }
  }
}
```

在这里，我们创建了一个名为 getWeather() 的方法，该方法将城市名称作为参数。然后我们将城市名称附加到 baseUrl。这将成为我们的 requestUrl。下一步是创建对 requestUrl的**get**请求。
我们将得到一个**Response**对象作为 get 请求的响应。我们现在必须解码它，以便我们可以提取它的值。因此，如果我们成功获得响应（状态代码 == 200），我们将使用名为 jsonDecode() 的方法解码响应对象并将 response.body 传递给它。一旦我们解码了响应，我们就可以创建模型的对象并返回它🥳

**第 6 步：**现在，让我们创建我们的 bloc 文件。创建一个名为`bloc`(lib/bloc)的文件夹并在其中创建 3 个文件。在这里，我们将它们命名为`weather_bloc`,`weather_event`和`weather_state`。它看起来像这样：

![](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210815165608878.png)

**weather_event.dart:**

```dart
part of 'weather_bloc.dart';
@immutable
abstract class WeatherEvent {}
class WeatherInitialEvent extends WeatherEvent {}
class WeatherGetEvent extends WeatherEvent {
  final String city;
  WeatherGetEvent({this.city});
}

```

在这里，我们只创建了 2 个事件，即 WeatherInitialEvent 和 WeatherGetEvent。WeatherGetEvent 将城市名称作为参数。**剧透警报：**这个城市名称将从 bloc 传递给我们的 ApiService 方法😉

**weather_state.dart**

```
part of 'weather_bloc.dart';
@immutable
abstract class WeatherState {}
class WeatherInitial extends WeatherState {}
class WeatherLoadingState extends WeatherState {}
class WeatherGetState extends WeatherState {}
class WeatherLoadedState extends WeatherState {
  final Weather weather;
  WeatherLoadedState({this.weather});
}
```

在这里，我们创建了 4 个状态。WeatherInitial、WeatherLoading、WeatherGetState 和 WeatherLoadedState。各州将按照其名称进行工作。WeatherLoadedState 占用了我们将在 UI 中使用的模型 Weather 的参数。

**Weather_bloc.dart：**

```dart
import 'dart:async';
import 'package:bloc/bloc.dart';
import 'package:bloc_weather_example/model/weather.dart';
import 'package:bloc_weather_example/services/api_service.dart';
import 'package:meta/meta.dart';
part 'weather_event.dart';
part 'weather_state.dart';
class WeatherBloc extends Bloc<WeatherEvent, WeatherState> {
  WeatherBloc() : super(WeatherInitial());
@override
  Stream<WeatherState> mapEventToState(WeatherEvent event) async* {
    if (event is WeatherGetEvent) {
      yield WeatherLoadingState();
      Weather _weather = await ApiService.getWeather(event.city);
      yield WeatherLoadedState(weather: _weather);
    }
  }
}
```

在我们的 bloc 文件中，我们只是调用了 ApiService.getWeather() 方法。在调用之前，我们让出加载状态以显示 CircularProgressIndicator。一旦响应可用，我们就会产生一个加载状态并将模型响应传递给该状态。

所以，到这里我们已经完成了我们的 bloc 实现。那么，现在让我们实现我们的 UI 吧！！😍

**第 7 步：**创建一个文件夹 lib/screens，我们将在其中存储所有 UI 相关文件。让我们首先更新**main.dart**：

```dart
import 'package:bloc_weather_example/bloc/weather_bloc.dart';
import 'package:bloc_weather_example/screens/home.dart';
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
void main() => runApp(MyApp());
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Material App',
      debugShowCheckedModeBanner: false,
      home: BlocProvider(
        create: (context) => WeatherBloc(),
        child: Home(),
      ),
    );
  }
}
```

**第 8 步：**现在，让我们创建包含主 UI 的 home.dart。

**home.dart：**

```dart
import 'package:bloc_weather_example/bloc/weather_bloc.dart';
import 'package:bloc_weather_example/screens/weather_card.dart';
import 'package:flutter/material.dart';
import 'package:flutter_bloc/flutter_bloc.dart';
class Home extends StatefulWidget {
  Home({Key key}) : super(key: key);
@override
  _HomeState createState() => _HomeState();
}
class _HomeState extends State<Home> {
  TextEditingController _city = TextEditingController();
  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        Image.asset(
          'assets/bg.jpg',
          fit: BoxFit.cover,
        ),
        Scaffold(
          backgroundColor: Colors.transparent,
          body: BlocBuilder<WeatherBloc, WeatherState>(
            builder: (BuildContext context, state) {
              if (state is WeatherLoadingState) {
                print('Weather Loading State');
                return Center(
                  child: CircularProgressIndicator(),
                );
              }
              return Center(
                child: Column(
                  mainAxisAlignment: MainAxisAlignment.center,
                  children: [
                    state is WeatherLoadedState
                        ? WeatherCard(
                            state: state,
                          )
                        : Container(),
                    SizedBox(
                      height: 30.0,
                    ),
                    Padding(
                      padding: const EdgeInsets.all(8.0),
                      child: TextField(
                        controller: _city,
                        style: TextStyle(color: Colors.black, fontSize: 20.0),
                        decoration: InputDecoration(
                          fillColor: Colors.white70,
                          filled: true,
                          hintText: 'Enter City Name',
                          hintStyle: TextStyle(color: Colors.black26),
                          prefixIcon: Icon(Icons.home),
                          border: OutlineInputBorder(
                            borderRadius: BorderRadius.all(
                              Radius.circular(20.0),
                            ),
                            borderSide: BorderSide(
                              color: Colors.white,
                              width: 2.0,
                            ),
                          ),
                        ),
                        keyboardType: TextInputType.name,
                      ),
                    ),
                    SizedBox(
                      height: 20.0,
                    ),
                    Container(
                      height: 50.0,
                      width: 200.0,
                      child: RaisedButton(
                        child: Text(
                          'Check Weather!',
                          style: TextStyle(color: Colors.white),
                        ),
                        color: Colors.blue.shade400,
                        shape: RoundedRectangleBorder(
                          borderRadius: BorderRadius.all(
                            Radius.circular(20.0),
                          ),
                        ),
                        onPressed: () async {
                          context.read<WeatherBloc>().add(
                                WeatherGetEvent(city: _city.text),
                              );
                        },
                      ),
                    ),
                  ],
                ),
              );
            },
          ),
        ),
      ],
    );
  }
}
```

在这里，我们用 BlocBuilder 包装了我们的输入 UI。现在，如果状态是 WeatherLoadingState，我们将显示 CircularProgressIndicator()。否则，我们会显示整个 UI。获得 WeatherLoadedState 后，我们将显示自定义 WeatherCard 以显示详细信息。另一个主要的事情是我们按钮的 onPressed。当用户按下按钮时，我们按如下方式触发 WeatherGetEvent：

```dart
context.read<WeatherBloc>().add(WeatherGetEvent(city: _city.text));
```

上面的代码读取我们从 main.dart 传递的 WeatherBloc 的上下文，即这个小部件的父级，然后添加事件。或者，我们可以先在 initState() 中初始化我们的 WeatherBloc 对象，然后在 onPressed 中添加事件。

**第 9 步：**现在，让我们创建自定义的 WeatherCard。在 lib/screens 中创建一个名为 weather_card.dart 的文件。您始终可以拥有自己的 UI。
**weather_card.dart:**

```dart
import 'package:bloc_weather_example/bloc/weather_bloc.dart';
import 'package:bloc_weather_example/screens/constants.dart';
import 'package:flutter/material.dart';
class WeatherCard extends StatelessWidget {
  final WeatherLoadedState state;
  WeatherCard({this.state});
  @override
  Widget build(BuildContext context) {
    return Card(
      shape: RoundedRectangleBorder(
        side: new BorderSide(color: Colors.blue, width: 3.0),
        borderRadius: BorderRadius.circular(10.0),
      ),
      color: Colors.white60,
      elevation: 10.0,
      shadowColor: Colors.yellow,
      child: Padding(
        padding: const EdgeInsets.all(8.0),
        child: Column(
          children: [
            Text(
              'City: ' + state.weather.city.toString() + ' 🏡',
              style: weatherCardTextStyle,
            ),
            SizedBox(
              height: 10.0,
            ),
            Text(
              'Current Temperature: ' + state.weather.temperature.toString(),
              style: weatherCardTextStyle,
            ),
            SizedBox(
              height: 10.0,
            ),
            Text(
              'Today\'s Maximum: ' + state.weather.todayMax.toString(),
              style: weatherCardTextStyle,
            ),
            SizedBox(
              height: 10.0,
            ),
            Text(
              'Today\'s Minimum: ' + state.weather.todayMin.toString(),
              style: weatherCardTextStyle,
            ),
            SizedBox(
              height: 10.0,
            ),
            Text(
              'Yesterday\'s Maximum: ' + state.weather.yesterdayMax.toString(),
              style: weatherCardTextStyle,
            ),
            SizedBox(
              height: 10.0,
            ),
            Text(
              'Yesterday\'s Minimum: ' + state.weather.yesterdayMin.toString(),
              style: weatherCardTextStyle,
            ),
            SizedBox(
              height: 10.0,
            ),
          ],
        ),
      ),
    );
  }
}
```

在这里，我们可以使用我们的 state 对象访问从 API 获取的参数。我使用了自定义样式 weatherCardTextStyle 并将其放置在 lib/screens/constants.dart 下。这是一个很好的做法，因为到处都在使用相同的样式。

```dart
TextStyle weatherCardTextStyle = TextStyle(
  color: Colors.black,
  fontSize: 20.0,
);
```

**第 10 步：运行您的应用程序😍**

我们刚刚在我们的天气应用程序中实现了 Bloc 💙