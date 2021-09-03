---
title: "《Dart语言基础之Map》"
subtitle: ""
date: 2021-03-29T19:18:14+08:00
lastmod: 2021-03-29T19:18:14+08:00
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

## Map定义

### 不指定泛型

```
  var map1 = {'aa': 'aaa', 'bb': 'bbb', 'cc': true}; //直接赋值初始值
  print(map1); //{aa: aaa, bb: bbb, cc: true}

  Map map2 = {'a': 'a1', 'b': 'b1'}; //直接赋值初始值
  print(map2); //{a: a1, b: b1}

  map2['c'] = 'c1';
  print(map2); //{a: a1, b: b1, c: c1}

  var map3 = new Map();
  map3['a'] = 'a3';
  map3[2] = 'b3';
  print(map3); //{a: a3, 2: b3}
  print(map3[3]); //null 如果不存在，则为null
  map3[3] = null; //key存在 value存在 value的值为null
  print(map3); //{a: a3, 2: b3, 3: null}
  
```

### 指定泛型

```
var map4 = <String, String>{};
  Map<int, String> map5 = new Map(); //new也可以省略
  map5[1] = 'a5';
  map5[2] = 'b5';
  print(map5); //{1: a5, 2: b5}

  Map<String, int> map6 = {'a6': 1, 'b6': 2};
  print(map6); //{a6: 1, b6: 2}
```

## Map常见属性

```
 Map<String, int> map7 = Map();
  map7['a7'] = 1;
  map7['b7'] = 2;
  map7['c7'] = 3;
  print(map7.length); //3 长度
  print(map7.isNotEmpty); //true  是否不为空
  print(map7.isEmpty); //flase  是否为空
  print(
      '${map7.keys},${map7.keys.runtimeType}'); // (a7, b7, c7),_CompactIterable<String>    Map的所有key
  print(
      '${map7.values},${map7.values.runtimeType}'); //(1, 2, 3),_CompactIterable<int>    Map的所有value
  print(
      '${map7.entries},${map7.entries.runtimeType}'); // (MapEntry(a7: 1), MapEntry(b7: 2), MapEntry(c7: 3)),MappedIterable<String, MapEntry<String, int> Map的所有键值对

```

## Map常见方法

- 新增一个键值对

```
  Map<String, int> map8 = Map();
  map8['a8'] = 1;
  print(map8); //{a8: 1}
  
```

------

- 修改一个键值对

```
  Map<String, int> map9 = {'a9': 1, 'b9': 2};
  map9['a9'] = 9;
  print(map9); //{a9: 9, b9: 2}  
  
```

------

- update(K key, V update(V value), {V ifAbsent()})  根据指定的Key对应的value做出修改，同时Map本身也会被修改

```
  Map<String, int> map10 = {'a10': 1, 'b10': 2, 'c10': 3};
  var resultMap10 = map10.update('b10', (value) => value * 2);
  print(resultMap10); //4
  print(map10); //{a10: 1, b10: 4, c10: 3}
  
  var resultMap101 = map10.update('c', (value) => (value * 2),
      ifAbsent: () => (10)); //如果key不存在，但是有ifAbsent参数，返回idAbsent函数的值，并添加到map中
  print('$resultMap101,${resultMap101.runtimeType}'); //10,int
  print(map10); //{a10: 1, b10: 4, c10: 3, c: 10}
 
```

------

- updateAll(V update(K key, V value)) 根据参数内的函数，修改map

```
  Map<String, int> map11 = {'a11': 2, 'b11': 3, 'c11': 4};
  var resultMap11 = map11.updateAll((key, value) {
    return value * 2;
  });
  print(map11); //{a11: 4, b11: 6, c11: 8}
  map11.updateAll((key, value) {
    if (key == 'a11') {
      return value * 2;
    }
    if (key == 'c11') {
      return value + 1;
    }
    return 7; //如果没有这行，b11对应的value为null 这里可以看出updateall会影响所有的键值对
  });
  print(map11); //{a11: 8, b11: 7, c11: 9}
  
```

------

- remove() 删除一个key

```
  Map<String, int> map12 = {'a12': 2, "b12": 1};
  map12.remove('a12');
  print(map12); //{b12: 1}
  map12.remove('c12'); //删除一个不存在的key，毫无影响，无报错无警告
  print(map12); //{b12: 1}  
  
```

------

- removeWhere(bool predicate(K key, V value))  根据函数条件批量删除key

```
 Map<String, int> map13 = {'a13': 3, 'b13': 4, 'c13': 1};
 map13.removeWhere((key, value) => value > 3);
 print(map13); //{a13: 3, c13: 1}  
 
```

------

- containsKey() 是否包含某个key  contrainsValue()是否包含某个value

```
   Map<String ,int> map14 = {'a14':1};
   bool resultMap14 = map14.containsKey('a11'); //false
   bool resultMap141 = map14.containsValue(1); //true
```

------

- forEach(void f(K key, V value)) 遍历Map ,遍历时不可add或者remove

```
  Map<String, int> map15 = {'a15': 1, 'b15': 2, 'c': 3, 'd': 4, 'e': 5};
  map15.forEach((key, value) {
    print('$key,$value');
    /*
    a15,1
    b15,2
     c,3
     d,4
     e,5
    */
  });
   map15.forEach((key, value) {
    //  if(key == 'a15'){value = 8;}
    map15['a15'] = 8;
   });
   print(map15); //{a15: 8, b15: 2, c: 3, d: 4, e: 5}
   
```

------

- map() 遍历每个键值对 根据参数函数，对keyvalue做出修改，转换成其他泛型Map

```
   Map<String,int> map16 = {'a16':7,"b16":5,'c16':4};
   Map<int,String> map17 = map16.map((key, value) {
       return MapEntry(value, key);
   });
   print(map17);
   
```

------

- addAll() 两个Map合并，类型需要一致 ，且如果key相同，则会覆盖value

```
  Map<String,int> map18 = {'a18':1,'b18':7,'a19':2};
  Map<String,int> map19 = {'a19':9};
  map18.addAll(map19);
  print(map18); //{a18: 1, b18: 7, a19: 9}   
  
```

------

- addEntres(key,value)  两个Map合并，类型需要一致 ，且如果key相同，则会覆盖value

```
  Map<String,int> map20 = {'a20':2,'b20':3};
  Map<String,int> map21 = {'a21':5,'b21':9};
  map20.addEntries(map21.entries);
  print(map20); //{a20: 2, b20: 3, a21: 5, b21: 9}  
  
```

- putIfAbsent()  存在key则返回value，查不到则返回值 不修改Map

```
 Map<String,int> map22 = {'a22':3,'b22':4};
 var resultMap22 = map22.putIfAbsent('a22', () => 2); //存在key则返回value，查不到则返回 2 不修改Map
 print('$resultMap22,$map22');//3,{a22: 3, b22: 4} 
 
 var resultMap221 = map2.putIfAbsent('a2', () => 1);
 print('$resultMap221,$map22'); //1,{a22: 3, b22: 4} //存在key则返回value，查不到则返回 1 不修改Map 
 
```

------

- cast() 泛型提升为其父祖类

```
 Map<String,int> map23 = {'a33':3};
 Map<Object,Object> map24 = map23.cast();
 print('$map23,${map23.runtimeType},$map24,${map24.runtimeType}'); //{a33: 3},_InternalLinkedHashMap<String, int>,{a33: 3},CastMap<String, int, Object, Object>

```

------

- 清除所有键值对，对所有类型map都适用

```
 Map<String,int> map25 = {'a25':2,'b25':3};
 map25.clear();
 print('$map25,${map25.runtimeType}'); //{},_InternalLinkedHashMap<String, int>
```


