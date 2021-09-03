---
title: "Flutter中使用Device_Calendar库与Android_ios日历通讯"
date: 2021-08-30T10:05:52+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---

构建到 Roam 创建了一个"device_calendar"飞毛device_calendar/Dart 库，以便与移动设备日历进行通信（即添加、更新、检索、删除 Android/iOS 日历事件）。他们当前的 Github 页面包括一个示例应用程序， 实现库， 我建议查看。

他们的 Github 页面目前没有关于如何使用库的详细信息， 所以我想我会记录一些说明， 以节省其他人的一些时间。我会用我的应用程序作为示例来显示如何使用这个库。我的应用程序被称为**MMA日历**。它从 Web 检索未来的 MMA（混合武术）事件，并自动为用户创建/更新日历事件。

我的应用程序可以在下面的链接看到供参考 （请务必遵循我的 Github 😎）
https://github.com/patpatchpatrick/MMACalendarFlutter-v2 [-](https://github.com/patpatchpatrick/MMACalendarFlutter-v2-)

1. 首先，有必要将包依赖性添加到**pubspec.yaml**文件依赖部分，这是任何其他飘动外部库所必需的

```
dependencies:
  flutter:
    sdk: flutter

  device_calendar: ^0.1.1
```

2.日历使用的**权限**还必须添加到安卓系统 （**安卓磁.xml）**和 iOS**（Info.plist）**的相应文件中)

*安卓清单：*

```
<uses-permission android:name="android.permission.READ_CALENDAR" />
<uses-permission android:name="android.permission.WRITE_CALENDAR" />
```

*信息.列表：*

```
<key>NSCalendarsUsageDescription</key>
<string>REASON_TO_USE_CALENDAR</string>
```

\3. 确保将**device_calendar**包导入您目前正在处理的应用程序飞镖文件。在我的 MMA 日历应用程序中，我在我的主页.dart 文件和日历页中使用此库。dart 文件，因此我导入两个文件中的包

```
import 'package:device_calendar/device_calendar.dart';
```

\4. 在我的应用程序中，我在日历页中创建了一个日历页小部件。在创建此小部件时，我使用 Github device_calendar 示例应用程序作为参考，但稍微调整了它。我的日历页小部件包含一个列表视图，显示用户的日历列表。然后，用户可以选择要使用的日历（即应添加到 Web 查询的 MMA 事件的日历）。然后，当用户单击"**加载战斗并添加到日历**"按钮时，将自动为用户创建日历事件（或者如果以前创建事件，将更新日历事件），可以如下所示。

![img](https://miro.medium.com/freeze/max/32/1*VsYo1PuaJvkEFtfEKXX2zA.gif?q=20)

![img](https://miro.medium.com/max/320/1*VsYo1PuaJvkEFtfEKXX2zA.gif)

应用中用户选择日历的示例

\5. 在与移动设备日历通信之前，您需要创建一个**设备日历**。当我构建我的日历页面小部件的状态 （**日历页状态**） 时， 我在构造器中创建了一个新的设备日历。我这样做是因为日历页小部件立即需要插件，以便与移动设备日历通信的方法可用。

```

class CalendarPageState extends State<CalendarPage> {
  DeviceCalendarPlugin _deviceCalendarPlugin;

  List<Calendar> _calendars;
  Calendar _selectedCalendar;


  CalendarPageState() {
    _deviceCalendarPlugin = new DeviceCalendarPlugin();
  }

  @override
  initState() {
    super.initState();
    _retrieveCalendars();
  }
```

\6. 您还将在上面注意到，在构建状态时创建**设备卡伦达普卢金**后，当状态初始化时（在过度的初始状态 （） 方法中），称为**_retrieveCalendars**方法。本质上，这里正在发生的事情，是在状态构建后立即从设备中检索日历列表。

在下面的代码中，您将看到**_retrieveCalendars**方法异步从设备中检索日历，并将**_calendars**对象（即**列表<日历>**对象）设置为日历列表。在此之前，您必须检查设备是否授予了适当的日历权限，如果没有，您必须请求从设备检索日历列表所需的日历权限。如果成功授予权限，则设备**日历（）的检索日历（）**方法可用于从移动设备检索日历列表（**列表<日历>）。**总之，当创建日历页小部件时，创建了一个新的设备日历，然后使用插件的检索日历（）方法从设备中检索日历。

```
void _retrieveCalendars() async {
    //Retrieve user's calendars from mobile device
    //Request permissions first if they haven't been granted
    try {
      var permissionsGranted = await _deviceCalendarPlugin.hasPermissions();
      if (permissionsGranted.isSuccess && !permissionsGranted.data) {
        permissionsGranted = await _deviceCalendarPlugin.requestPermissions();
        if (!permissionsGranted.isSuccess || !permissionsGranted.data) {
          return;
        }
      }

      final calendarsResult = await _deviceCalendarPlugin.retrieveCalendars();
      setState(() {
        _calendars = calendarsResult?.data;
      });
    } catch (e) {
      print(e);
    }
  }
```

\7. 在我们有日历列表后，我们可以开始使用它们！您有几个选项 - 您可以从日历中检索事件、添加事件、删除事件、更新事件等...

在我的 MMA 日历应用程序中， 我只添加和更新事件， 所以这就是我将专注于这里。如第 4 步所述，我的日历页小部件包含显示用户日历的**ListView。**此 ListView 还显示仅读取日历的锁图标（此代码全部附带device_calendar 示例应用）。ListView 内部有一个**手势检测器，该检测器**将获取使用列表视图索引选择的日历日历。然后，我创建了一个回调功能，将此日历 ID、日历名称和设备日历名称和设备日历返回到我的应用程序主页，以便它们可以在我的主页小部件中使用。我不会在这里深入探讨如何使用 Dart 中的回调的详细信息，但要总结一下，您只需在小部件 （主页） 中创建一个功能，并将该功能传递到您的子小部件 （日历页） 的构造器中。然后，您可以从孩子调用此回调功能，为家长提供所需的对象（例如日历、日历名、设备日历）。（这方面的一个例子可以在我的代码_calendarCallback函数中看到）。

```
new ConstrainedBox(
            constraints: new BoxConstraints(maxHeight: 150.0),
            child: new ListView.builder(
              itemCount: _calendars?.length ?? 0,
              itemBuilder: (BuildContext context, int index) {
                return new GestureDetector(
                  onTap: () {
                    setState(() {
                      _selectedCalendar = _calendars[index];
                      this.widget._calendarCallback(_selectedCalendar.id, _selectedCalendar.name, _deviceCalendarPlugin);
                    });
                  },
                  child: new Padding(
                    padding: const EdgeInsets.all(10.0),
                    child: new Row(
                      children: <Widget>[
                        new Expanded(
                          flex: 1,
                          child: new Text(
                            _calendars[index].name,
                            style: new TextStyle(fontSize: 25.0),
                          ),
                        ),
                        new Icon(_calendars[index].isReadOnly
                            ? Icons.lock
                            : Icons.lock_open, color: Colors.white,)
                      ],
                    ),
                  ),
                );
              },
            ),
          ),
```

\8. 通过主页小部件中的回调接收日历信息后，用户已正式选择日历，因此我启用了"**加载战斗并添加到日历**"按钮供用户选择。我还向用户显示选定的日历名称，这可以在应用程序的上述 GIF 中看到（在第 4 步）中。

当用户单击此负载战斗按钮时，MMA 事件数据将从 Web 中刮取，并创建 MMA 事件，这些事件可以添加到用户的移动设备日历中。我的代码使用一种称为**_addEventsToCalendar**的方法在用户日历中创建/更新事件。在用户日历中添加/更新事件相对直接。首先，您必须使用选定的日历 ID 作为构建器创建新**事件 （日历ID）。** 然后，您需要向活动添加某些数据（开始时间、持续时间、标题、描述等）。创建事件后，使用**设备日历**将其添加到用户日历中。安恩德完成了！您将通过您调用的事件创建方法收到结果变量（**创建事件结果**）。如果您的活动创建成功，**则创建事件成功。**

另一个重要注释，您的创建事件结果对象还将有一个数据字符串变量（**创建事件结果.data），**它对应于您在移动设备中创建的事件的 ID。如果需要，您可以使用此 ID 更新事件。要更新事件，您必须在事件上设置 ID 属性（即**event.ID = 创建事件报告.data）。**然后，当您在事件上调用**创建或更新事件（事件）**方法时，它将被更新，而不是正在创建的新/重复事件。

我个人使用**共享参考**（shared_preferences 库）来处理我的活动 ID 。当我创建 MMA 事件时，我将事件名称/日期与 ID 关联，然后，如果用户已经拥有与事件相关的 ID，则我确保更新事件，而不是在其日历中创建新的/重复事件。

```
Future _addEventsToCalendar(List<MMAEvent> mmaEvents) async {
    //Method to add events to the user's calendar
    //If called, the list of mmaEvents will be iterated through and the mma
    // Events will be added to the user's selected calendar

    //If the events have previously been added by the user, they will have a
    // shared preference key for the Event ID and the event will be UPDATED
    // instead of CREATED

    //If events are successfully created/added, then the events that were
    // CREATED/UPDATED will be displayed to the user in the status string

    var fightString = new StringBuffer('');
    SharedPreferences prefs = await SharedPreferences.getInstance();

    for (var mmaEvent in mmaEvents) {
      //Before adding MMA Event to calendar, check if it is ready for calendar
      // (i.e. ensure it is properly formatted)
      if (mmaEvent.readyForCalendar) {
        final eventTime = mmaEvent.eventDate;
        final eventToCreate = new Event(_currentCalendarID);
        eventToCreate.title = mmaEvent.eventName;
        eventToCreate.start = eventTime;
        eventToCreate.description = mmaEvent.eventFights.toString();
        String mmaEventId = prefs.getString(mmaEvent.getPrefKey());
        if (mmaEventId != null) {
          eventToCreate.eventId = mmaEventId;
        }
        eventToCreate.end = eventTime.add(new Duration(hours: 3));
        final createEventResult =
            await _deviceCalendarPlugin.createOrUpdateEvent(eventToCreate);
        if (createEventResult.isSuccess &&
            (createEventResult.data?.isNotEmpty ?? false)) {
          prefs.setString(mmaEvent.getPrefKey(), createEventResult.data);
          fightString.write(mmaEvent.eventName + '\n');
        }
      }
    }
```





将以下***几***行添加到***flutter_app_path/android/gradle.properties。***

```dart
android.useAndroidX=true
android.enableJetifier=true
```
