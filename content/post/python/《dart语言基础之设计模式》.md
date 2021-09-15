---
title: "《Dart语言基础之设计模式》"
subtitle: ""
date: 2021-07-28T19:39:10+08:00
lastmod: 2021-07-28T19:39:10+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]

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



> 本文主要介绍

<!--more-->

# 设计模式

[参考](https://www.  .com/design-pattern/) 模式分析，模式难点，模式解决问题，优点，缺点，模式应用场景，模式代码(基于dart)

## 六大原则

### 开闭原则(Open-Closed-Principle)

核心：一个软件实体应当对拓展开放，对修改关闭。即：软件实体应尽量在不修改原有代码的情况下进行拓展。

### 里氏代换原则(Liskow-Substitution-Principle)

核心：所有引用基类(父类)的地方，都必须能透明地使用其子类的对象。

一个软件实体如果使用的是一个父类，那么一定适用于其子类，而且他察觉不出父类对象和子类对象的区别。也就是说，在软件里面，把父类都替换成它的子类，程序的行为没有变化。简单地说，子类必须能够替换掉它们的父类型。

###  依赖倒转原则(Dependency-Inversion-Principle)

核心: 抽象不应该依赖于细节，细节应当依赖于抽象。换言之，要针对接口编程，而非针对实现编程。

思想: 抽象不应该依赖于细节，细节应当依赖于抽象。换言之，要针对接口编程，而不是针对实现编程。依赖倒转其实可以说是面向对象设计的标志，用哪种语言来编写程序不重要，如果编写时考虑的都是如何针对抽象编程而不是针对细节编程，即 程序中所有的依赖关系都是终止于抽象类或者接口，那就是面向对象的设计，反之就是面向过程化设计了。

原则：

1. 高层模块不应该依赖于低层模块。两个都应该依赖于抽象。
2. 抽象不应该依赖细节。细节应该依赖于抽象。

### 单一职责原则(Single-Responsibility-Principle)

核心：一个类只负责一个功能领域中相应的职责，或者可以定义为：就一个类而言，应该只有一个引起它变化的原因。

思想：如果一个类承担的职责过多，就等于把这些职责耦合在一起，一个职责的变化可能会削弱或者抑制这个类完成其他职责的能力。这种耦合会导致脆弱的设计，当变化发生时，设计会遭受到意想不到的破坏。【Eg:游戏的界面组成与逻辑组成分离】

### 接口隔离原则(Interface-Segregation-Principle)

核心：使用多个专门的接口，而不使用单一的总接口。即 客户端不应该依赖于那些它不需要的接口。

### 迪米特法则(Law-Of-Demeter)

核心：一个软件实体应当尽可能少地与其他实体发生作用。(无熟人难办事)

思想：也叫最少知识原则。如果两个类不彼此通信，那么这两个类就不应当直接地发生相互作用。如果其中一个类需要另一个类的某一个方法的话，可以通过第三者转发这个调用。(不要和陌生人说话)

原则： 在迪米特法则中，对于一个对象，其朋友包括如下几类：

1. 当前对象 this
2. 以参数形式传入到当前对象方法中的对象
3. 当前对象的成员对象
4. 若当前对象的成员你对象是一个集合，那么集合中的对象也都是朋友
5. 当前对象所创建的对象

## 工厂模式

### 模式分析

定义一个创建对象的接口，让其子类自己决定实例化哪一个工厂类，工厂模式使其创建过程延迟到子类进行

### 模式难点

### 模式解决问题

该模式主要解决接口选择的问题。我们明确地计划不同条件下创建不同实例时，让其子类实现工厂接口，返回的也是一个抽象的产品

###  优点

1. 一个调用者想创建一个对象，只要知道其名称就可以了。
2. 扩展性高，如果想增加一个产品，只要扩展一个工厂类就可以。
3. 屏蔽产品的具体实现，调用者只关心产品的接口。

### 缺点

每次增加一个产品时，都需要增加一个具体类和对象实现工厂，使得系统中类的个数成倍增加，在一定程度上增加了系统的复杂度，同时也增加了系统具体类的依赖。

### 模式应用场景

1. 日志记录器：记录可能记录到本地硬盘、系统事件、远程服务器等，用户可以选择记录日志到什么地方。
2. 数据库访问，当用户不知道最后系统采用哪一类数据库，以及数据库可能有变化时。
3. 设计一个连接服务器的框架，需要三个协议，"POP3"、"IMAP"、"HTTP"，可以把这三个作为产品类，共同实现一个接口。

### 模式代码



```dart
abstract class Pen {
  draw();
}

class Pencil implements Pen {
  draw() {
    print("this is Pencil");
  }
}

class ColorPen implements Pen {
  draw() {
    print("this is ColorPen");
  }
}
class RunFactory implements Run {
  @override
  main() {
    var pencil = getPen("Pencil");
    var colorPen = getPen("ColorPen");
    pencil.draw();
    colorPen.draw();
  }
  Pen getPen(String penStr) {
    if (penStr == "Pencil") {
      return new Pencil();
    } else if (penStr == "ColorPen") {
      return new ColorPen();
    } else {
      return null;
    }
  }
}
```

## 单例模式

> 这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

> 这种模式涉及到一个单一的类，该类负责创建自己的对象，同时确保只有单个对象被创建。这个类提供了一种访问其唯一的对象的方式，可以直接访问，不需要实例化该类的对象。

### 模式分析

1. 单例类只能有一个实例。
2. 单例类必须自己创建自己的唯一实例。
3. 单例类必须给所有其他对象提供这一实例。

### 模式解决问题

1. 控制实例数目，节省系统资源。
2. 保证该类只有一个实例，多处共享

### 优点

1. 内存中只有一个实例，减少了内存开销，尤其是频繁创建和销毁实例
2. 避免对资源的多重占用
3. 保证全局唯一实例从而保证实例状态一致

### 缺点

1. 没有接口，不能继承
2. 大量使用也会导致内存泄漏

### 模式应用场景

1. 要求生产唯一序列号。
2. WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来。
3. 创建的一个对象需要消耗的资源过多，比如 I/O 与数据库的连接等。

### 模式代码



```dart
class _SingleTon {
  _SingleTon._();
  //第一种方式调用
  factory _SingleTon() {
    return instance;
  }
  //第二种方式调用
  static _SingleTon instance = _SingleTon._();
  int count = 0;
}

class RunSingleTon implements Run {
  @override
  main() {
    print('单例模式创建');
    print(_SingleTon().count++);
    print(_SingleTon.instance.count++);
    print(_SingleTon.instance.count++);
  }
}
```



## 建造者模式

### 模式分析

建造者模式(Builder Pattern)使用多个简单的对象一步一步构建成一个复杂的对象。这种类型的设计模式属于创建型模式，它提供了一种创建对象的最佳方式。

一个 Builder 类会一步一步构造最终的对象。该 Builder 类是独立于其他对象的。

### 模式难点

如何抽象出不同特性需要很强的抽象思维

###  模式解决问题

主要解决在软件系统中，有时候面临着"一个复杂对象"的创建工作，其通常由各个部分的子对象用一定的算法构成；由于需求的变化，这个复杂对象的各个部分经常面临着剧烈的变化，但是将它们组合在一起的算法却相对稳定。

### 优点

1. 建造者独立，易扩展。
2. 便于控制细节风险。

### 缺点

1. 产品必须有共同点，范围有限制。
2. 如内部变化复杂，会有很多的建造类。

### 模式应用场景

1. 需要生成的对象具有复杂的内部结构。
2. 需要生成的对象内部属性本身相互依赖。

### 模式代码



```dart
//建造者模式

import 'run.dart';

//建造者模式的精髓在于抽象，要把内容都抽象成一个一个的类来实现
//标识食物的接口
abstract class Item {
  String name;
  Packing packing();
  double price;
}

//包装接口
abstract class Packing {
  String pack();
}

//实现包装接口的实体类
class Wrapper implements Packing {
  @override
  pack() {
    return "Wrapper";
  }
}

class Bottle implements Packing {
  @override
  String pack() {
    return "Bottle";
  }
}

///创建实现Item接口的抽象类
//使用包装纸包装的食物
abstract class Burger implements Item {
  @override
  Packing packing() {
    return new Wrapper();
  }
}
//使用瓶子包装的食物
abstract class ColdDrink implements Item {
  @override
  Packing packing() {
    return new Bottle();
  }
}

//创建扩展了Burger和ColdDrink的实体类
//蔬菜
class VegBurger extends Burger {
  @override
  String name = "Veg Burger";

  @override
  double price = 34.3;
}

//鸡肉汉堡包
class ChickenBurger extends Burger {
  @override
  String name = "Chicken Burger";
  @override
  double price = 56.2;
}

//可乐
class Coke extends ColdDrink {
  @override
  String name = "Coke";
  @override
  double price = 3;
}

//百事
class Pepsi extends ColdDrink {
  @override
  String name = "Pepsi";
  @override
  double price = 4;
}

//创建Meal类用于打印实例化的对象信息
class Meal {
  List<Item> _items = [];
  void addItem(Item item) => _items.add(item);
  //计算所有项目的总价
  double getCost() {
    double cost = 0;
    for (var item in _items) {
      cost += item.price;
    }
    return cost;
  }

  //展示当前项目
  showItems() {
    for (var item in _items) {
      print("item:" +
          item.name +
          ", Packing: " +
          item.packing().pack() +
          ", Price:" +
          item.price.toString());
    }
  }
}

class MealBuilder {
  Meal prepareVegMeal() {
    Meal meal = new Meal();
    meal.addItem(new VegBurger());
    meal.addItem(new Coke());
    return meal;
  }

  Meal prepareNonVegMeal() {
    Meal meal = new Meal();
    meal.addItem(new ChickenBurger());
    meal.addItem(new Pepsi());
    return meal;
  }
}

class RunBuilderPattern implements Run {
  @override
  main() {
    print('建造者模式创建');
    MealBuilder mealBuilder = new MealBuilder();
    Meal vegMeal = mealBuilder.prepareVegMeal();
    print("Veg Meal");
    vegMeal.showItems();
    print("Total Cost: " + vegMeal.getCost().toString());

    Meal nonVegMeal = mealBuilder.prepareNonVegMeal();
    print("\n\nNon-Veg Meal");
    nonVegMeal.showItems();
    print("Total Cost: " + nonVegMeal.getCost().toString());
  }
}
```

## 原型模式

### 模式分析

原型模式(Prototype Pattern)是用于创建`重复的对象`，同时又能保证性能。这种类型的设计模式属于`创建型模式`，它提供了一种创建对象的最佳方式。

这种模式是实现了一个原型接口，该接口用于创建当前对象的克隆。当直接创建对象的代价比较大时，则采用这种模式。例如，一个对象需要在一个高代价的数据库操作之后被创建。我们可以缓存该对象，在下一个请求时返回它的克隆，在需要的时候更新数据库，以此来减少数据库调用。

### 模式解决问题

1. 当一个系统应该独立于它的产品创建，构成和表示时。
2. 当要实例化的类是在运行时刻指定时，例如，通过动态装载。
3. 为了避免创建一个与产品类层次平行的工厂类层次时。
4. 当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。

### 模式难点

克隆

### 优点

1. 性能提高。
2. 逃避构造函数的约束。

### 缺点

1. 配备克隆方法需要对类的功能进行通盘考虑，这对于全新的类不是很难，但对于已有的类不一定很容易，特别当一个类引用不支持串行化的间接对象，或者引用含有循环结构的时候。
2. 必须实现 Cloneable 接口。

### 模式应用场景

1. 资源优化场景。
2. 类初始化需要消化非常多的资源，这个资源包括数据、硬件资源等。
3. 性能和安全要求的场景。
4. 通过 new 产生一个对象需要非常繁琐的数据准备或访问权限，则可以使用原型模式。
5. 一个对象多个修改者的场景。
6. 一个对象需要提供给其他对象访问，而且各个调用者可能都需要修改其值时，可以考虑使用原型模式拷贝多个对象供调用者使用。
7. 在实际项目中，原型模式很少单独出现，一般是和工厂方法模式一起出现，通过 clone 的方法创建一个对象，然后由工厂方法提供给调用者。原型模式已经与 Java 融为浑然一体，大家可以随手拿来使用。

###  模式代码



```dart
abstract class ProtoType{
  String _id;
  ProtoType(this._id);
  String get id=>_id;
  void set id(String id)=>this._id=id;
  ProtoType clone();
}
class ConcretePrototype extends ProtoType{
  ConcretePrototype(String id,this.name) : super(id);
  final String name;
  @override
  ConcretePrototype clone() {
    ProtoType protoType = new ConcretePrototype(this.id,this.name);
    return protoType;
  }
}
class RunPrototype implements Run {
  @override
  main(){
    var a= ConcretePrototype("1","protoType模式");
    var b= a.clone();
    print("实例a的hashCode:"+a.hashCode.toString());
    print("实例b的hashCode:"+b.hashCode.toString());
    print("实例a的name:"+a.name);
    print("实例b的name:"+b.name);
  }
  @override
  String name="原型模式";
}
```

## 适配器模式

### 模式分析

适配器模式(Adapter Pattern)是作为两个不兼容的接口之间的桥梁。这种类型的设计模式属于结构型模式，它结合了两个独立接口的功能。 这种模式涉及到一个单一的类，该类负责加入独立的或不兼容的接口功能。举个真实的例子，读卡器是作为内存卡和笔记本之间的适配器。您将内存卡插入读卡器，再将读卡器插入笔记本，这样就可以通过笔记本来读取内存卡。 我们通过下面的实例来演示适配器模式的使用。其中，音频播放器设备只能播放 mp3 文件，通过使用一个更高级的音频播放器来播放 vlc 和 mp4 文件。

### 模式难点

###  模式解决问题

主要解决在软件系统中，常常要将一些"现存的对象"放到新的环境中，而新环境要求的接口是现对象不能满足的。

### 优点

1. 可以让任何两个没有关联的类一起运行。
2. 提高了类的复用。
3. 增加了类的透明度。
4. 灵活性好。

### 缺点

1. 过多地使用适配器，会让系统非常零乱，不易整体进行把握。比如，明明看到调用的是 A 接口，其实内部被适配成了 B 接口的实现，一个系统如果太多出现这种情况，无异于一场灾难。因此如果不是很有必要，可以不使用适配器，而是直接对系统进行重构。 2.由于 JAVA 至多继承一个类，所以至多只能适配一个适配者类，而且目标类必须是抽象类。

### 模式应用场景

有动机地修改一个正常运行的系统的接口，这时应该考虑使用适配器模式。

### 模式代码



```dart
import 'run.dart';

//官方媒体播放接口
abstract class MediaPlayer {
  void play(String audioType, String fileName);
}

//自定义媒体播放接口
abstract class AdvancedMediaPlayer {
  void playVlc(String fileName);
  void playMp4(String fileName);
}

//创建实现了 AdvancedMediaPlayer 接口的实体类。
class VlcPlayer implements AdvancedMediaPlayer {
  @override
  void playMp4(String fileName) {}

  @override
  void playVlc(String fileName) {
    print("Playing vlc file. Name: " + fileName);
  }
}

class Mp4Player implements AdvancedMediaPlayer {
  @override
  void playVlc(String fileName) {
    //什么也不做
  }

  @override
  void playMp4(String fileName) {
    print("Playing mp4 file. Name: " + fileName);
  }
}

// 创建实现了 MediaPlayer 接口的适配器类。
class MediaAdapter implements MediaPlayer {
  AdvancedMediaPlayer advancedMusicPlayer;
  MediaAdapter(String audioType) {
    if (audioType == "vlc") {
      advancedMusicPlayer = new VlcPlayer();
    } else if (audioType == "mp4") {
      advancedMusicPlayer = new Mp4Player();
    }
  }
  @override
  void play(String audioType, String fileName) {
    if (audioType == "vlc") {
      advancedMusicPlayer.playVlc(fileName);
    } else if (audioType == "mp4") {
      advancedMusicPlayer.playMp4(fileName);
    }
  }
}

// 创建实现了 MediaPlayer 接口的实体类。
class AudioPlayer implements MediaPlayer {
  MediaAdapter mediaAdapter;

  @override
  void play(String audioType, String fileName) {
    //播放 mp3 音乐文件的内置支持
    if (audioType == "mp3") {
      print("Playing mp3 file. Name: " + fileName);
    }
    //mediaAdapter 提供了播放其他文件格式的支持
    else if (audioType == "vlc" || audioType == "mp4") {
      mediaAdapter = new MediaAdapter(audioType);
      mediaAdapter.play(audioType, fileName);
    } else {
      print("Invalid media. " + audioType + " format not supported");
    }
  }
}

class RunAdapter implements Run {
  @override
  main() {
    AudioPlayer audioPlayer = new AudioPlayer();
    audioPlayer.play("mp3", "beyond the horizon.mp3");
    audioPlayer.play("mp4", "alone.mp4");
    audioPlayer.play("vlc", "far far away.vlc");
    audioPlayer.play("avi", "mind me.avi");
  }

  @override
  String name = "适配器模式";
}
```

## 桥接模式(Bridge)

###  模式分析

桥接(Bridge)是用于把抽象化与实现化解耦，使得二者可以独立变化。这种类型的设计模式属于结构型模式，它通过提供抽象化和实现化之间的桥接结构，来实现二者的解耦。

这种模式涉及到一个作为桥接的接口，使得实体类的功能独立于接口实现类。这两种类型的类可被结构化改变而互不影响。

我们通过下面的实例来演示桥接模式(Bridge Pattern)的用法。其中，可以使用相同的抽象类方法但是不同的桥接实现类，来画出不同颜色的圆。

### 模式难点

### 模式解决问题

在有多种可能会变化的情况下，用继承会造成类爆炸问题，扩展起来不灵活。

### 优点

1. 抽象和实现的分离。
2. 优秀的扩展能力。
3. 实现细节对客户透明。

### 缺点

桥接模式的引入会增加系统的理解与设计难度，由于聚合关联关系建立在抽象层，要求开发者针对抽象进行设计与编程。

### 模式应用场景

1. 如果一个系统需要在构件的抽象化角色和具体化角色之间增加更多的灵活性，避免在两个层次之间建立静态的继承联系，通过桥接模式可以使它们在抽象层建立一个关联关系。
2. 对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，桥接模式尤为适用。
3. 一个类存在两个独立变化的维度，且这两个维度都需要进行扩展。

### 模式代码



```dart
//咖啡售卖系统中，容量作为抽象化(Abstraction)，口味作为实现化(Implementor)
//创建口味接口
abstract class ICoffeeAdditives {
  void addSomething();
}

// 创建抽象化类
abstract class Coffee {
  ICoffeeAdditives additives;
  Coffee(this.additives);
  void orderCoffee(int count);
}

//修正抽象化类，增加品控方法
abstract class RefinedCoffee extends Coffee {
  RefinedCoffee(ICoffeeAdditives additives) : super(additives);
  checkQuality(int score) {
    print("添加" + score.toString() + "%");
  }
}

//创建实现化部分
class Milk implements ICoffeeAdditives {
  @override
  void addSomething() {
    print("加奶");
  }
}

class Sugar implements ICoffeeAdditives {
  @override
  void addSomething() {
    print("加糖");
  }
}

class LargeCoffee extends RefinedCoffee {
  LargeCoffee(ICoffeeAdditives additives) : super(additives);

  @override
  void orderCoffee(int count) {
    additives.addSomething();
    print("大杯咖啡" + count.toString() + "杯");
  }
}
class MidCoffee extends RefinedCoffee {
  MidCoffee(ICoffeeAdditives additives) : super(additives);

  @override
  void orderCoffee(int count) {
    additives.addSomething();
    print("中杯咖啡" + count.toString() + "杯");
  }
}
class RunBridge implements Run {
  @override
  String name = "桥接模式";

  @override
  main() {
    RefinedCoffee largeWithMilk = new LargeCoffee(Milk());
    largeWithMilk.orderCoffee(2);
    largeWithMilk.checkQuality(90);
    print("\n");
    RefinedCoffee largeWithSugar = new MidCoffee(Sugar());
    largeWithSugar.orderCoffee(1);
    largeWithSugar.checkQuality(100);
  }
}
```



##  装饰器模式(Decorator Pattern)

### 模式分析

装饰器模式(Decorator Pattern)允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。

这种模式创建了一个装饰类，用来包装原有的类，并在保持类方法签名完整性的前提下，提供了额外的功能。

我们通过下面的实例来演示装饰器模式的用法。其中，我们将把一个形状装饰上不同的颜色，同时又不改变形状类。

### 模式难点

实现现有抽象对象并传入已实现的抽象对象，并加入自定义方法

###  模式解决问题

一般的，我们为了扩展一个类经常使用继承方式实现，由于继承为类引入静态特征，并且随着扩展功能的增多，子类会很膨胀。

### 优点

装饰类和被装饰类可以独立发展，不会相互耦合，装饰模式是继承的一个替代模式，装饰模式可以动态扩展一个实现类的功能。

### 缺点

多层装饰比较复杂。

### 模式应用场景

1. 扩展一个类的功能。
2. 动态增加功能，动态撤销。

### 模式代码



```dart
//创建接口
abstract class Shape {
  void draw();
}

//创建接口实体类
class Rectangle implements Shape {
  @override
  void draw() {
    print("shape: Rectangle");
  }
}

class Circle implements Shape {
  @override
  void draw() {
    print("shape: Circle");
  }
}

//创建实现了Shape接口的抽象类
class ShapeDecorator implements Shape {
  final Shape decoratedShape;
  ShapeDecorator(this.decoratedShape);
  @override
  void draw() {
    decoratedShape.draw();
  }
}

//创建扩展了 ShapeDecorator 类的实体装饰类。
class RedShapeDecorator extends ShapeDecorator {
  RedShapeDecorator(Shape decoratedShape) : super(decoratedShape);
  @override
  void draw() {
    decoratedShape.draw();
    _setRedBorder(decoratedShape);
  }

  void _setRedBorder(Shape decoratedShape) {
    print("Border Color: Red");
  }
}
//使用 RedShapeDecorator 来装饰 Shape 对象。

class RunDecorator implements Run {
  @override
  void main() {
    Shape circle = new Circle();
    // ShapeDecorator redCircle = new RedShapeDecorator(new Circle());
    // ShapeDecorator redRectangle = new RedShapeDecorator(new Rectangle());
    Shape redCircle = new RedShapeDecorator(new Circle());
    Shape redRectangle = new RedShapeDecorator(new Rectangle());
    print("Circle with normal border");
    circle.draw();

    print("\nCircle of red border");
    redCircle.draw();

    print("\nRectangle of red border");
    redRectangle.draw();
  }

  @override
  String name = "装饰器模式";
}
```

## 外观模式(Facade)

###  模式分析

外观模式(Facade Pattern)隐藏系统的复杂性，并向客户端提供了一个客户端可以访问系统的接口。这种类型的设计模式属于结构型模式，它向现有的系统添加一个接口，来隐藏系统的复杂性。

这种模式涉及到一个单一的类，该类提供了客户端请求的简化方法和对现有系统类方法的委托调用。

### 模式难点

### 模式解决问题

降低访问复杂系统的内部子系统时的复杂度，简化客户端与之的接口。

###  优点

1. 减少系统相互依赖。
2. 提高灵活性。
3. 提高了安全性。

### 缺点

不符合开闭原则，如果要改东西很麻烦，继承重写都不合适。

###  模式应用场景

1. 为复杂的模块或子系统提供外界访问的模块。
2. 子系统相对独立。
3. 预防低水平人员带来的风险。

### 模式代码



```dart
abstract class Shape {
  void draw();
}

// 创建实现接口的实体类。
class Rectangle implements Shape {
  @override
  void draw() {
    print("Rectangle::draw()");
  }
}

class Square implements Shape {
  @override
  void draw() {
    print("Square::draw()");
  }
}

class Circle implements Shape {
  @override
  void draw() {
    print("Circle::draw()");
  }
}

// 创建一个外观类。
class ShapeMaker {
  Shape circle;
  Shape rectangle;
  Shape square;

  ShapeMaker() {
    circle = new Circle();
    rectangle = new Rectangle();
    square = new Square();
  }

  void drawCircle() {
    circle.draw();
  }

  void drawRectangle() {
    rectangle.draw();
  }

  void drawSquare() {
    square.draw();
  }
}

class RunFacade implements Run {
  @override
  void main() {
    ShapeMaker shapeMaker = new ShapeMaker();
    shapeMaker.drawCircle();
    shapeMaker.drawRectangle();
    shapeMaker.drawSquare();
  }

  @override
  String name = "外观模式";
}
```

## 享元模式(Flyweight)

### 模式分析

享元模式(Flyweight Pattern)主要用于减少创建对象的数量，以减少内存占用和提高性能。这种类型的设计模式属于结构型模式，它提供了减少对象数量从而改善应用所需的对象结构的方式。

享元模式尝试重用现有的同类对象，如果未找到匹配的对象，则创建新对象。我们将通过创建 5 个对象来画出 20 个分布于不同位置的圆来演示这种模式。由于只有 5 种可用的颜色，所以 color 属性被用来检查现有的 Circle 对象。

### 模式难点

使用Map讲重复创建的对象进行存储，需要严格分离出外部状态和内部状态

### 模式解决问题

在有大量对象时，有可能会造成内存溢出，我们把其中共同的部分抽象出来，如果有相同的业务请求，直接返回在内存中已有的对象，避免重新创建。

### 优点

大大减少对象的创建，降低系统的内存，使效率提高。

### 缺点

提高了系统的复杂度，需要分离出外部状态和内部状态，而且外部状态具有固有化的性质，不应该随着内部状态的变化而变化，否则会造成系统的混乱。

###  模式应用场景

1. 系统有大量相似对象。
2. 需要缓冲池的场景。

### 模式代码



```dart
import 'dart:math' as math;

import 'run.dart';

abstract class Shape {
  void draw();
}

// 创建实现接口的实体类。
class Circle implements Shape {
  String color;
  int x;
  int y;
  int radius;
  Circle(String color) {
    this.color = color;
  }
  void setX(int x) {
    this.x = x;
  }

  void setY(int y) {
    this.y = y;
  }

  void setRadius(int radius) {
    this.radius = radius;
  }

  @override
  void draw() {
    print("Circle: Draw() [Color : " +
        color +
        ", x : " +
        x.toString() +
        ", y :" +
        y.toString() +
        ", radius :" +
        radius.toString());
  }
}

// 创建一个工厂，生成基于给定信息的实体类的对象。
class ShapeFactory {
  static final Map<String, Shape> circleMap = new Map();

  static Shape getCircle(String color) {
    Circle circle = circleMap[color];

    if (circle == null) {
      circle = new Circle(color);
      circleMap[color] = circle;
      print("Creating circle of color : " + color);
    }
    return circle;
  }
}

class RunFlyweight implements Run {
  final List<String> colors = ["Red", "Green", "Blue", "White", "Black"];
  @override
  void main() {
    for (int i = 0; i < 20; ++i) {
      Circle circle = ShapeFactory.getCircle(getRandomColor());
      circle.setX(getRandomX());
      circle.setY(getRandomY());
      circle.setRadius(100);
      circle.draw();
    }
  }

  String getRandomColor() {
    return colors[math.Random().nextInt(colors.length)];
  }

  int getRandomX() {
    return math.Random().nextInt(100);
  }

  int getRandomY() {
    return math.Random().nextInt(100);
  }

  @override
  String name = "享元模式";
}
```



## 代理模式

### 模式分析

在代理模式(Proxy Pattern)中，一个类代表另一个类的功能。这种类型的设计模式属于结构型模式。

在代理模式中，我们创建具有现有对象的对象，以便向外界提供功能接口。

### 模式难点

### 模式解决问题

在直接访问对象时带来的问题，比如说：要访问的对象在远程的机器上。在面向对象系统中，有些对象由于某些原因(比如对象创建开销很大，或者某些操作需要安全控制，或者需要进程外的访问)，直接访问会给使用者或者系统结构带来很多麻烦，我们可以在访问此对象时加上一个对此对象的访问层。

### 优点

1. 你可以在客户端毫无察觉的情况下控制服务对象。
2. 如果客户端对服务对象的生命周期没有特殊要求， 你可以对生命周期进行管理。
3. 即使服务对象还未准备好或不存在， 代理也可以正常工作。
4. 开闭原则。 你可以在不对服务或客户端做出修改的情况下创建新代理。

### 缺点

1. 由于在客户端和真实主题之间增加了代理对象，因此有些类型的代理模式可能会造成请求的处理速度变慢。
2. 实现代理模式需要额外的工作，有些代理模式的实现非常复杂。

### 模式应用场景

按职责来划分，通常有以下使用场景：

1. 本地执行远程服务 (远程代理)。 适用于服务对象位于远程服务器上的情形。
2. 延迟初始化 (虚拟代理)。 如果你有一个偶尔使用的重量级服务对象， 一直保持该对象运行会消耗系统资源时， 可使用代理模式。
3. Copy-on-Write 代理。
4. 访问控制 (保护代理)。 如果你只希望特定客户端使用服务对象， 这里的对象可以是操作系统中非常重要的部分， 而客户端则是各种已启动的程序 (包括恶意程序)， 此时可使用代理模式。
5. Cache代理。
6. 防火墙(Firewall)代理。
7. 同步化(Synchronization)代理。
8. 智能引用(Smart Reference)代理。 可在没有客户端使用某个重量级对象时立即销毁该对象。
9. 记录日志请求 (日志记录代理)。 适用于当你需要保存对于服务对象的请求历史记录时。 代理可以在向服务传递请求前进行记录。

### 模式代码

```dart
abstract class Subject {
  void request();
}

class RealSubject implements Subject {
  const RealSubject();
  @override
  void request() {
    print("RealSubject:正在连接");
  }
}

class SubjectProxy implements Subject {
  final RealSubject _realSubject;
  const SubjectProxy(this._realSubject);
  @override
  void request() {
    if (this._checkAccess()) {
      this._realSubject.request();
      this._logAccess();
    }
  }

  bool _checkAccess() {
    print("Proxy: 判断当前是否可连接");
    return true;
  }

  void _logAccess() {
    print("Proxy: 写一些日志比如连接时间");
  }
}

class RunProxy implements Run {
  @override
  void main() {
    print('Client: 使用RealSubject类：');
    const realSubject = const RealSubject();
    _clientCode(realSubject);
    print('');
    print('Client: 使用代理类：');
    const proxy = const SubjectProxy(realSubject);
    _clientCode(proxy);
  }

  _clientCode(Subject subject) {
    // ...
    print('做一些连接前的事情');
    subject.request();

    // ...
  }

  @override
  String name = "代理模式";
}
```



## 发布订阅模式

### 模式分析

### 模式难点

### 模式解决问题

### 优点

### 缺点

### 模式应用场景

### 模式代码