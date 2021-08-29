---
title: "Flutter实现多主题"
date: 2021-08-21T20:34:32+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍

<!--more-->

getx

https://medium.com/flutter-community/the-flutter-getx-ecosystem-dependency-injection-8e763d0ec6b9

http://www.androidcoding.in/

# 首先我们不用任何状态管理pckage

## **main.dart**

```dart
import 'package:flutter/material.dart';
import 'package:flutter_themes_demo/themes.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatefulWidget {
  MyApp({Key key}) : super(key: key);

  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  @override
  void initState() {
    super.initState();
    currentTheme.addListener(() {
      setState(() {});
    });
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: MyHomePage(title: 'Flutter Theme Demo'),
      title: 'Flutter Theme Demo',
      theme: CustomTheme.lightTheme,
      darkTheme: CustomTheme.darkTheme,
      themeMode: currentTheme.currentTheme,
    );
  }
}

class MyHomePage extends StatefulWidget {
  MyHomePage({Key key, this.title}) : super(key: key);
  final String title;

  @override
  _MyHomePageState createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  @override
  Widget build(BuildContext context) {
    final theme = Theme.of(context);

    return Scaffold(
      appBar: AppBar(
        title: Text(
          widget.title,
          style: TextStyle(
            color: theme.accentColor,
          ),
        ),
        actions: [
          IconButton(
            icon: const Icon(Icons.brightness_4_rounded),
            onPressed: () {
              currentTheme.toggleTheme();
            },
          ),
        ],
      ),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: <Widget>[
            Text(
              'Fluter Themes Demo',
            ),
          ],
        ),
      ),
    );
  }
}
```

## **themes.dart**

```dart
import 'package:flutter/material.dart';

CustomTheme currentTheme = CustomTheme();

class CustomTheme with ChangeNotifier {
  static bool _isDarkTheme = false;
  ThemeMode get currentTheme => _isDarkTheme ? ThemeMode.dark : ThemeMode.light;

  void toggleTheme() {
    _isDarkTheme = !_isDarkTheme;
    notifyListeners();
  }

  static ThemeData get lightTheme {
    return ThemeData(
      primaryColor: Colors.lightBlue,
      accentColor: Colors.white,
      backgroundColor: Colors.white,
      scaffoldBackgroundColor: Colors.white,
      textTheme: TextTheme(
        headline1: TextStyle(color: Colors.black),
        headline2: TextStyle(color: Colors.black),
        bodyText1: TextStyle(color: Colors.black),
        bodyText2: TextStyle(color: Colors.black),
      ),
    );
  }

  static ThemeData get darkTheme {
    return ThemeData(
      primaryColor: Colors.black,
      accentColor: Colors.red,
      backgroundColor: Colors.grey,
      scaffoldBackgroundColor: Colors.grey,
      textTheme: TextTheme(
        headline1: TextStyle(color: Colors.white),
        headline2: TextStyle(color: Colors.white),
        bodyText1: TextStyle(color: Colors.white),
        bodyText2: TextStyle(color: Colors.white),
      ),
    );
  }
}
```



# 第二种方式 provider

先看我们的代码结构

![image-20210821204534589](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821204534589.png)



## main.dart

```dart
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:provider/provider.dart';
import 'package:theme_example/page/home_page.dart';
import 'package:theme_example/provider/theme_provider.dart';

Future main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await SystemChrome.setPreferredOrientations([
    DeviceOrientation.portraitUp,
    DeviceOrientation.portraitDown,
  ]);

  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  static const String title = 'Light & Dark Theme';

  @override
  Widget build(BuildContext context) => ChangeNotifierProvider(
        create: (context) => ThemeProvider(),
        builder: (context, _) {
          final themeProvider = Provider.of<ThemeProvider>(context);

          return MaterialApp(
            title: title,
            themeMode: themeProvider.themeMode,
            theme: MyThemes.lightTheme,
            darkTheme: MyThemes.darkTheme,
            home: HomePage(),
          );
        },
      );
}
```

## provider文件夹

**theme_provider.dart**

```dart
import 'package:flutter/material.dart';
import 'package:flutter/scheduler.dart';

class ThemeProvider extends ChangeNotifier {
  ThemeMode themeMode = ThemeMode.system;

  bool get isDarkMode {
    if (themeMode == ThemeMode.system) {
      final brightness = SchedulerBinding.instance.window.platformBrightness;
      return brightness == Brightness.dark;
    } else {
      return themeMode == ThemeMode.dark;
    }
  }

  void toggleTheme(bool isOn) {
    themeMode = isOn ? ThemeMode.dark : ThemeMode.light;
    notifyListeners();
  }
}

class MyThemes {
  static final darkTheme = ThemeData(
    scaffoldBackgroundColor: Colors.grey.shade900,
    primaryColor: Colors.black,
    colorScheme: ColorScheme.dark(),
    iconTheme: IconThemeData(color: Colors.purple.shade200, opacity: 0.8),
  );

  static final lightTheme = ThemeData(
    scaffoldBackgroundColor: Colors.white,
    primaryColor: Colors.white,
    colorScheme: ColorScheme.light(),
    iconTheme: IconThemeData(color: Colors.red, opacity: 0.8),
  );
}
```

## page文件夹

### **home_page.dart**

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'package:theme_example/main.dart';
import 'package:theme_example/page/profile_widget.dart';
import 'package:theme_example/provider/theme_provider.dart';
import 'package:theme_example/widget/change_theme_button_widget.dart';

import 'navigationbar_widget.dart';

class HomePage extends StatelessWidget {
  // @override
  // Widget build(BuildContext context) => Scaffold(
  //       appBar: AppBar(
  //         iconTheme: Theme.of(context).iconTheme,
  //         backgroundColor: Colors.transparent,
  //         leading: Icon(Icons.menu),
  //         title: Text(MyApp.title),
  //         elevation: 0,
  //         actions: [
  //           ChangeThemeButtonWidget(),
  //         ],
  //       ),
  //       body: ProfileWidget(),
  //       extendBody: true,
  //       bottomNavigationBar: NavigationBarWidget(),
  //     );

  @override
  Widget build(BuildContext context) {
    final text = Provider.of<ThemeProvider>(context).themeMode == ThemeMode.dark
        ? 'DarkTheme'
        : 'LightTheme';

    return Scaffold(
      appBar: AppBar(
        backgroundColor: Colors.orange,
        title: Text(MyApp.title),
        actions: [
          ChangeThemeButtonWidget(),
        ],
      ),
      body: Center(
        child: Text(
          'Hello $text!',
          style: TextStyle(
            fontSize: 32,
            fontWeight: FontWeight.bold,
          ),
        ),
      ),
    );
  }
}
```



### **navigationbar_widget.dart**

```dart
import 'package:flutter/material.dart';

class NavigationBarWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) => Container(
        margin: const EdgeInsets.symmetric(vertical: 12, horizontal: 16),
        child: Container(
          height: 60,
          decoration: BoxDecoration(
            boxShadow: [
              BoxShadow(color: Colors.grey),
            ],
            color: Theme.of(context).primaryColor,
            borderRadius: BorderRadius.circular(10),
          ),
          child: Row(
            mainAxisAlignment: MainAxisAlignment.spaceEvenly,
            children: [
              Icon(Icons.home_rounded),
              Icon(Icons.search),
              Icon(Icons.camera_alt),
              Icon(Icons.notifications),
              Icon(Icons.person)
            ],
          ),
        ),
      );
}
```



### **profile_widget.dart**

```dart
import 'package:flutter/material.dart';
import 'package:flutter_staggered_grid_view/flutter_staggered_grid_view.dart';

class ProfileWidget extends StatelessWidget {
  const ProfileWidget({
    Key key,
  }) : super(key: key);

  @override
  Widget build(BuildContext context) => ListView(
        physics: BouncingScrollPhysics(),
        children: [
          buildProfile(context),
          Divider(height: 32),
          buildPhotos(context),
        ],
      );

  Widget buildPhotos(BuildContext context) => Container(
        padding: EdgeInsets.symmetric(horizontal: 12),
        child: Column(
          children: [
            Align(
              alignment: Alignment.centerLeft,
              child: Text(
                'PHOTOS',
                style: TextStyle(
                  color: Theme.of(context).iconTheme.color,
                  fontSize: 16,
                  fontWeight: FontWeight.bold,
                ),
              ),
            ),
            SizedBox(height: 12),
            buildPhotoGrid(),
          ],
        ),
      );

  Widget buildPhotoGrid() => StaggeredGridView.countBuilder(
        primary: false,
        shrinkWrap: true,
        crossAxisCount: 4,
        mainAxisSpacing: 8,
        crossAxisSpacing: 8,
        itemCount: 8,
        itemBuilder: (BuildContext context, int index) {
          final urlImage = 'https://source.unsplash.com/random?sig=$index';

          return Container(
            decoration: BoxDecoration(
              borderRadius: BorderRadius.circular(10),
              image: DecorationImage(
                image: NetworkImage(urlImage),
                fit: BoxFit.cover,
              ),
            ),
          );
        },
        staggeredTileBuilder: (int index) =>
            StaggeredTile.count(2, index.isEven ? 2 : 1),
      );

  Widget buildProfile(BuildContext context) => Column(
        children: [
          SizedBox(height: 20),
          CircleAvatar(
            radius: 46,
            backgroundColor: Theme.of(context).iconTheme.color,
            child: CircleAvatar(
              radius: 44,
              backgroundColor: Theme.of(context).scaffoldBackgroundColor,
              child: CircleAvatar(
                child: Image.asset('assets/user1.png'),
                backgroundColor: Colors.pink,
                radius: 40,
              ),
            ),
          ),
          SizedBox(height: 20),
          Text(
            'Jane Doe',
            style: TextStyle(
              fontSize: 24,
              fontWeight: FontWeight.bold,
            ),
          ),
          SizedBox(height: 8),
          Row(
            mainAxisAlignment: MainAxisAlignment.center,
            children: [
              Icon(Icons.location_on),
              const SizedBox(width: 4),
              Text('New York, USA'),
            ],
          ),
          SizedBox(height: 16),
          Text('Photography is the story I fail to put into words'),
          SizedBox(height: 16),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceAround,
            children: [
              buildCounters(context, '36', 'Posts'),
              buildCounters(context, '3.5K', 'Following'),
              buildCounters(context, '15K', 'Followers'),
            ],
          ),
        ],
      );

  Widget buildCounters(context, String firstLine, String secondLine) => Column(
        children: [
          Text(
            firstLine,
            style: TextStyle(
              fontSize: 20,
              fontWeight: FontWeight.bold,
            ),
          ),
          SizedBox(height: 8),
          Text(secondLine),
        ],
      );
}
```



## **pubspec.yaml**

```dart
dependencies:
  flutter:
    sdk: flutter

  cupertino_icons: ^1.0.0

  # display photos in grid
  flutter_staggered_grid_view: ^0.3.3

  # state management
  provider: ^4.3.2+3

```



# 第三种Flutter 中应用主题、创建自定义主题

https://www.youtube.com/watch?v=ju1fr9C4g4U

![image-20210821205408798](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821205408798.png)

## **main.dart**

```dart
import 'package:flutter/material.dart';
import 'package:flutterfetchtaxiapp/utils/theme.dart';
import 'ui/screens/homescreen.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Flutter Taxi App',
      // theme: ThemeData(
      //   primaryColor: Color(0xffdd6b3d),
      // ),
      theme: basicTheme(),
      home: HomeScreen(),
    );
  }
}
```



## **Listviewdetailsscreen.dart**

```dart
import 'package:flutter/material.dart';
import 'package:flutterfetchtaxiapp/model/carservices.dart';
import 'package:font_awesome_flutter/font_awesome_flutter.dart';
import 'package:flutterfetchtaxiapp/utils/widgets/starWidget.dart';
import 'package:flutterfetchtaxiapp/utils/widgets/profilewidget.dart';

class ListViewDetailsScreen extends StatefulWidget {
  ListViewDetailsScreen({Key key, this.carServices}) : super(key: key);
  final CarServices carServices;

  _ListViewDetailsScreenState createState() =>
      _ListViewDetailsScreenState(carServices: this.carServices);
}

class _ListViewDetailsScreenState extends State<ListViewDetailsScreen> {
  CarServices carServices;
  Color _colorVal;

  _ListViewDetailsScreenState({this.carServices});

  void _favServices() {
    setState(() {
      _colorVal = Colors.red;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        actions: <Widget>[
          Row(
            children: <Widget>[
              IconButton(
                icon: Icon(
                  FontAwesomeIcons.solidHeart,
                  size: 30.0,
                  color: _colorVal,
                ),
                onPressed: () {
                 _favServices();
                },
              ),
              SizedBox(
                width: 25.0,
              ),
              Icon(
                FontAwesomeIcons.pen,
                size: 25.0,
                color: Theme.of(context).buttonColor,
              ),
              SizedBox(
                width: 5.0,
              ),
            ],
          )
        ],
        elevation: 0,
      ),
      body: Column(
        children: <Widget>[
          Container(
              color: Theme.of(context).primaryColor,
              width: MediaQuery.of(context).size.width,
              height: 300.0,
              child: Column(
                mainAxisAlignment: MainAxisAlignment.center,
                children: <Widget>[
                  Icon(
                    FontAwesomeIcons.carSide,
                    size: 120.0,
                    color: Theme.of(context).buttonColor,
                  ),
                  SizedBox(
                    height: 60.0,
                  ),
                  Padding(
                    padding: EdgeInsets.only(left: 16.0),
                    child: Column(
                      children: <Widget>[
                        Align(
                          alignment: Alignment.centerLeft,
                          child: Text(
                            carServices.carService,
                            style: Theme.of(context).textTheme.display1,
                          ),
                        ),
                        SizedBox(height: 5.0),
                        StarWidget(
                          numberOfStars:
                              int.parse(carServices.carRate.toString()),
                        ),
                      ],
                    ),
                  ),
                ],
              )),
          Card(
            elevation: 3.0,
            child: Padding(
              padding: EdgeInsets.only(top: 16.0, bottom: 16.0),
              child: ListTile(
                title: Row(
                  children: <Widget>[
                    Expanded(
                      child: Column(
                        children: <Widget>[
                          Align(
                            alignment: Alignment.centerLeft,
                            child: Text(
                              '+123 456 7890',
                              style: Theme.of(context).textTheme.display2,
                            ),
                          ),
                          SizedBox(height: 5.0),
                          Align(
                              alignment: Alignment.centerLeft,
                              child: Text('Office')),
                        ],
                      ),
                    ),
                    IconButton(
                      icon: Icon(
                        FontAwesomeIcons.phone,
                        color: Theme.of(context).textTheme.title.color,
                      ),
                      onPressed: () {
                        //
                      },
                    ),
                  ],
                ),
              ),
            ),
          ),
          ProfileWidget(),
        ],
      ),
    );
  }
}
```



## **homescreen.dart**

```dart
import 'package:flutter/material.dart';
import 'package:font_awesome_flutter/font_awesome_flutter.dart';
import 'package:flutterfetchtaxiapp/utils/widgets/serviceswidget.dart';
import 'package:flutterfetchtaxiapp/utils/widgets/favouritewidget.dart';


class HomeScreen extends StatefulWidget {
  HomeScreen({Key key}) : super(key: key);

  _HomeScreenState createState() => _HomeScreenState();
}

class _HomeScreenState extends State<HomeScreen> {

  int _currentIndex = 0;
 final List<Widget> _children = [
   ServicesWidget(),
   FavouriteWidget()
 ];

  @override
  Widget build(BuildContext context) {
    // Build the content depending on the state:
    return Scaffold(
     appBar: AppBar(
       leading: Icon(FontAwesomeIcons.bars,
        color: Theme.of(context).iconTheme.color,
       ),
       title: Text('Fetch Taxi'),
     ),
     body: _children[_currentIndex], // new
     bottomNavigationBar: BottomNavigationBar(
       onTap: onTabTapped, // new
       currentIndex: _currentIndex, 
       items: [
         BottomNavigationBarItem(
           icon: new Icon(FontAwesomeIcons.car),
           title: new Text('Services'),
         ),
         BottomNavigationBarItem(
           icon: new Icon(FontAwesomeIcons.solidHeart),
           title: new Text('Favourite'),
         ),
       ],
     ),
   );
  }
  void onTabTapped(int index) {
   setState(() {
     _currentIndex = index;
   });
 }
}
```



## **carservices.dart**

```dart
class CarServices{

  final int carId;
   String carService;
   int carRate;

  CarServices({this.carId,this.carService,this.carRate});
  factory CarServices.fromJson(Map<String, dynamic> json) {
    return CarServices(
      carId: json['car_id'] as int,
      carService: json['car_service'] as String,
      carRate: json['car_rate'] as int,
    );
  }
}
```



# 第四种方式package  stacked_themes

本教程将介绍如何在堆叠式架构中实现多个主题。它是 Theme Manager Snippet 的一个实现，它使用纯提供程序作为主题管理器。我们正在使用该教程中显示的完全相同的原则。如果您只想使用 provider 构建主题管理器，那么您可以查看它。要继续下载本指南中将使用[的起始代码，请点击此处](https://firebasestorage.googleapis.com/v0/b/filledstacks.appspot.com/o/tutorials%2F056%2F056-starting.zip?alt=media&token=7b4ea964-dc93-43aa-95c2-f0f6f19f1dc0)。

https://www.filledstacks.com/post/multiple-themes-in-flutter-dark-and-light-theme-flutter-stacked/

## 设置和堆叠主题

我们首先将stacked_themes 包添加到pubspec.yaml 文件中

```yaml
dependencies: ...
  stacked_themes:
```

这个包提供了一些基本的类，以便在使用 stack 构建应用程序时使主题管理更容易。stacked_themes 为您提供了交换提供给您的应用程序的 ThemeData 的基本功能，可以使用`Theme.of(context)`. 除此之外，它还为您提供了一个帮助功能，您可以在其中更改状态栏颜色。让我们来看看代码。

## 用法

该软件包使用 a`ThemeManager`来管理所有主题功能。首先，我们必须`ThemeManager`在应用程序启动之前调用 的初始化函数。更改您的主函数以返回 a`Future`并使其异步并等待初始化静态函数调用`ThemeManager`。

```dart
Future main() async {
  await ThemeManager.initialise();
  runApp(MyApp());
}
```

### 主题生成器

我们通过包装我们的开始`MaterialApp`或任何其他`App`与我们的`ThemeBuilder`。builder 函数返回一个上下文、regularTheme、darkTheme 和一个 themeMode。我们将此提供给`MaterialApp`.

```dart
class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return ThemeBuilder(
      builder: (context, regularTheme, darkTheme, themeMode) => MaterialApp(
        title: 'Flutter Demo',
        theme: regularTheme,
        darkTheme: darkTheme,
        themeMode: themeMode,
        ...
      ),
    );
  }
}
```

这将重建您的应用程序并通过上下文传递一个新主题供您在应用程序中使用。

### 提供多个主题

该`ThemeBuilder`有一个属性叫做主题，在那里你可以提供的列表`ThemeData`。这通常只有 2 或 3 个，浅色、深色、中性色，但我们将其列在一个列表中，这样您就可以拥有任意数量的。为了保持整洁，我们将从在不同文件中创建的函数返回主题列表。创建一个名为`theme_setup.dart`. 您可以将其放置在 UI 文件夹的根位置或将其保留在 lib 的根目录中。我还没有决定哪里最好，所以这取决于你

```dart
List<ThemeData> getThemes() {
  return [
    ThemeData(backgroundColor: Colors.blue, accentColor: Colors.yellow),
    ThemeData(backgroundColor: Colors.white, accentColor: Colors.green),
    ThemeData(backgroundColor: Colors.purple, accentColor: Colors.green),
    ThemeData(backgroundColor: Colors.black, accentColor: Colors.red),
    ThemeData(backgroundColor: Colors.red, accentColor: Colors.blue),
  ];
}
```

这是一个非常简单的功能，但考虑到您将在主题中使用多少主题属性，它可能会开始看起来很忙。这就是为什么我建议您将其保存在单独的文件中。返回您的 main.dart 文件并为 .dart`getThemes()`的`themes`属性提供值`ThemeBuilder`。

```dart
Widget build(BuildContext context) {
  return ThemeBuilder(
    themes: getThemes(),
    builder: (context, regularTheme, darkTheme, themeMode) => MaterialApp(
      title: 'Flutter Demo',
      theme: regularTheme,
      darkTheme: darkTheme,
      themeMode: themeMode,
      ...
    ),
  );
}
```

我们在主题之间切换的方式是设置我们要使用的主题的索引。它将从列表中的第一个主题开始。要将主题更新为任何其他主题，您只需在 UI 中调用`getThemeManager(context)`获取`ThemeManager`然后调用`selectThemeAtIndex(1)`它即可。这是在提供的主题列表中选择第二个主题的代码。

```dart
getThemeManager(context)
  .selectThemeAtIndex(1);
```

###  Light 和 Dark 主题

如果您只想使用 Light 和 Dark 主题，那么您可以将其提供给`ThemeBuilder`而不是提供多个主题。在提供 Light 和 Dark 模式时，您还可以提供 defaultThemeMode，这是`ThemeMode.system`默认的。当您离开时，您是`system`在告诉`ThemeManager`您希望系统确定您使用的是亮模式还是暗模式。这就是您提供深色和浅色主题的方式。

```dart
Widget build(BuildContext context) {
  return ThemeBuilder(
    defaultThemeMode: ThemeMode.light,
    darkTheme: ThemeData(
      brightness: Brightness.dark,
      backgroundColor: Colors.blue[700],
      accentColor: Colors.yellow[700],
    ),
    lightTheme: ThemeData(
      brightness: Brightness.light,
      backgroundColor: Colors.blue[300],
      accentColor: Colors.yellow[300],
    ),
    builder: (context, regularTheme, darkTheme, themeMode) => MaterialApp(
      title: 'Flutter Demo',
      theme: regularTheme,
      darkTheme: darkTheme,
      themeMode: themeMode,
      ...
    ),
  );
}
```

当`defaultThemeMode`不是系统时，如上例所示，您可以使用该`toggleDarkLightTheme`功能在明暗主题之间切换。

```dart
getThemeManager(context)
  .toggleDarkLightTheme();
```

### 更改状态栏颜色

有时，当您有特定主题时，您也希望状态栏颜色也发生变化。大多数应用程序将此透明设置为使用背景颜色。如果您的应用程序要求您更改状态栏颜色，那么也有相应的功能。该`ThemeBuilder`有一个属性`statusBarColorBuilder`它是希望你返回的功能`Color`。它为您提供了要选择的主题，您可以使用它来返回要为状态栏显示的颜色。

```dart
Widget build(BuildContext context) {
  return ThemeBuilder(
    statusBarColorBuilder: (theme) => theme.accentColor,
    lightTheme: ThemeData(...),
    darkTheme: ThemeData(...),
    ...
  );
}
```

在上面的例子中，我们返回主题的重音颜色作为状态栏颜色。此功能将自动检查状态文本是否可见，并将根据背景值强度（与白色/黑色的接近程度）将文本从白色更改为黑色。

### 主题持久化]

如果用户在应用程序的生命周期内更改了任何主题，则在应用程序再次启动时将自动应用上次选择的主题。此功能是通过使用[shared_preferences](https://pub.dev/packages/shared_preferences)包创建的。

## 使用主题

现在您已经设置了所有主题功能，让我们看看如何在您的 UI 中使用它。首先要知道的是，您可以以正常方式访问主题数据。

```dart
var theme = Theme.of(context);
```

这意味着您的 Flutter 代码的其余部分仍然保持不变。

### 业务逻辑中的访问

如你所知。我也坚持的规则之一是，不应在任何业务逻辑中直接使用 UI 相关代码或类。出于这个原因，还有一个`ThemeService`允许您调用在主题之间更改所需的相同功能。此服务应用作您过去使用过的任何其他stacked_services。首先我们用定位器注册它。

```dart
locator.registerSingleton(ThemeService.getInstance());

// or add it to your third_party_services_module if you’re using injectable
```

然后`ViewModel`通过从`locator`. 就是这样。这就是所需的所有功能。如果您想更多地讨论这个问题，请查看 slack 并在那里提出您的问题。如果您想请求一些附加功能或更改，请不要犹豫，





# 第五种方式 provider

![image-20210821210815695](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210821210815695.png)

## main.dart

```dart
import 'package:flutter/material.dart';
import 'package:hive/hive.dart';
import 'package:light_dark_theme_flutter/models_providers/theme_provider.dart';
import 'package:light_dark_theme_flutter/pages/home_page.dart';
import 'package:path_provider/path_provider.dart' as pathProvider;
import 'package:provider/provider.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  final appDocumentDirectory =
      await pathProvider.getApplicationDocumentsDirectory();

  Hive.init(appDocumentDirectory.path);

  final settings = await Hive.openBox('settings');
  bool isLightTheme = settings.get('isLightTheme') ?? false;

  print(isLightTheme);

  runApp(ChangeNotifierProvider(
    create: (_) => ThemeProvider(isLightTheme: isLightTheme),
    child: AppStart(),
  ));
}

// to ensure we have the themeProvider before the app starts lets make a few more changes.

class AppStart extends StatelessWidget {
  const AppStart({Key key}) : super(key: key);

  @override
  Widget build(BuildContext context) {
    ThemeProvider themeProvider = Provider.of<ThemeProvider>(context);
    return MyApp(
      themeProvider: themeProvider,
    );
  }
}

class MyApp extends StatefulWidget with WidgetsBindingObserver {
  final ThemeProvider themeProvider;

  const MyApp({Key key, @required this.themeProvider}) : super(key: key);
  @override
  _MyAppState createState() => _MyAppState();
}

class _MyAppState extends State<MyApp> {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      debugShowCheckedModeBanner: false,
      title: 'Light Dark Theme',
      theme: widget.themeProvider.themeData(),
      home: HomePage(),
    );
  }
}
```



## **home_page.dart**

```dart
import 'package:flutter/material.dart';
import 'package:light_dark_theme_flutter/components/z_animated_toggle.dart';
import 'package:light_dark_theme_flutter/models_providers/theme_provider.dart';
import 'package:provider/provider.dart';

class HomePage extends StatefulWidget {
  HomePage({Key key}) : super(key: key);

  @override
  _HomePageState createState() => _HomePageState();
}

class _HomePageState extends State<HomePage>
    with SingleTickerProviderStateMixin {
  AnimationController _animationController;
  GlobalKey<ScaffoldState> _scaffoldKey = GlobalKey<ScaffoldState>();

  @override
  void initState() {
    _animationController =
        AnimationController(vsync: this, duration: Duration(milliseconds: 800));
    super.initState();
  }

  // function to toggle circle animation
  changeThemeMode(bool theme) {
    if (!theme) {
      _animationController.forward(from: 0.0);
    } else {
      _animationController.reverse(from: 1.0);
    }
  }

  @override
  Widget build(BuildContext context) {
    double height = MediaQuery.of(context).size.height;
    double width = MediaQuery.of(context).size.width;
    // Now we have access to the theme properties
    final themeProvider = Provider.of<ThemeProvider>(context);
    return Scaffold(
      key: _scaffoldKey,
      body: SafeArea(
        child: Container(
          alignment: Alignment.center,
          margin: EdgeInsets.only(top: height * 0.1),
          child: Column(
            children: [
              Stack(
                children: [
                  Container(
                    width: width * 0.35,
                    height: width * 0.35,
                    decoration: BoxDecoration(
                      shape: BoxShape.circle,
                      gradient: LinearGradient(
                          colors: themeProvider.themeMode().gradient,
                          begin: Alignment.bottomLeft,
                          end: Alignment.topRight),
                    ),
                  ),
                  Transform.translate(
                    offset: Offset(40, 0),
                    child: ScaleTransition(
                      scale: _animationController.drive(
                        Tween<double>(begin: 0.0, end: 1.0).chain(
                          CurveTween(curve: Curves.decelerate),
                        ),
                      ),
                      alignment: Alignment.topRight,
                      child: Container(
                        width: width * .26,
                        height: width * .26,
                        decoration: BoxDecoration(
                            shape: BoxShape.circle,
                            color: themeProvider.isLightTheme
                                ? Colors.white
                                : Color(0xFF26242e)),
                      ),
                    ),
                  )
                ],
              ),
              SizedBox(height: height * 0.1),
              Text(
                'Choose a style',
                style: TextStyle(
                    fontSize: width * .06, fontWeight: FontWeight.bold),
              ),
              SizedBox(height: height * 0.03),
              Container(
                width: width * .6,
                child: Text(
                  'Pop or subtle. Day or night. Customize your interface',
                  textAlign: TextAlign.center,
                ),
              ),
              SizedBox(height: height * 0.1),
              ZAnimatedToggle(
                values: ['Light', 'Dark'],
                onToggleCallback: (v) async {
                  await themeProvider.toggleThemeData();
                  setState(() {});
                  changeThemeMode(themeProvider.isLightTheme);
                },
              ),
              SizedBox(
                height: height * .05,
              ),
              Row(
                mainAxisAlignment: MainAxisAlignment.center,
                children: <Widget>[
                  buildDot(
                    width: width * 0.022,
                    height: width * 0.022,
                    color: const Color(0xFFd9d9d9),
                  ),
                  buildDot(
                    width: width * 0.055,
                    height: width * 0.022,
                    color: themeProvider.isLightTheme
                        ? Color(0xFF26242e)
                        : Colors.white,
                  ),
                  buildDot(
                    width: width * 0.022,
                    height: width * 0.022,
                    color: const Color(0xFFd9d9d9),
                  ),
                ],
              ),
              // skip & next
              Expanded(
                child: Container(
                  margin: EdgeInsets.symmetric(
                      vertical: height * 0.02, horizontal: width * 0.04),
                  alignment: Alignment.bottomCenter,
                  child: Row(
                    mainAxisAlignment: MainAxisAlignment.spaceBetween,
                    children: <Widget>[
                      Padding(
                        padding:
                            EdgeInsets.symmetric(horizontal: width * 0.025),
                        child: Text(
                          'Skip',
                          style: TextStyle(
                            fontSize: width * 0.045,
                            color: const Color(0xFF7c7b7e),
                            fontFamily: 'Rubik',
                          ),
                        ),
                      ),
                      RaisedButton(
                        onPressed: () {
                          _scaffoldKey.currentState.showSnackBar(
                            SnackBar(
                              content: Text(
                                'Loved it? Give a star on Github',
                                textAlign: TextAlign.center,
                                style: TextStyle(
                                  fontFamily: 'Rubik',
                                  fontSize: width * 0.045,
                                ),
                              ),
                            ),
                          );
                        },
                        shape: CircleBorder(),
                        color: themeProvider.isLightTheme
                            ? const Color(0xFFFFFFFF)
                            : const Color(0xFF35303f),
                        child: Padding(
                          padding: EdgeInsets.all(width * 0.05),
                          child: Icon(
                            Icons.arrow_forward,
                            color: themeProvider.isLightTheme
                                ? const Color(0xFF000000)
                                : const Color(0xFFFFFFFF),
                          ),
                        ),
                      )
                    ],
                  ),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }

// for drawing the dots
  Container buildDot({double width, double height, Color color}) {
    return Container(
      margin: EdgeInsets.symmetric(horizontal: 4),
      width: width,
      height: height,
      decoration: ShapeDecoration(
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(20),
        ),
        color: color,
      ),
    );
  }
}

// #time for finishing touches! I
```



## **theme_provider.dart**

```dart
// we use provider to manage the app state

import 'package:flutter/material.dart';
import 'package:flutter/services.dart';
import 'package:hive/hive.dart';

class ThemeProvider with ChangeNotifier {
  bool isLightTheme;

  ThemeProvider({this.isLightTheme});

  // the code below is to manage the status bar color when the theme changes
  getCurrentStatusNavigationBarColor() {
    if (isLightTheme) {
      SystemChrome.setSystemUIOverlayStyle(SystemUiOverlayStyle(
        statusBarColor: Colors.transparent,
        statusBarBrightness: Brightness.light,
        statusBarIconBrightness: Brightness.dark,
        systemNavigationBarColor: Color(0xFFFFFFFF),
        systemNavigationBarIconBrightness: Brightness.dark,
      ));
    } else {
      SystemChrome.setSystemUIOverlayStyle(SystemUiOverlayStyle(
        statusBarColor: Colors.transparent,
        statusBarBrightness: Brightness.dark,
        statusBarIconBrightness: Brightness.light,
        systemNavigationBarColor: Color(0xFF26242e),
        systemNavigationBarIconBrightness: Brightness.light,
      ));
    }
  }

  // use to toggle the theme
  toggleThemeData() async {
    final settings = await Hive.openBox('settings');
    settings.put('isLightTheme', !isLightTheme);
    isLightTheme = !isLightTheme;
    getCurrentStatusNavigationBarColor();
    notifyListeners();
  }

  // Global theme data we are always check if the light theme is enabled #isLightTheme
  ThemeData themeData() {
    return ThemeData(
      visualDensity: VisualDensity.adaptivePlatformDensity,
      primarySwatch: isLightTheme ? Colors.grey : Colors.grey,
      primaryColor: isLightTheme ? Colors.white : Color(0xFF1E1F28),
      brightness: isLightTheme ? Brightness.light : Brightness.dark,
      backgroundColor: isLightTheme ? Color(0xFFFFFFFF) : Color(0xFF26242e),
      scaffoldBackgroundColor:
          isLightTheme ? Color(0xFFFFFFFF) : Color(0xFF26242e),
    );
  }

  // Theme mode to display unique properties not cover in theme data
  ThemeColor themeMode() {
    return ThemeColor(
      gradient: [
        if (isLightTheme) ...[Color(0xDDFF0080), Color(0xDDFF8C00)],
        if (!isLightTheme) ...[Color(0xFF8983F7), Color(0xFFA3DAFB)]
      ],
      textColor: isLightTheme ? Color(0xFF000000) : Color(0xFFFFFFFF),
      toggleButtonColor: isLightTheme ? Color(0xFFFFFFFF) : Color(0xFf34323d),
      toggleBackgroundColor:
          isLightTheme ? Color(0xFFe7e7e8) : Color(0xFF222029),
      shadow: [
        if (isLightTheme)
          BoxShadow(
              color: Color(0xFFd8d7da),
              spreadRadius: 5,
              blurRadius: 10,
              offset: Offset(0, 5)),
        if (!isLightTheme)
          BoxShadow(
              color: Color(0x66000000),
              spreadRadius: 5,
              blurRadius: 10,
              offset: Offset(0, 5))
      ],
    );
  }
}

// A class to manage specify colors and styles in the app not supported by theme data
class ThemeColor {
  List<Color> gradient;
  Color backgroundColor;
  Color toggleButtonColor;
  Color toggleBackgroundColor;
  Color textColor;
  List<BoxShadow> shadow;

  ThemeColor({
    this.gradient,
    this.backgroundColor,
    this.toggleBackgroundColor,
    this.toggleButtonColor,
    this.textColor,
    this.shadow,
  });
}
```



## **z_animated_toggle.dart**

```dart
import 'package:flutter/material.dart';
import 'package:light_dark_theme_flutter/models_providers/theme_provider.dart';
import 'package:provider/provider.dart';

class ZAnimatedToggle extends StatefulWidget {
  final List<String> values;
  final ValueChanged onToggleCallback;
  ZAnimatedToggle({
    Key key,
    @required this.values,
    @required this.onToggleCallback,
  }) : super(key: key);

  @override
  _ZAnimatedToggleState createState() => _ZAnimatedToggleState();
}

class _ZAnimatedToggleState extends State<ZAnimatedToggle> {
  @override
  Widget build(BuildContext context) {
    double width = MediaQuery.of(context).size.width;
    final themeProvider = Provider.of<ThemeProvider>(context);
    return Container(
      width: width * .7,
      height: width * .13,
      child: Stack(
        children: [
          GestureDetector(
            onTap: () {
              widget.onToggleCallback(1);
            },
            child: Container(
              width: width * .7,
              height: width * .13,
              decoration: ShapeDecoration(
                  color: themeProvider.themeMode().toggleBackgroundColor,
                  shape: RoundedRectangleBorder(
                      borderRadius: BorderRadius.circular(width * .1))),
              child: Row(
                children: List.generate(
                  widget.values.length,
                  (index) => Padding(
                    padding: EdgeInsets.symmetric(horizontal: width * .1),
                    child: Text(
                      widget.values[index],
                      style: TextStyle(
                          fontSize: width * .05,
                          fontWeight: FontWeight.bold,
                          color: Color(0xFF918f95)),
                    ),
                  ),
                ),
              ),
            ),
          ),
          AnimatedAlign(
            alignment: themeProvider.isLightTheme
                ? Alignment.centerLeft
                : Alignment.centerRight,
            duration: Duration(milliseconds: 350),
            curve: Curves.ease,
            child: Container(
              alignment: Alignment.center,
              width: width * .35,
              height: width * .13,
              decoration: ShapeDecoration(
                  color: themeProvider.themeMode().toggleButtonColor,
                  shadows: themeProvider.themeMode().shadow,
                  shape: RoundedRectangleBorder(
                      borderRadius: BorderRadius.circular(width * .1))),
              child: Text(
                themeProvider.isLightTheme
                    ? widget.values[0]
                    : widget.values[1],
                style: TextStyle(
                    fontSize: width * .045, fontWeight: FontWeight.bold),
              ),
            ),
          )
        ],
      ),
    );
  }
}
```



## **pubspec.yaml**

```dart
dependencies:
  flutter:
    sdk: flutter
  hive: ^1.4.1+1
  #used to store local state
  path_provider: ^1.6.5
  provider: ^4.3.1
   #used to manage global app state
  cupertino_icons: ^0.1.3
```



第二种方式 provider





# 第六gex

# 在 Flutter 中使用 GetX 动态更改您的应用主题

在本文中，您将逐步学习如何在没有任何样板代码的情况下获得动态深色主题

# Motivation



![img](https://miro.medium.com/max/375/1*1jKHA_05bXSK-uQ9-5C4JQ.gif)

## This article consists of two parts

- ## 本文由两部分组成

  - 第 1 部分：如何获得动态暗模式
  - 第 2 部分：如何使主题持久化

  此外，文章末尾还有一个示例项目。你可以检查一下

# Part 1: How to get dynamic dark mode

# 第 1 部分：如何获得动态暗模式

## 第 1 步：将包添加到您的 pubspec.yaml 文件中

[GetX](https://pub.dev/packages/get)就像一个微[框架](https://pub.dev/packages/get)，几乎可以为您完成所有工作。这次我们将使用它来将我们的应用程序主题更改为暗面。

```
dependencies:
  get: ^3.13.2
```

## 第 2 步：创建明暗主题

在这种情况下，我们只有 2 个主题。



```
class Themes {
  static final light = ThemeData.light().copyWith(
    backgroundColor: Colors.white,
    buttonColor: Colors.blue,
  );
  static final dark = ThemeData.dark().copyWith(
    backgroundColor: Colors.black,
    buttonColor: Colors.red,
  );
}
```

## 第 3 步：将 MaterialApp 更改为 GetMaterialApp 并添加主题

GetX 给了我们更多的权力和功能来控制 MaterialApp，为了使用它们我们需要改变权力的来源

```
return GetMaterialApp(
  ...
  theme: Themes.light,
  darkTheme: Themes.dark,
);
```

## 第 4 步：从主题中获取您的风格

您也可以使用`Theme.of(context)`代替`context.theme`

```
return Container(
  color: context.theme.backgroundColor
);
```

## 第 5 步：动态更改主题

我˚F ThemeMode是黑暗的模式，则是让它发光别的反之亦然

```
onPressed: () {
  if (Get.isDarkMode)
    Get.changeThemeMode(ThemeMode.light);
  else
    Get.changeThemeMode(ThemeMode.dark);
}
```

就是这样！您的动态主题已准备就绪！现在我们可以开始下一部分。让主题持久化

# 第 2 部分：如何使主题持久化

## 第 1 步：将包添加到您的 pubspec.yaml 文件中

[get_storage](https://pub.dev/packages/get_storage)是 GetX 作者的本地存储包。它是[shared_preferences](https://pub.dev/packages/shared_preferences)的替代方案，具有更好的性能。

```
dependencies:
  flutter:
    sdk: flutter
  get: ^3.13.2
  get_storage: ^1.3.1 // add this
```



## 第 2 步：创建 ThemeService

```
class ThemeService {
  final _box = GetStorage();
  final _key = 'isDarkMode';
  
  /// Get isDarkMode info from local storage and return ThemeMode
  ThemeMode get theme => _loadThemeFromBox() ? ThemeMode.dark : ThemeMode.light;

  /// Load isDArkMode from local storage and if it's empty, returns false (that means default theme is light)
  bool _loadThemeFromBox() => _box.read(_key) ?? false;
  
  /// Save isDarkMode to local storage
  _saveThemeToBox(bool isDarkMode) => _box.write(_key, isDarkMode);
  
  /// Switch theme and save to local storage
  void switchTheme() {
    Get.changeThemeMode(_loadThemeFromBox() ? ThemeMode.light : ThemeMode.dark);
    _saveThemeToBox(!_loadThemeFromBox());
  }
}
```

## 第 3 步：初始化 GetStorage 并从 ThemeService 获取 ThemeMode

我们初始化本地存储并从存储中获取主题信息

```
void main() async { // make it async
  await GetStorage.init(); // add this
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return GetMaterialApp(
      theme: Themes.light,
      darkTheme: Themes.dark,
      themeMode: ThemeService().theme, // add this
      home: HomePage(),
    );
  }
}
```



## 第 4 步：将 ThemeService().switchTheme 添加到按钮的 onPressed 方法中

```
class HomePage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      backgroundColor: context.theme.backgroundColor,
      body: Center(
        child: RaisedButton(
          color: context.theme.buttonColor,
          child: Text(
            'Change Theme',
            style: TextStyle(color: Colors.white),
          ),
          onPressed: ThemeService().switchTheme,
        ),
      ),
    );
  }
}
```



## Example Project

If you’re the guy who better understands with source code. This is for you.