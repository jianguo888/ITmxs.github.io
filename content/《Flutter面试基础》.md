---
title: "《Flutter面试基础》"
subtitle: ""
date: 2021-04-24T22:39:27+08:00
lastmod: 2021-04-24T22:39:27+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter","面试 "]
categories: ["flutter","面试 "]

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



> 本文主要介绍flutter面试相关，分为网络，dart和flutter

<!--more-->

# **网络**

## **http 与 https 的区别？https 是如何工作的？**



http 是超文本传输协议，而 https 可以简单理解为安全的 http 协议。https 通过在 http 协议下添加了一层 ssl 协议对数据进行加密从而保证了安全。https 的作用主要有两点：建立安全的信息传输通道，保证数据传输安全；确认网站的真实性。

### **http 与 https 的区别主要如下：**

- https 需要到 CA 申请证书，很少免费，因而需要一定的费用
- http 是明文传输，安全性低；而 https 在 http 的基础上通过 ssl 加密，安全性高
- 二者的默认端口不一样，http 使用的默认端口是80；https使用的默认端口是 443

### **https 的工作流程**

提到 https 的话首先要说到加密算法，加密算法分为两类：对称加密和非对称加密。

- **对称加密：** 加密和解密用的都是相同的秘钥，优点是速度快，缺点是安全性低。常见的对称加密算法有 DES、AES 等等。
- **非对称加密：** 非对称加密有一个秘钥对，分为公钥和私钥。一般来说，私钥自己持有，公钥可以公开给对方，优点是安全性比对称加密高，缺点是数据传输效率比对称加密低。采用公钥加密的信息只有对应的私钥可以解密。常见的非对称加密包括RSA等。

在正式的使用场景中一般都是对称加密和非对称加密结合使用，使用非对称加密完成秘钥的传递，然后使用对称秘钥进行数据加密和解密。二者结合既保证了安全性，又提高了数据传输效率。

### **https 的具体流程如下：**

1. 客户端（通常是浏览器）先向服务器发出加密通信的请求

- 支持的协议版本，比如 TLS 1.0版
- 一个客户端生成的随机数 random1，稍后用于生成"对话密钥"
- 支持的加密方法，比如 RSA 公钥加密
- 支持的压缩方法

1. 服务器收到请求,然后响应

- 确认使用的加密通信协议版本，比如 TLS 1.0版本。如果浏览器与服务器支持的版本不一致，服务器关闭加密通信
- 一个服务器生成的随机数 random2，稍后用于生成"对话密钥"
- 确认使用的加密方法，比如 RSA 公钥加密
- 服务器证书

1. 客户端收到证书之后会首先会进行验证

- 首先验证证书的安全性
- 验证通过之后，客户端会生成一个随机数 pre-master secret，然后使用证书中的公钥进行加密，然后传递给服务器端

1. 服务器收到使用公钥加密的内容，在服务器端使用私钥解密之后获得随机数 pre-master secret，然后根据 radom1、radom2、pre-master secret 通过一定的算法得出一个对称加密的秘钥，作为后面交互过程中使用对称秘钥。同时客户端也会使用 radom1、radom2、pre-master secret，和同样的算法生成对称秘钥。
2. 然后再后续的交互中就使用上一步生成的对称秘钥对传输的内容进行加密和解密。

### **http头部的字段以及含义**

- **Accept :** 浏览器（或者其他基于HTTP的客户端程序）可以接收的内容类型（Content-types）,例如 `Accept: text/plain`
- **Accept-Charset：**浏览器能识别的字符集，例如 `Accept-Charset: utf-8`
- **Accept-Encoding：**浏览器可以处理的编码方式，注意这里的编码方式有别于字符集，这里的编码方式通常指gzip,deflate等。例如 `Accept-Encoding: gzip, deflate`
- **Accept-Language：**浏览器接收的语言，其实也就是用户在什么语言地区，例如简体中文的就是 `Accept-Language: zh-CN`
- **Authorization：**在HTTP中，服务器可以对一些资源进行认证保护，如果你要访问这些资源，就要提供用户名和密码，这个用户名和密码就是在Authorization头中附带的，格式是“username:password”字符串的base64编码
- **Cache-Control：**这个指令在request和response中都有，用来指示缓存系统（服务器上的，或者浏览器上的）应该怎样处理缓存，因为这个头域比较重要，特别是希望使用缓　存改善性能的时候
- **Connection：**告诉服务器这个user agent（通常就是浏览器）想要使用怎样的连接方式。值有keep-alive和close。http1.1默认是keep-alive。keep-alive就是浏览器和服务器　的通信连接会被持续保存，不会马上关闭，而close就会在response后马上关闭。但这里要注意一点，我们说HTTP是无状态的，跟这个是否keep-alive没有关系，不要认为keep-alive是对HTTP无状态的特性的改进。
- **Cookie：**浏览器向服务器发送请求时发送cookie，或者服务器向浏览器附加cookie，就是将cookie附近在这里的。例如：`Cookie:user=admin`
- **Content-Length：**一个请求的请求体的内存长度，单位为字节(byte)。请求体是指在HTTP头结束后，两个CR-LF字符组之后的内容，常见的有POST提交的表单数据，这个Content-Length并不包含请求行和HTTP头的数据长度。
- **Content-MD5：**使用base64进行了编码的请求体的MD5校验和。例如：`Content-MD5: Q2hlY2sgSW50ZWdyaXR5IQ==`
- **Content-Type：**请求体中的内容的mime类型。通常只会用在POST和PUT方法的请求中。例如：`Content-Type: application/x-www-form-urlencoded`
- **Date：**发送请求时的GMT时间。例如：`Date: Tue, 15 Nov 1994 08:12:31 GMT`
- **From：**发送这个请求的用户的email地址。例如：`From: user@example.com`
- **Host：**被服务器的域名或IP地址，如果不是通用端口，还包含该端口号，例如：`Host: www.some.com:182`
- **Proxy-Authorization：**连接到某个代理时使用的身份认证信息，跟Authorization头差不多。例如：`Proxy-Authorization: Basic QWxhZGRpbjpvcGVuIHNlc2FtZQ==`
- **User-Agent：**通常就是用户的浏览器相关信息。例如：`User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:12.0) Gecko/20100101 Firefox/12.0`
- **Warning：**记录一些警告信息。

# ** Dart**

### **1. Dart 当中的 「..」表示什么意思？**

Dart 当中的 「..」意思是 「级联操作符」，为了方便配置而使用。「..」和「.」不同的是 调用「..」后返回的相当于是 this，而「.」返回的则是该方法返回的值 。

#### 变量声明总结

1. var: 如果没有初始值，可以变成任何类型
2. dynamic:动态任意类型，编译阶段不检查类型
3. Object 动态任意类型，编译阶段检查类型区别：

1. 唯一区别 var 如果有初始值，类型被是锁定

#### **final 和 const 总结**

共同点

- 声明的类型可省略
- 初始化后不能再赋值
- 不能和 var 同时使用

区别（需要注意的地方）

- 类级别常量，使用 static ，const 。
- const 可使用其他 const 常量的值来初始化其值
- const 可使用其他 const 常量的值来初始化其值
- 可以更改非 final、非 const 变量的值，即使曾经具有 const 值
- const 导致的不可变性是可传递的
- 相同的 const 常量不会在内存中重复创建
- const 需要是编译时常量

### 操作符

#### 后缀操作

- 条件成员访问 和 . 类似，但是左边的操作对象不能为 null，例如 foo?.bar 如果 foo 为 null 则返回 null，否则返回 bar 成员

#### 取商操作符

- 被除数 ÷ 除数 = 商 ... 余数，A ~/ B = C，这个C就是商。相当于Java里的 /

#### 类型判定操作符

- as、is、is! 在运行时判定对象类型

#### 条件表达式

- 三目运算符 condition ? expr1 : expr2

#### 流程控制语句

if else

for , forEach , for-in



### **2. Dart 的作用域**

Dart 没有 「public」「private」等关键字，默认就是公开的，私有变量使用 下划线 _开头。“_”的限制范围并不是类访问级别的，而是库访问级别。

### **3. Dart 是不是单线程模型？是如何运行的？**

Dart 是单线程模型，运行的的流程如下图。

![图片](https://mmbiz.qpic.cn/mmbiz/82jN7o40p6kJ1IVJfDqXyX3VeYiaZqRhYNvc09SVhJx5RCicDFqkBwRxVDazFOGiaQC89FF12pTAeMc7vZGC1g0oA/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

简单来说，Dart 在单线程中是以消息循环机制来运行的，包含两个任务队列，一个是“微任务队列” microtask queue，另一个叫做“事件队列” event queue。

当Flutter应用启动后，消息循环机制便启动了。首先会按照先进先出的顺序逐个执行微任务队列中的任务，当所有微任务队列执行完后便开始执行事件队列中的任务，事件任务执行完毕后再去执行微任务，如此循环往复，生生不息。

### ** 4. Dart 是如何实现多任务并行的？**

Dart 是单线程的，不存在多线程，那如何进行多任务并行的呢？其实，Dart的多线程和前端的多线程有很多的相似之处。Flutter的多线程主要依赖Dart的并发编程、异步和事件驱动机制。


![图片](https://mmbiz.qpic.cn/mmbiz/xB67IcsqKk5zsuCYD1pBGyXx8PV53LmNT9ylsWLCfXUS59sDibrNDtjCSelgs4CsD89iaI9hibC3ghiccC7jDbwf9A/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

简单的说，在Dart中，一个Isolate对象其实就是一个isolate执行环境的引用，一般来说我们都是通过当前的isolate去控制其他的isolate完成彼此之间的交互，而当我们想要创建一个新的Isolate可以使用Isolate.spawn方法获取返回的一个新的isolate对象，两个isolate之间使用SendPort相互发送消息，而isolate中也存在了一个与之对应的ReceivePort接受消息用来处理，但是我们需要注意的是，ReceivePort和SendPort在每个isolate都有一对，只有同一个isolate中的ReceivePort才能接受到当前类的SendPort发送的消息并且处理。



### **future、async 和 await**

Future 是异步编程的解决方案，Future 是基于观察者模式的，它有 3 种状态：pending（进行中）、fulfilled（已成功）和 rejected（已失败）可以用 then 方法指定成功状态的回调函数 then 方法还可以接受一个可选命名参数，参数的名称是 onError，即失败状态的回调函数 Future 实例的函数中抛出了异常，被 onError 回调函数捕获到，并且可以看出 then 方法返回的还是一个 Future 对象，所以其实我们还可以利用 Future 对象的 cathError 进行链式调用从而捕获异常

在Dart1.9中加入了`async`和`await`关键字，有了这两个关键字，我们可以更简洁的编写异步代码，而不需要调用`Future`相关的API

将 `async` 关键字作为方法声明的后缀时，具有如下意义

- 被修饰的方法会将一个 `Future` 对象作为返回值
- 该方法会**同步**执行其中的方法的代码直到**第一个 await 关键字**，然后它暂停该方法其他部分的执行；
- 一旦由 **await** 关键字引用的 **Future** 任务执行完成，**await**的下一行代码将立即执行。

**async** 不是并行执行，它是遵循Dart **事件循环**规则来执行的，它仅仅是一个语法糖，简化`Future API`的使用。

### Dart异步编程中的 Stream数据流？

在Dart中，Stream 和 Future 一样，都是用来处理异步编程的工具。它们的区别在于，Stream 可以接收多个异步结果，而Future 只有一个。

Stream 的创建可以使用 Stream.fromFuture，也可以使用 StreamController 来创建和控制。还有一个注意点是：普通的 Stream 只可以有一个订阅者，如果想要多订阅的话，要使用 asBroadcastStream()。

### **Stream 有哪两种订阅模式？分别是怎么调用的**

Stream有两种订阅模式：单订阅(single) 和 多订阅（broadcast）。单订阅就是只能有一个订阅者，而广播是可以有多个订阅者。这就有点类似于消息服务（Message Service）的处理模式。单订阅类似于点对点，在订阅者出现之前会持有数据，在订阅者出现之后就才转交给它。而广播类似于发布订阅模式，可以同时有多个订阅者，当有数据时就会传递给所有的订阅者，而不管当前是否已有订阅者存在。

Stream 默认处于单订阅模式，所以同一个 stream 上的 listen 和其它大多数方法只能调用一次，调用第二次就会报错。但 Stream 可以通过 transform() 方法（返回另一个 Stream）进行连续调用。通过 Stream.asBroadcastStream() 可以将一个单订阅模式的 Stream 转换成一个多订阅模式的 Stream，isBroadcast 属性可以判断当前 Stream 所处的模式。

### **mixin机制**

mixin 是Dart 2.1 加入的特性，以前版本通常使用abstract class代替。简单来说，mixin是为了解决继承方面的问题而引入的机制，Dart为了支持多重继承，引入了mixin关键字，它最大的特殊处在于：mixin定义的类不能有构造方法，这样可以避免继承多个类而产生的父类构造方法冲突。

mixins的对象是类，mixins绝不是继承，也不是接口，而是一种全新的特性，可以mixins多个类，mixins的使用需要满足一定条件。



### **JIT 与 AOT**

借助于先进的工具链和编译器，Dart 是少数同时支持 JIT（Just In Time，即时编译）和 AOT（Ahead of Time，运行前编译）的语言之一。那，到底什么是 JIT 和 AOT 呢？语言在运行之前通常都需要编译，JIT 和 AOT 则是最常见的两种编译模式。JIT 在运行时即时编译，在开发周期中使用，可以动态下发和执行代码，开发测试效率高，但运行速度和执行性能则会因为运行时即时编译受到影响。AOT 即提前编译，可以生成被直接执行的二进制代码，运行速度快、执行性能表现好，但每次执行前都需要提前编译，开发测试效率低。

### 内存分配与垃圾回收

Dart VM 的内存分配策略比较简单，创建对象时只需要在堆上移动指针，内存增长始终是线性的，省去了查找可用内存的过程。在 Dart 中，并发是通过 Isolate 实现的。Isolate 是类似于线程但不共享内存，独立运行的 worker。这样的机制，就可以让 Dart 实现无锁的快速分配。Dart 的垃圾回收，则是采用了多生代算法。新生代在回收内存时采用“半空间”机制，触发垃圾回收时，Dart 会将当前半空间中的“活跃”对象拷贝到备用空间，然后整体释放当前空间的所有内存。回收过程中，Dart 只需要操作少量的“活跃”对象，没有引用的大量“死亡”对象则被忽略，这样的回收机制很适合 Flutter 框架中大量 Widget 销毁重建的场景。



# **Flutter**

### **简单介绍下Flutter框架，以及它的优缺点？**

Flutter是Google推出的一套开源跨平台UI框架，可以快速地在Android、iOS和Web平台上构建高质量的原生用户界面。同时，Flutter还是Google新研发的Fuchsia操作系统的默认开发套件。在全世界，Flutter正在被越来越多的开发者和组织使用，并且Flutter是完全免费、开源的。Flutter采用现代响应式框架构建，其中心思想是使用组件来构建应用的UI。当组件的状态发生改变时，组件会重构它的描述，Flutter会对比之前的描述，以确定底层渲染树从当前状态转换到下一个状态所需要的最小更改。

**优点**

- 热重载（Hot Reload），利用Android Studio直接一个ctrl+s就可以保存并重载，模拟器立马就可以看见效果，相比原生冗长的编译过程强很多；
- 一切皆为Widget的理念，对于Flutter来说，手机应用里的所有东西都是Widget，通过可组合的空间集合、丰富的动画库以及分层课扩展的架构实现了富有感染力的灵活界面设计；
- 借助可移植的GPU加速的渲染引擎以及高性能本地代码运行时以达到跨平台设备的高质量用户体验。简单来说就是：最终结果就是利用Flutter构建的应用在运行效率上会和原生应用差不多。

**缺点**

- 不支持热更新；
- 三方库有限，需要自己造轮子；
- Dart语言编写，增加了学习难度，并且学习了Dart之后无其他用处，相比JS和Java来说。

### **介绍下Flutter的理念架构**

其实也就是下面这张图。

![图片](https://mmbiz.qpic.cn/mmbiz/82jN7o40p6kJ1IVJfDqXyX3VeYiaZqRhYNHmyic3iaT8In33ayia2Va2Ploiceickic1mp3UelTaHgrymOENqL4Pro0dA/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

由上图可知，Flutter框架自下而上分为Embedder、Engine和Framework三层。其中，Embedder是操作系统适配层，实现了渲染 Surface设置，线程设置，以及平台插件等平台相关特性的适配；Engine层负责图形绘制、文字排版和提供Dart运行时，Engine层具有独立虚拟机，正是由于它的存在，Flutter程序才能运行在不同的平台上，实现跨平台运行；Framework层则是使用Dart编写的一套基础视图库，包含了动画、图形绘制和手势识别等功能，是使用频率最高的一层。

### **Flutter的FrameWork层和Engine层，以及它们的作用**

Flutter的FrameWork层是用Drat编写的框架（SDK），它实现了一套基础库，包含Material（Android风格UI）和Cupertino（iOS风格）的UI界面，下面是通用的Widgets（组件），之后是一些动画、绘制、渲染、手势库等。这个纯 Dart实现的 SDK被封装为了一个叫作 dart:ui的 Dart库。我们在使用 Flutter写 App的时候，直接导入这个库即可使用组件等功能。

Flutter的Engine层是Skia 2D的绘图引擎库，其前身是一个向量绘图软件，Chrome和 Android均采用 Skia作为绘图引擎。Skia提供了非常友好的 API，并且在图形转换、文字渲染、位图渲染方面都提供了友好、高效的表现。Skia是跨平台的，所以可以被嵌入到 Flutter的 iOS SDK中，而不用去研究 iOS闭源的 Core Graphics / Core Animation。Android自带了 Skia，所以 Flutter Android SDK要比 iOS SDK小很多。



### **介绍下Widget、State、Context 概念**

- **Widget**：在Flutter中，几乎所有东西都是Widget。将一个Widget想象为一个可视化的组件（或与应用可视化方面交互的组件），当你需要构建与布局直接或间接相关的任何内容时，你正在使用Widget。
- **Widget树**：Widget以树结构进行组织。包含其他Widget的widget被称为父Widget(或widget容器)。包含在父widget中的widget被称为子Widget。
- **Context**：仅仅是已创建的所有Widget树结构中的某个Widget的位置引用。简而言之，将context作为widget树的一部分，其中context所对应的widget被添加到此树中。一个context只从属于一个widget，它和widget一样是链接在一起的，并且会形成一个context树。
- **State**：定义了StatefulWidget实例的行为，它包含了用于”交互/干预“Widget信息的行为和布局。应用于State的任何更改都会强制重建Widget。

### **StatelessWidget和StatefulWidget两种状态组件类**

- **StatelessWidget**: 一旦创建就不关心任何变化，在下次构建之前都不会改变。它们除了依赖于自身的配置信息（在父节点构建时提供）外不再依赖于任何其他信息。比如典型的Text、Row、Column、Container等，都是StatelessWidget。它的生命周期相当简单：初始化、通过build()渲染。
- **StatefulWidget**: 在生命周期内，该类Widget所持有的数据可能会发生变化，这样的数据被称为State，这些拥有动态内部数据的Widget被称为StatefulWidget。比如复选框、Button等。State会与Context相关联，并且此关联是永久性的，State对象将永远不会改变其Context，即使可以在树结构周围移动，也仍将与该context相关联。当state与context关联时，state被视为已挂载。StatefulWidget由两部分组成，在初始化时必须要在createState()时初始化一个与之相关的State对象。

### **StatefulWidget 的生命周期**

Flutter的Widget分为StatelessWidget和StatefulWidget两种。其中，StatelessWidget是无状态的，StatefulWidget是有状态的，因此实际使用时，更多的是StatefulWidget。StatefulWidget的生命周期如下图

**![图片](https://mmbiz.qpic.cn/mmbiz/82jN7o40p6kJ1IVJfDqXyX3VeYiaZqRhY2U6WK9qswJMhcEE8JhtgPGib8RNTQkibdVMJjQzvBEQic6Vk53YwLzEXg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

- **initState()**：Widget 初始化当前 State，在当前方法中是不能获取到 Context 的，如想获取，可以试试 Future.delayed()
- **didChangeDependencies()**：在 initState() 后调用，State对象依赖关系发生变化的时候也会调用。
- **deactivate()**：当 State 被暂时从视图树中移除时会调用这个方法，页面切换时也会调用该方法，和Android里的 onPause 差不多。
- **dispose()**：Widget 销毁时调用。
- **didUpdateWidget**：Widget 状态发生变化的时候调用。



### **Widgets、RenderObjects 和 Elements的关系**

首先看一下这几个对象的含义及作用。

- **Widget** ：仅用于存储渲染所需要的信息。
- **RenderObject** ：负责管理布局、绘制等操作。
- **Element** ：才是这颗巨大的控件树上的实体。

Widget会被inflate（填充）到Element，并由Element管理底层渲染树。Widget并不会直接管理状态及渲染,而是通过State这个对象来管理状态。Flutter创建Element的可见树，相对于Widget来说，是可变的，通常界面开发中，我们不用直接操作Element,而是由框架层实现内部逻辑。就如一个UI视图树中，可能包含有多个TextWidget(Widget被使用多次)，但是放在内部视图树的视角，这些TextWidget都是填充到一个个独立的Element中。Element会持有renderObject和widget的实例。记住，Widget 只是一个配置，RenderObject 负责管理布局、绘制等操作。

在第一次创建 Widget 的时候，会对应创建一个 Element， 然后将该元素插入树中。如果之后 Widget 发生了变化，则将其与旧的 Widget 进行比较，并且相应地更新 Element。重要的是，Element 不会被重建，只是更新而已。

### **什么是状态管理，你了解哪些状态管理框架？**

Flutter中的状态和前端React中的状态概念是一致的。React框架的核心思想是组件化，应用由组件搭建而成，组件最重要的概念就是状态，状态是一个组件的UI数据模型，是组件渲染时的数据依据。

Flutter的状态可以分为全局状态和局部状态两种。常用的状态管理有ScopedModel、BLoC、Redux / FishRedux和Provider。详细使用情况和差异可以自行了解。



### **Flutter的绘制流程**

**![图片](https://mmbiz.qpic.cn/mmbiz/82jN7o40p6kJ1IVJfDqXyX3VeYiaZqRhYxNGtJddVK4HKNXQsxCODic26QSTVLqIuyBTFmgekDeexMILd62XHGCw/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)**

Flutter只关心向 GPU提供视图数据，GPU的 VSync信号同步到 UI线程，UI线程使用 Dart来构建抽象的视图结构，这份数据结构在 GPU线程进行图层合成，视图数据提供给 Skia引擎渲染为 GPU数据，这些数据通过 OpenGL或者 Vulkan提供给 GPU。

### Flutter的线程管理模型

默认情况下，Flutter Engine层会创建一个Isolate，并且Dart代码默认就运行在这个主Isolate上。必要时可以使用spawnUri和spawn两种方式来创建新的Isolate，在Flutter中，新创建的Isolate由Flutter进行统一的管理。

事实上，Flutter Engine自己不创建和管理线程，Flutter Engine线程的创建和管理是Embeder负责的，Embeder指的是将引擎移植到平台的中间层代码，Flutter Engine层的架构示意图如下图所示。

![图片](https://mmbiz.qpic.cn/mmbiz/82jN7o40p6kJ1IVJfDqXyX3VeYiaZqRhYmlD3lo31pxtTCD1W4JH2ETHfkXibMpCXLo9ptwhUHPZw5D3KeAGJyVg/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

在Flutter的架构中，Embeder提供四个Task Runner，分别是Platform Task Runner、UI Task Runner Thread、GPU Task Runner和IO Task Runner，每个Task Runner负责不同的任务，Flutter Engine不在乎Task Runner运行在哪个线程，但是它需要线程在整个生命周期里面保持稳定

### **Flutter 是如何与原生Android、iOS进行通信的？**

Flutter 通过 PlatformChannel 与原生进行交互，其中 PlatformChannel 分为三种：

- **BasicMessageChannel** ：用于传递字符串和半结构化的信息。
- **MethodChannel** ：用于传递方法调用（method invocation）。
- **EventChannel** : 用于数据流（event streams）的通信。

同时 Platform Channel 并非是线程安全的 ，更多详细可查阅闲鱼技术的 《深入理解Flutter Platform Channel》

### **简述Flutter 的热重载**

Flutter 的热重载是基于 JIT 编译模式的代码增量同步。由于 JIT 属于动态编译，能够将 Dart 代码编译成生成中间代码，让 Dart VM 在运行时解释执行，因此可以通过动态更新中间代码实现增量同步。

热重载的流程可以分为 5 步，包括：扫描工程改动、增量编译、推送更新、代码合并、Widget 重建。Flutter 在接收到代码变更后，并不会让 App 重新启动执行，而只会触发 Widget 树的重新绘制，因此可以保持改动前的状态，大大缩短了从代码修改到看到修改产生的变化之间所需要的时间。

另一方面，由于涉及到状态的保存与恢复，涉及状态兼容与状态初始化的场景，热重载是无法支持的，如改动前后 Widget 状态无法兼容、全局变量与静态属性的更改、main 方法里的更改、initState 方法里的更改、枚举和泛型的更改等。

可以发现，热重载提高了调试 UI 的效率，非常适合写界面样式这样需要反复查看修改效果的场景。但由于其状态保存的机制所限，热重载本身也有一些无法支持的边界。



### **Flutter 是怎么运转的？**

与用于构建移动应用程序的其他大多数框架不同，Flutter 是重写了一整套包括底层渲染逻辑和上层开发语言的完整解决方案。这样不仅可以保证视图渲染在 Android 和 iOS 上的高度一致性（即高保真），在代码执行效率和渲染性能上也可以媲美原生 App 的体验（即高性能）。这，就是

#### Flutter 和其他跨平台方案的本质区别：

React Native 之类的框架，只是通过 JavaScript 虚拟机扩展调用系统组件，由 Android 和 iOS 系统进行组件的渲染；

Flutter 则是自己完成了组件渲染的闭环。那么，Flutter 是怎么完成组件渲染的呢？这需要从图像显示的基本原理说起。在计算机系统中，图像的显示需要 CPU、GPU 和显示器一起配合完成：CPU 负责图像数据计算，GPU 负责图像数据渲染，而显示器则负责最终图像显示。CPU 把计算好的、需要显示的内容交给 GPU，由 GPU 完成渲染后放入帧缓冲区，随后视频控制器根据垂直同步信号（VSync）以每秒 60 次的速度，从帧缓冲区读取帧数据交由显示器完成图像显示。操作系统在呈现图像时遵循了这种机制，而 Flutter 作为跨平台开发框架也采用了这种底层方案。下面有一张更为详尽的示意图来解释 Flutter 的绘制原理。

Flutter 绘制原理可以看到，Flutter 关注如何尽可能快地在两个硬件时钟的 VSync 信号之间计算并合成视图数据，然后通过 Skia 交给 GPU 渲染：UI 线程使用 Dart 来构建视图结构数据，这些数据会在 GPU 线程进行图层合成，随后交给 Skia 引擎加工成 GPU 数据，而这些数据会通过 OpenGL 最终提供给 GPU 渲染。

## ** 架构**

### **组件化**

组件化又叫模块化，即基于可重用的目的，将一个大型软件系统（App）按照关注点分离的方式，拆分成多个独立的组件或模块。每个独立的组件都是一个单独的系统，可以单独维护、升级甚至直接替换，也可以依赖于别的独立组件，只要组件提供的功能不发生变化，就不会影响其他组件和软件系统的整体功能。

![图片](https://mmbiz.qpic.cn/mmbiz/82jN7o40p6kJ1IVJfDqXyX3VeYiaZqRhYKx8uDLj4mPPoj6ko8kCyQ4GQmib6jgPJYCfRhcasIhJJXTmficcMX2XQ/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以看到，组件化的中心思想是将独立的功能进行拆分，而在拆分粒度上，组件化的约束则较为松散。一个独立的组件可以是一个软件包（Package）、页面、UI 控件，甚至可能是封装了一些函数的模块。组件的粒度可大可小，那我们如何才能做好组件的封装重用呢？哪些代码应该被放到一个组件中？这里有一些基本原则，包括单一性原则、抽象化原则、稳定性原则和自完备性原则。接下来，我们先看看这些原则具体是什么意思。

**单一性原则**指的是，每个组件仅提供一个功能。分而治之是组件化的中心思想，每个组件都有自己固定的职责和清晰的边界，专注地做一件事儿，这样这个组件才能良性发展。一个反例是 Common 或 Util 组件，这类组件往往是因为在开发中出现了定义不明确、归属边界不清晰的代码：“哎呀，这段代码放哪儿好像都不合适，那就放 Common（Util）吧”。久而久之，这类组件就变成了无人问津的垃圾堆。所以，再遇到不知道该放哪儿的代码时，就需要重新思考组件的设计和职责了。



**抽象化原则**指的是，组件提供的功能抽象应该尽量稳定，具有高复用度。而稳定的直观表现就是对外暴露的接口很少发生变化，要做到这一点，需要我们提升对功能的抽象总结能力，在组件封装时做好功能抽象和接口设计，将所有可能发生变化的因子都在组件内部做好适配，不要暴露给它的调用方。



**稳定性原则**指的是，不要让稳定的组件依赖不稳定的组件。比如组件 1 依赖了组件 5，如果组件 1 很稳定，但是组件 5 经常变化，那么组件 1 也就会变得不稳定了，需要经常适配。如果组件 5 里确实有组件 1 不可或缺的代码，我们可以考虑把这段代码拆出来单独做成一个新的组件 X，或是直接在组件 1 中拷贝一份依赖的代码。



**自完备性**，即组件需要尽可能地做到自给自足，尽量减少对其他底层组件的依赖，达到代码可复用的目的。比如，组件 1 只是依赖某个大组件 5 中的某个方法，这时更好的处理方法是，剥离掉组件 1 对组件 5 的依赖，直接把这个方法拷贝到组件 1 中。这样一来组件 1 就能够更好地应对后续的外部变更了。



在理解了组件化的基本原则之后，我们再来看看组件化的具体实施步骤，即**剥离基础功能、抽象业务模块和最小化服务能力**。首先，我们需要剥离应用中与业务无关的基础功能，比如网络请求、组件中间件、第三方库封装、UI 组件等，将它们封装为独立的基础库；然后，我们在项目里用 pub 进行管理。如果是第三方库，考虑到后续的维护适配成本，我们最好再封装一层，使项目不直接依赖外部代码，方便后续更新或替换。基础功能已经封装成了定义更清晰的组件，接下来我们就可以按照业务维度，比如首页、详情页、搜索页等，去拆分独立的模块了。拆分的粒度可以先粗后细，只要能将大体划分清晰的业务组件进行拆分，后续就可以通过分布迭代、局部微调，最终实现整个业务项目的组件化。在业务组件和基础组件都完成拆分封装后，应用的组件化架构就基本成型了，最后就可以按照刚才我们说的 4 个原则，去修正各个组件向下的依赖，以及最小化对外暴露的能力了。

### **平台化**

从组件的定义可以看到，组件是个松散的广义概念，其规模取决于我们封装的功能维度大小，而各个组件之间的关系也仅靠依赖去维持。如果组件之间的依赖关系比较复杂，就会在一定程度上造成功能耦合现象。

如下所示的组件示意图中，组件 2 和组件 3 同时被多个业务组件和基础功能组件直接引用，甚至组件 2 和组件 5、组件 3 和组件 4 之间还存在着循环依赖的情况。一旦这些组件的内部实现和外部接口发生变化，整个 App 就会陷入不稳定的状态，即所谓牵一发而动全身

![图片](https://mmbiz.qpic.cn/mmbiz/82jN7o40p6kJ1IVJfDqXyX3VeYiaZqRhYma8X7sIiazGZa7jC88WliaWvtIlr1ZQNic9uhrAoIju0sObqMxE3fRfnA/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

平台化是组件化的升级，即在组件化的基础上，对它们提供的功能进行分类，统一分层划分，增加依赖治理的概念。为了对这些功能单元在概念上进行更为统一的分类，我们按照四象限分析法，把应用程序的组件按照业务和 UI 分解为 4 个维度，来分析组件可以分为哪几类。


![图片](https://mmbiz.qpic.cn/mmbiz/xB67IcsqKk5zsuCYD1pBGyXx8PV53LmNtPm1W7kaxnsZxccsoGQnNSgwrj1AiakibFvW62Lb1v2ic0pxiaBMyhAQlQ/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以看出，经过业务与 UI 的分解之后，这些组件可以分为 4 类：

具备 UI 属性的独立业务模块；

不具备 UI 属性的基础业务功能；

不具备业务属性的 UI 控件

不具备业务属性的基础功能


按照自身定义，这 4 类组件其实隐含着分层依赖的关系。比如，处于业务模块中的首页，依赖位于基础业务模块中的账号功能；再比如，位于 UI 控件模块中的轮播卡片，依赖位于基础功能模块中的存储管理等功能。我们将它们按照依赖的先后顺序从上到下进行划分，就是一个完整的 App 了


![图片](https://mmbiz.qpic.cn/mmbiz/xB67IcsqKk5zsuCYD1pBGyXx8PV53LmN1SibJnFvcvbvxSibPM8nQBKyhlHSh7zVZ4EBib7zCt23T21YwsDvJbBXQ/640?wx_fmt=other&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

可以看到，平台化与组件化最大的差异在于增加了分层的概念，每一层的功能均基于同层和下层的功能之上，这使得各个组件之间既保持了独立性，同时也具有一定的弹性，在不越界的情况下按照功能划分各司其职。



**与组件化更关注组件的独立性相比，平台化更关注的是组件之间关系的合理性，而这也是在设计平台化架构时需要重点考虑的单向依赖原则。**



所谓**单向依赖原则**，指的是组件依赖的顺序应该按照应用架构的层数从上到下依赖，不要出现下层模块依赖上层模块这样循环依赖的现象。这样可以最大限度地避免复杂的耦合，减少组件化时的困难。如果我们每个组件都只是单向依赖其他组件，各个组件之间的关系都是清晰的，代码解耦也就会变得非常轻松了。平台化强调依赖的顺序性，除了不允许出现下层组件依赖上层组件的情况，跨层组件和同层组件之间的依赖关系也应当严格控制，因为这样的依赖关系往往会带来架构设计上的混乱。


**如果下层组件确实需要调用上层组件的代码怎么办？**

这时，我们可以采用增加中间层的方式，比如 Event Bus、Provider 或 Router，以中间层转发的形式实现信息同步。比如，位于第 4 层的网络引擎中，会针对特定的错误码跳转到位于第 1 层的统一错误页，这时我们就可以利用 Router 提供的命名路由跳转，在不感知错误页的实现情况下来完成。又比如，位于第 2 层的账号组件中，会在用户登入登出时主动刷新位于第 1 层的首页和我的页面，这时我们就可以利用 Event Bus 来触发账号切换事件，在不需要获取页面实例的情况下通知它们更新界面。


**总****结**

简历不要写自己不会的

换工作要给自己留足够的时间，不然很被动，