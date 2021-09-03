---
title: "Kotlin初探基础1"
date: 2021-08-27T21:39:30+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

> 本文主要介绍

<!--more-->



# kotlin与jvm

![image-20210827213938556](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210827213938556.png)



# kotlin的跨平台性

Kotlin不仅支持编写代码在虚拟机上运行，而且还是一门垮平台的通用型语言，我们可以用Kotlin开发各种类型的原生应用，如Android、 macOS、Windows、Javascript应用。

Kotlin能脱离虚拟机层，直接编译成可以在Windows、Linux和macOS平台上运行的原生二进制代码。

关于变量

只读变量
要声明可修改变量，使用var关键字。
要声明只读变量，使用val关键字。

```
fun main(args: Array<String>) {
    val name :String ="breeze"
   var age:Int=10
    age+=1;
    print(name)
}
```

编译时常量

只读变量并非绝对只读。

编译时常量只能在函数之外定义，因为编译时常量必须在编译时赋值，而函数都是在运行时才调用，函数内的变量也是在运行时赋值，编译时常量要在这些变量赋值前就已存在。

编译时常量只能是常见的基本数据类型:String.Int、Double、Float.Long、Short、Byte、Char、Boolean。

查看Kotlin字节码
查看Kotlin编译之后的字节码，有助于理解kotlin语言
两种方式
Shift键两次，输入Show kotlin
Tools->Kotlin->Show Kotlin Bytecode

![image-20210827215526480](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210827215526480.png)

表达式

if/else if表达式range表达式
in A..B，in关键字用来检查某个值是否在指定范围之内。>when表达式
允许你编写条件式，在某个条件满足时，执行对应的代码只要代码包含else if分支，都建议改用when表达式

```
val school = "0小学"
    val level = when(school){
        "学前班"  -> "幼儿"
        "小学" -> "少儿"
        "中学" -> "青少年"
        else -> {
            println("未知")
        }
    }
```

string模板
>模板支持在字符串的引号内放入变量值
>还支持字符串里计算表达式的值并插入结果，添加在$轩中的任何表达式，都会作为字符串的一部分求值。

```
/*
fun main() {
    val origin = "Jack"
    val dest = "Rose"
    println("$origin love $dest")

    val flag = false
    println("Answer is: ${if(flag) "我可以" else "对比起"}")
}*/

```

![image-20210827225452780](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210827225452780.png)

函数参数
默认值参
如果不打算传入值参，可以预先给参数指定默认值
具名函数参数
如果使用命名值参，就可以不用管值参的顺序

Unit函数



>不是所有函数都有返回值，Kotlin中没有返回值的函数叫Unit函数，也就是说他们的返回类型是Unit。在Kotlin之前，函数不返回任何东西用void描述，意思是“没有返回类型，不会带来什么，忽略它”，也就是说如果函数不返回任何东西，就忽略类型。但是，void这种解决方案无法解释现代语言的一个重要特征，泛型。

Nothing类型
>TODO函数的任务就是抛出异常，就是永远别指望它运行成功，返回Nothing类
>型。

反引号中的函数名
Kotlin可以使用空格和特殊字符对函数命名，不过函数名要用一对反引号括起来。为了支持Kotlin和Java互操作，而Kotlin和Java各自却有着不同的保留关键字，不能作为函数名，使用反引号括住函数名就能避免任何冲突。



匿名函数
定义时不取名字的函数，我们称之为匿名函数，匿名函数通常整体传递给其他函数，
或者从其他函数返回。

>匿名函数对Kotlin来说很重要，有了它，我们能够根据需要制定特殊规则，轻松定制标准库里的内置函数。

```
fun main() {
    val total = "Mississippi".count()

    val totalS = "Mississippi".count{it == 's'}

    println(total)
    println(totalS)

    //变量的类型是一个匿名函数
    /*val blessingFunction:()->String = {
        val holiday = "New Year."
        "Happy $holiday"
    }*/

    /*val blessingFunction:(String) -> String = { name ->
        val holiday = "New Year."
        "$name, Happy $holiday"
    }*/

    /*val blessingFunction:(String) -> String = {
        val holiday = "New Year."
        "$it, Happy $holiday"
    }*/

    /*val blessingFunction = {
        val holiday = "New Year."
        "Happy $holiday"
    }*/

    /*val blessingFunction:(String,Int) -> String = {name, year ->
        val holiday = "New Year."
        "$name, Happy $holiday $year"
    }*/


    val blessingFunction = {name:String, year:Int ->
        val holiday = "New Year."
        "$name, Happy $holiday $year"
    }

    println(blessingFunction("Jack",2027))

}
```

函数类型与隐式返回
>匿名函数也有类型，匿名函数可以当作变量赋值给函数类型变量，就像其他变量一样，
>匿名函数就可以在代码里传递了。变量有类型，变量可以等于函数，函数也会有类型。函数的类型，由传入的参数和返回值类型决定。
>
>和具名函数不一样，除了极少数情况外，匿名函数不需要return关键字来返回数据，
>匿名函数会隐式或自动返回函数体最后一行语句的结果。

it关键字
定义只有一个参数的匿名函数时，可以使用it关键字来表示参数名。当你需要传入两个值参，it关键字就不能用了。