---
title: "Flutter持久化存储之使用和封装shared_preferences"
date: 2021-08-31T13:27:35+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter","dart"]
categories: ["Flutter","dart"]
---



http://www.ptbird.cn/flutter-video.html

https://www.jianshu.com/p/a104f0a96594

https://blog.csdn.net/beyondforme/article/details/103895229

首先在 pubspec.yaml 文件中添加 shared_preferences 引用并 Packages get

```dart
dependencies:
  flutter:
    sdk: flutter
 
  # The following adds the Cupertino Icons font to your application.
  # Use with the CupertinoIcons class for iOS style icons.
  shared_preferences: ^0.4.2
```

然后封装 SharedPreferences 工具类

```dart
import 'package:shared_preferences/shared_preferences.dart';
 
/**
 * @des SharedPreferences 工具类
 * @author liyongli 20190522
 * */
class SharedPreferencesDataUtils{
 
  // 保存数据
  Future setUserInfo(key, value) async{
    SharedPreferences sp = await SharedPreferences.getInstance();
    sp.setString(key, value);
  }
 
  // 获取数据
  Future getUserInfo(key) async{
    SharedPreferences sp = await SharedPreferences.getInstance();
    print(sp.get(key));
    return sp.get(key);
  }
 
  // 清除数据
  Future deleteUserInfo(key) async{
    SharedPreferences sp = await SharedPreferences.getInstance();
    sp.remove(key);
  }
 
}
```

调用方式

```dart
    SharedPreferencesDataUtils spDataUtils = new SharedPreferencesDataUtils();
 
    spDataUtils.setUserInfo("name","Flutter Flutter Flutter");
    spDataUtils.setUserInfo("type","Save Save Save");
 
    print(dataUtils.getUserInfo("name"));
    print(dataUtils.getUserInfo("type"));
```

运行结果

```dart
    I/flutter (13801): Flutter Flutter Flutter
    I/flutter (13801): Save Save Save
```



# Flutter持久化存储之使用和封装shared_preferences

对NSUserDefaults(在iOS上)和SharedPreferences(在Android上)进行了包装，用于持久化的存储一些较为简单的数据，以键值对的方式存储(key-value) ，不建议存储重要的信息。 复杂数据进行数据库持久化存储请看 Flutter持久化存储之sqflite
默认只能存储 int、double、String、List、bool类型，对其进行封装处理为单例模式，并使其具备存储Map和List的能力，并且不用关注类型，存储和读取都更加方便

## 1.准备工作

1.1 添加依赖

```dart
在pubspec.yaml文件下添加
```

```dart
dependencies:
  shared_preferences: ^0.5.7+3

```

 安装
如果保存了有自动执行则最好，如果没有那么在终端运行flutter packages get命令

导入

```dart
import ‘package:shared_preferences/shared_preferences.dart’;
```



## 2.使用流程

2.1 获取实例对象

```dart
SharedPreferences prefs = await SharedPreferences.getInstance();
```


2.2 设置值

```dart
// 目前仅支持5种类型的存储,Map(对象)需要先转换成JSON字符串之后 再进行存储 本质上还是存储字符串
// 1. int
prefs.setInt('age',18);
// 2. double
prefs.setDouble('source',99.9);
// 3. String
prefs.setString('name','Marco');
// 4. bool
prefs.setBool('isADC',true);
// 5. List<String>
prefs.setStringList('strList',['str1','str2']);
// 6. Map 使用convert中的方法将Map转换成字符串进行存储 取出时也需要进行处理
prefs.setString('info',JsonEncoder().convert({"name":"Marco","type":"ADC"}));

```

2.3 读取值

```dart
// 0. get 可以取出任意类型的value
prefs.get('age');
// 1. int 
prefs.getInt('age');
// 2.double
prefs.getDouble('source');
// 3. String
prefs.getString('name');
// 4. bool 
prefs.getBool('isADC');
// 5.List<String>
prefs.getStringList('strList');
// 6. Map 取出的是JSON字符串 因此需要进行转换
JsonDecoder().convert(prefs.getString("info"))

```

2.4 其他操作

```dart
// 1. remove(key)     删除值 根据key删除保存的键值对
prefs.remove('age');
// 2.containsKey(key)  是否存储的键值对中是否包含给定的key
prefs.remove('source');
// 3. clear()  清空所有键值对
prefs.clear();
// 4. getKeys() 获取所有的key 返回一个Set<String> 
prefs.getKeys();

```



## 3.封装 shared_preferences

封装和使用都较为简单，只是对不同类型的数据进行判断和处理，如果没搞明白，直接使用也是完全没有问题的
这里的代码是完整的main.dart的代码，包含了封装的类以及每一个方法的使用

```dart
import 'package:flutter/material.dart';
import 'package:shared_preferences/shared_preferences.dart';
import 'dart:convert';
/// 封装SharedPreferences为单例模式
class PersistentStorage{
  /// 静态私有实例对象
  static final _instance=PersistentStorage._init();
  /// 工厂构造函数 返回实例对象
  factory PersistentStorage()=>_instance;
  /// SharedPreferences对象
  static SharedPreferences _storage;
  /// 命名构造函数 用于初始化SharedPreferences实例对象
  PersistentStorage._init(){
    _initStorage();
  } 
  // 之所以这个没有写在 _init中，是因为SharedPreferences.getInstance是一个异步的方法 需要用await接收它的值
  _initStorage()async{
    // 若_不存在 则创建SharedPreferences实例
    if(_storage==null)
      _storage=await SharedPreferences.getInstance();
  }
  /// 设置存储
  setStorage(String key,dynamic value)async{
    await _initStorage();
    String type;
    // 监测value的类型 如果是Map和List,则转换成JSON，以字符串进行存储
    if(value is Map||value is List){
      type='String';
      value=JsonEncoder().convert(value);
    }
    // 否则 获取value的类型的字符串形式
    else{
      type=value.runtimeType.toString();
    }
    // 根据value不同的类型 用不同的方法进行存储
    switch (type) {
      case 'String':
        _storage.setString(key, value);
        break;
      case 'int':
        _storage.setInt(key, value);
        break;
      case 'double':
        _storage.setDouble(key, value);
        break;
      case 'bool':
        _storage.setBool(key, value);
        break;
    }
  }
  /// 获取存储 注意：返回的是一个Future对象 要么用await接收 要么在.then中接收
  Future<dynamic> getStorage(String key)async{
    await _initStorage();
    // 获取key对应的value
    dynamic value=_storage.get(key);
    // 判断value是不是一个json的字符串 是 则解码
    if(_isJson(value)){
      return JsonDecoder().convert(value);
    }else{
      // 不是 则直接返回
      return value;
    }
  }
  /// 是否包含某个key 
  Future<bool> hasKey(String key)async{
    await _initStorage();
    return _storage.containsKey(key);
  }
  /// 删除key指向的存储 如果key存在则删除并返回true，否则返回false
  Future<bool> removeStorage(String key)async{
    await _initStorage();
    if(await hasKey(key)){
      await _storage.remove(key);
      return true;
    }else{
      return false;
    }
    // return  _storage.remove(key);
  }

  /// 清空存储 并总是返回true
  Future<bool> clear()async{
    await _initStorage();
    _storage.clear();
    return true;
  }

  /// 获取所有的key 类型为Set<String>
  Future<Set<String>> getKeys()async{
    await _initStorage();
    return _storage.getKeys();
  }


  // 判断是否是JSON字符串
  _isJson(dynamic value){
    try{
      // 如果value是一个json的字符串 则不会报错 返回true
      JsonDecoder().convert(value);
      return true;
    }catch(e){
      // 如果value不是json的字符串 则报错 进入catch 返回false
      return false;
    }
  }
}

main(List<String> args) async{
  runApp(MaterialApp(
    home: Scaffold(
      appBar: AppBar(title: Text("封装SharedPreferences"),),
      body: Container(),
    ),
  ));
  // 创建两个实例对象
  var ps=PersistentStorage();
  var ps2=PersistentStorage();
  // 单例模式 对象是同一个对象
  print("两个对象是否相等:${ps==ps2}");  // true 
  // 1. 存储所有类型的值
  ps.setStorage('Map',{"key":"value"});        // 存储Map
  ps.setStorage('int',1);                      // 存储int
  ps.setStorage('double',1.0);                 // 存储double
  ps.setStorage('bool',true);                  // 存储bool
  ps.setStorage('String', "Hello World");      // 存储String
  ps.setStorage('List',[1,true,'String',1.0]); // 存储List

  // 除setStorage以外 获取的方法的返回值全部都是Future类型
  // 因此需要使用await获取 或者 在其.then方法中获取值

  // 2. 根据key获取存储的值
  // 2.1 通过await 直接获取存储的值 注意 await只能在async方法中使用
  print(await ps.getStorage('Map'));                  // {key: value}
  // 2.2 在then中获取存储的值 
  ps.getStorage('Map').then((value) => print(value)); // {key: value}

  // 同上

  // 3. 根据key移除存储的值
  print(await ps.removeStorage("List"));      // true  删除成功
  print(await ps.removeStorage('nothing'));   // false 删除失败

  // 4. 是否包含某个key 
  print(await ps.hasKey('map'));   // 存在   true  
  print(await ps.hasKey("List"));  // 不存在 false 

  // 5. 获取所有的key
  print(await ps.getKeys());

  // 6. 清空所有存储 
  print(await ps.clear());  //一直都会返回true 
}


```

未封装的shared_preferences的使用还是比较简单的，主要是Map类型的存储不是很方便，需要借助convert进行转换，关于convert可以参考Flutter中编解码Convert基本使用 。
经过封装之后还是非常实用的，用法也比较简单，如果对你有所帮助，点赞鼓励可好~~





# Flutter中编解码Convert基本使用

## 1.导入

```dart
import 'dart:convert';     
import 'dart:typed_data';  // Uint8List需要导入这个

```

## 2.Ascii

```dart
// 创建一个 AsciiCodec 对象 
AsciiCodec ac=new AsciiCodec();
// 编码 将ascii字符串转换成UintList类型 无法编码中文 会报错
Uint8List list=ac.encode("Hello Dart");
// 等价于
// Uint8List list=AsciiEncoder().convert("Hello Dart");
print(list);  // [72, 101, 108, 108, 111, 32, 68, 97, 114, 116]

// 解码 将UintList转换成ascii字符串
String str=ac.decode(list);
// 等价于
// String str=AsciiDecoder().convert(list);
print(str);    // Hello Dart

// 解码流 即将Stram<List<int>>类型转换成ascii字符串 
ac.decodeStream(Stream.value(list)).then((value)=>print(value)); // Hello Dart

// 编解码器的名称
print(ac.name);  // us-ascii

```

## 3.Base64

```dart
AsciiCodec ac=new AsciiCodec();
Uint8List list=await ac.encode("Hello Dart");
print(list);  //  [72, 101, 108, 108, 111, 32, 68, 97, 114, 116]

// Base64Codec允许base64将字节编码为ASCII字符串，并将有效编码解码回字节,始终是4的倍数。
Base64Codec bc=new Base64Codec();
// 编码
String str=bc.encode(list);
// 等价于
// String str=new Base64Encoder().convert(list);
print(str);  //   SGVsbG8gRGFydA==

// 解码
Uint8List list2=bc.decode(str);
// 等价于
// Uint8List list2=new Base64Decoder().convert(str);
print(list2); //  [72, 101, 108, 108, 111, 32, 68, 97, 114, 116]

// 验证并标准化source的base64编码数据 是base64则照常输出 不是则进行填充或报错
// 看是否是4的倍数 如果多一个字符 则报错 多两个及以上 则填充
print(bc.normalize(str));

```

## 4.json

```dart
JsonCodec jc=new JsonCodec();
// JS中的对象就是这里的Map
print({}.runtimeType);
// 将Map转换成JSON
Object person=jc.encode({'name':"zs"});
// 等价于
// Object person=new JsonEncoder().convert({'name':"zs"});
print(person);
// 将JSON转换成Map
Map<String,dynamic> str=jc.decode(person);
// 等价于
// Map<String,dynamic> str=new JsonDecoder().convert(person);
print(str);

// 将对象或JSON字符串 转换成一个UTF-8数组(Uint8List)
Uint8List person2=new JsonUtf8Encoder().convert({'name':'zs'});
print(person2);

```

## 5.utf8

```dart
Utf8Codec utf=new Utf8Codec();

// 将字符串编码为Uint8List
Uint8List list=utf.encode("Hello Dart");
// 等价于
// Uint8List list=new Utf8Encoder().convert("Hello Dart");
print(list);

// 将Uint8List解码为字符串
String str=utf.decode(list);
// 等价于
// String str=new Utf8Decoder().convert(list);
print(str);

// 将流解码
utf.decodeStream(Stream.value(list)).then((value) => print(value));

print(utf.name);
}

```

## 6.Latin

```dart
// Latin1编码是单字节编码(无法编码中文)，向下兼容ASCII
Latin1Codec lc=new Latin1Codec();
Uint8List list=lc.encode("Hello Dart");
// Uint8List list2=lc.encode("你好 Dart"); //报错
print(list);

String str=lc.decode(list);
print(str);

```

# Flutter持久化存储之sqflite操作数据库详细使用

前言
sqflite是一个轻量的关系型数据库，以表的形式将较为复杂的数据存储到.db文件中(数据库)，同时支持安卓和IOS，简单数据可直接使用 shared_preferences

## 1.准备工作

### 1.1 添加依赖:

在pubspec.yaml文件下添加
path_provider是用来处理路径相关的操作

```dart
dependencies:
  sqflite: ^1.3.0+2
  path_provider: ^1.6.9
```

### 1.2 安装

如果保存了有自动执行则最好，如果没有那么在终端运行flutter packages get命令

### 1.3 导入

```dart
import 'package:sqflite/sqflite.dart';
import 'package:path_provider/path_provider.dart';

```

### 1.4 注意

```dart
 // SQLite支持的数据类型  不支持bool和DateTime
 // SQL类型 ==== Dart类型
 // integer ==== int
 // real    ==== num
 // text    ==== String
 // blob    ==== Uint8List

```



## 2.使用流程

### 2.1 创建数据库

```dart
// 数据库文件路径：可以根据自己的需求自己定 这里使用的是外部存储可见的目录
// 可以将路径打印出来 在手机上找到它
String databasePath=(await getExternalStorageDirectory()).path+"/demo.db";

// 打开数据库  (若想数据库只能读取则使用openReadOnlyDatabase())
// 1 若数据库不存在 则创建数据库
// 2 若数据库存在 version大于当前数据库version 则升级
// 3 若数据库存在 version小于当前数据库version 则降级
// 4 若数据库存在 version等于当前数据库version 则不变
 Database database=await openDatabase(databasePath,version: 2,
  readOnly: false,      //   是否只读
  singleInstance: true, //   是否单例
  // 第一个被调用的可选回调 配置数据库使用SQL语句配置
  onConfigure: (Database database)async{
    await database.execute("PRAGMA foreign_keys = ON");
    print("配置数据库");
  },
  // 创建数据库回调
  onCreate: (Database database,int version)async{
    print("版本:$version 数据库创建成功");
  },
  // 数据库降级回调
  onDowngrade: (Database database,int oldVersion,int newVersion)async{
    print("版本$oldVersion 降级到 $newVersion");
  },
  // 数据库升级回调
  onUpgrade: (Database database,int oldVersion,int newVersion)async{
    print("版本$oldVersion 升级到 $newVersion");
  },
  // 数据库打开回调
  onOpen: (Database database){
    print("数据库打开成功");
  },
);

```

### 2.2 创建数据表

```dart
// 使用execute方法执行SQL语句 注意：execute执行的SQL是没有返回值的
database.execute('CREATE TABLE Test (id INTEGER PRIMARY KEY, name TEXT, value INTEGER, age REAL)');

```

### 2.3 增删改查

2.3.1 where和whereArgs的使用

```dart
// 在查询和更新删除等语句中，会使用到where和whereArgs作为条件及条件参数
// 此处以查询为例 更新和删除请举一反三
// 查询test表中 name为zs并且age为18的记录 
// where中 and代表并且(都满足) or代表或者(满足其一)
// where中的?为占位符 会依照顺序被后面whereArgs中的数据替换
await database.query('test',where:"name=? and age=?",whereArgs: ["zs",18]).then((value) => print(value));

```

2.3.2 插入数据

```dart
// 插入数据  
//1.insert(表名,{"键":值},conflictAlgorithm(冲突算法:枚举类型)) 返回行数
database.insert('test',{"name":'zs',"age":18}).then((value) => print(value));
// 2. 使用SQL语句进行插入 
// database.rawInsert("SQL语句");

```

2.3.3 删除数据

```dart
// 删除数据 
// 1.delete("表名",where(条件),whereArgs(条件参数))
// 此处为寻找name为zs的记录
database.delete('test',where: "name=?",whereArgs: ["zs"]);  
// 2. 使用SQL语句进行删除
// database.rawDelete("SQL语句");

```

2.3.4 更新数据

```dart
// 更新数据
// 1.update("表名",要修改的内容{'键':值},where(条件),whereArgs(条件参数),conflictAlgorithm(冲突算法：枚举类型))
// 将name为zs的记录的name改为ls，age改为18
database.update('test', {"name":'ls',"age":18},where: "name=?",whereArgs: ['zs']);
// 2.使用SQL语句进行更新
// database.rawUpdate("SQL语句");

```

2.3.5 查询数据

```dart
// 查询数据
// 1.query(表名,{distinct,columns(返回哪些字段),where(条件),
// whereArgs(条件参数),groupBy(分组),having(分组后的进行条件查询),
//  orderBy(排序),limit(一次读几条),offset(起始位置)}) 返回读取到的结果(列表)
// 若没有查询条件 则返回这个表的数据
await database.query('test',                 //查询的表名
  distinct: true,                              // 不重复
  where:"value=? and age=?",whereArgs: [1,18], // 查询 value为1并且age为18的记录
  columns: ["name","age"],                     // 返回的记录只包含name和age两个字段
  groupBy: "age",                              // 根据age分组
  orderBy: "age",                              // 根据age排序
  limit: 10,                                   // 一次读取10条记录
  offset: 5                                    // 从第5条记录开始查询
);
// 2. 使用SQL语句进行查询
// database.rawQuery("SQL语句");

```

### 2.4 批处理 Batch

```dart
// 批处理SQL语句 往Batch中添加语句 但是这些语句并不会执行 需要调用commit方法 然后一次性执行
Batch batch=database.batch();
batch.insert("test",{"name":'ls'});
batch.insert("test",{"name":'ww'});
// noResult 是否不需要结果  continueOnError遇到异常是否继续执行 
batch.commit(noResult: true,continueOnError: true).then((value) => print(value));

```

### 2.5 事务

```dart
// 事务
// 在事务中执行SQL语句 若发生异常 则进行回滚 即全部SQL语句无效
// 四大特征:
// 1. 原子性：即事务中的语句为一个整体 要么全部执行 要么全不执行
// 2. 一致性：保持数据的一致性 如转账 转账过后的总金额需要保持一致
// 3. 隔离性：多个事务并发时  互不干涉内部数据 处理的数据都是别的事务处理前或处理后的数据
// 4. 持久性：事务提交后 数据是持久不可更改的

database.transaction((txn)async{
  // txn基本有database中所有的操作 基本可以当做database使用
  txn.insert('test',{"name":'Rob'});
});

```

### 2.6其他

```dart
// 数据库是否打开
// print(database.isOpen);

//  获取默认数据库存放的路径
getDatabasesPath().then((value) => print("默认数据库路径"+value));

// 根据路径查看数据库是否存在
databaseExists(databasePath).then((value) => print("数据库是否存在:"+value.toString()));

// 设置数据库版本
database.setVersion(666);

// 获取数据库版本
database.getVersion().then((value) => print(value));

// 关闭数据库(数据库文件还在)
// database.close(); 

//  删除数据库(数据库文件不在)
// await deleteDatabase(databasePath);

```

根据官方文档和源码注解以及尝试所得，若有所帮，点赞一下


# Flutter插件之path_provider

## 准备工作

1.1 添加依赖
在pubspec.yaml文件下添加
1
dependencies:
  path_provider: ^1.6.9
1
2
1.2 安装
如果保存了有自动执行则最好，如果没有那么在终端运行flutter packages get命令
1
1.3 导入
import 'package:path_provider/path_provider.dart';
1

## 2.方法

1. 2.1 获取临时文件路径(IOS和安卓通用)

  ```dart
  // 获取不会备份并且随时会被删除的临时目录(IOS和安卓通用)
  getTemporaryDirectory().then((value) => print(value));
  ```

  2.2 获取应用支持目录(IOS和安卓通用)

  ```dart
  // 用于存储应用支持的目录 这个目录对于用户是不可见的 (IOS和安卓通用)
  getApplicationSupportDirectory().then((value) => print(value));
  ```

  2.3 获取应用文件目录(IOS和安卓通用)

  ```dart
  // 用于放置用户生成的数据或不能有应用程序重新创建的数据 用户不可见(IOS和安卓通用)
  getApplicationDocumentsDirectory().then((value) => print(value));
  ```

  2.4 获取应用持久存储目录路径(仅IOS可用)

  ```dart
  // 应用程序可以存储持久化、备份和用户不可见的文件的目录路径
  getLibraryDirectory().then((value) => print(value));
  ```

  2.5 获取外部存储目录(仅安卓可用)

  ```dart
  // 获取外部存储目录 用户可见
  getExternalStorageDirectory().then((value) => print(value));
  ```

  2.6 获取外部存储目录列表(仅安卓可用)

  ```dart
  // 可以存储应用程序特定数据的目录 
  // 这些路径通常驻留在外部存储上 用户可见 如单独的分区或SD卡(可以有多个 所以是列表)
  getExternalStorageDirectories().then((value) => print(value));
  ```

  2.7 获取外部缓存目录(仅安卓可用)

  ```dart
  // 可以存储应用程序特定外部存储数据的目录 
  // 这些路径通常驻留在外部存储上，如单独的分区或SD卡(可以有多个 所以是列表)
  getExternalCacheDirectories().then((value) => print(value));
  ```

  2.8 获取下载目录(仅桌面可用 安卓和IOS报错)

  ```dart
  // 获取下载路径 
  getDownloadsDirectory().then((value) => print(value));
  ```


  插件的内容较少，使用也比较简单，仅仅只是用于获取路径，并没有操作文件和目录的功能，因此，需要搭配Director和File等进行操作。

# Flutter中目录(Directory)和文件(File)常用操作

## 1. 目录(Directory)

#### 1.1 导入

```dart
import 'dart:io';       // Directory所在的包
import 'dart:convert';  // 

```

#### 1.2创建实例

```dart
// 根据路径字符串创建目录对象
Directory dir=new Directory("assets\\files");
//  根据Uri对象创建目录对象
// Directory dir=new Directory.fromUri(Uri(path: "assets"));
// 根据Uint8List路径创建实例 Utf8Encoder().convert()将字符串转换成Uint8List
// Directory.fromRawPath(Utf8Encoder().convert("assets\\files"));

```

#### 1.3属性

```dart
// String path 获取此目录的路径
print(dir.path);

// Uri 返回表示目录位置的uri
print(dir.uri);

// 父目录的Directory对象
print(dir.parent);

// absolute 返回一个绝对路径的Directory对象
print(dir.absolute);

// 判断是否是绝对路径
print(dir.isAbsolute);

```

#### 1.4方法

```dart
// create({bool recursive: false}) → Future<Directory> 根据路径创建目录 recursive是否递归创建 递归创建的意思就是 即便路径中有目录不存在 全部都会创建出来
await dir.create(recursive: true);

// createTemp([String prefix]) → Future<Directory> 在此目录中创建一个临时目录,其他随机字符将附加到prefix以产生唯一的目录名称. 
await dir.createTemp();

// list({bool recursive: false, bool followLinks: true}) → Stream<FileSystemEntity>
// recursive是否递归列出子目录 followLinks是否允许link
await dir.list().toList().then((value) => print(value));

// rename(String newPath) → Future<Directory> 重命名目录

// resolveSymbolicLinks() → Future<String> 解析文件系统对象相对于当前工作目录的路径 解析路径上的所有符号链接，并解析所有..和. 路径段 返回一个类似绝对路径的字符串
await dir.absolute.resolveSymbolicLinks().then((value) => print(value));

// 查看目录是否存在
await dir.exists().then((value) => print(value));

// delete({bool recursive: false}) → Future<FileSystemEntity> 删除文件目录 
// 默认情况下 只能删除空目录 将recursive设置为true 才可以递归删除目录中所有的文件和目录
await dir.delete(recursive: true).then((value) => print(value)).catchError((err)=>{print(err)});

// stat() → Future<FileStat> 查看目录信息
// type directory                     类型：目录
// changed 2020-05-29 11:14:50.000    文件系统对象的数据或元数据的最后更改时间
// modified 2020-05-29 11:15:01.000   最后一次更改文件系统对象的数据的时间
// accessed 2020-05-29 11:15:01.000   上次访问文件系统对象的数据的时间
// mode rwxrwxrwx                     文件系统对象的模式 r(read) w(write) x(execute) 读写执行
// size 0                             文件大小
await dir.stat().then((value) => print(value));

```

## 2.文件(File)

#### 2.1 导入

```dart
import 'dart:io';
import 'dart:convert';

```

#### 2.1创建实例

```dart
// 根据文件路径字符串创建文件实例
File file=new File("assets/index.txt");
// 根据Uri对象创建文件实例
File file2=new File.fromUri(Uri(path: "assets/index.txt"));
Utf8Codec utf=Utf8Codec();
// 根据Uint8List文件路径创建实例 Utf8Encoder().convert()将字符串转换成Uint8List
File file3=File.fromRawPath(Utf8Encoder().convert("assets\\files\\index.txt"));

```

#### 2.3属性

```dart
// 打印一下file实例对象
print(file);   // File: 'assets\files\index.txt'

// 返回绝对路径的File实例
print(file.absolute); // File: 'C:\Users\Administrator.PW6NUH21X31RM79\Desktop\Dart\assets\files\index.txt'

// 获取文件路径
print(file.path); // assets\files\index.txt

// 是否是绝对路径
print(file.isAbsolute);  // fasle

// 获取父目录的Directory对象
print(file.parent);  // Directory: 'assets\files'

// 获取uri
print(file.uri);   // assets/files/index.txt

```

#### 2.4方法

```dart
// 1. copy(String newPath) 复制文件  copySync(String newPath) 同步复制文件
// 新路径的目录必须存在 否则报错
// 若新路径下已有同名文件 则覆盖
await file.copy("assets/image/index.txt").then((value) => print(value));

// 2. create({bool recursive: false})创建文件 
// createSync({bool recursive: false}) 同步创建文件
// 可选命名参数:recursive 默认false, 
// 若为true  则路径中有目录不存在时 会递归创建目录 
// 若为false 则路径中的目录不存在时 会报错
File file2=new File("assets/files/text.txt");
await file2.create();

// 3.lastAccessed() 获取文件上次访问时间  lastAccessedSync()同步获取
print(await file.lastAccessed()); // 2020-05-30 10:39:35.000
// setLastAccessed(Datetime time)  修改文件上次访问时间 +Sync 同步
await file.setLastAccessed(new DateTime.now());

// 4. lastModified() 获取文件最后修改时间 lastModifiedSync()同步获取
print(await file.lastModified());
// setLastModified(DateTime time)  修改文件的最后修改时间 +Sync 同步
await file.setLastModified(new DateTime.now());

// 5. length()获取文件长度(字节数) lengthSync() 同步获取
print(await file.length());

// 6. rename(String newPath) 重命名 renameSync(String newPath)
// 若新路径和旧路径在同一个目录下 那么仅仅是重命名
// 若新路径和旧路径不在一个目录下 那么就是移动文件加重命名 
file.rename("index.txt");

// 7. 读取
// 7.1 readAsString()以字符串形式读取 readAsStringSync() 同步读取
print(await file.readAsString());
// 7.2 readAsBytes()以字节形式读取   readAsBytesSync() 同步读取
print(await file.readAsBytes());     
// 7.3 readAsLines()按行读取 返回字符串数组    readAsLinesSync() 同步读取
print(await file.readAsLines());

// 8. 写入
// 8.1 writeAsString() 以字符串方式写入
file.writeAsString("Dart ",
mode: FileMode.append,// 写入的模式 append(追加写入) read(只读) write(读写) writeOnly(只写)  writeOnlyAppend(只追加)
flush: true,    // 如果flush设置为`true` 则写入的数据将在返回之前刷新到文件系统 
encoding:utf8,  // 设置编码
);
// 8.2 writeAsBytes() 写入字节数组 参数同上
file.writeAsBytes([1,2,3]);

// 9. delete() 删除文件  deleteSync() 同步删除文件
// await file.delete();

// 10. exists() 检查文件是否存在 existsSync() 同步检查文件是否存在
print(await file.exists());

// 11. resolveSymbolicLinks() 解析文件系统对象相对于当前工作目录的路径 +Sync同步
print(await file.resolveSymbolicLinks());

// 12. stat()  获取文件对应的FileStat对象 StatSync() 同步获取
// 包含type(类型) 
// changed(文件系统对象的数据的最后修改时间) 
// modified(最后一次更改文件系统对象的数据的时间)
// accessed(上次访问时间)
// size(文件大小)  
print(await file.stat());

// 13和14涉及流(Stream)的操作 之后会继续更新异步和流
// 13. openRead() 为文件的内容创建一个Stream(流) 
print(file.openRead());

// 14. openWrite() 为文件创建一个IOSink,使用结束需要释放资源
// print(file.openWrite());  // 先注释 不然会截断文件

```

