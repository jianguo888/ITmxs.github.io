---
title: "Go使用中的一些优化建议和技巧"
date: 2021-09-18T17:25:53+08:00
draft: false
tags: ["go"]
categories: ["go"]
---

### 预习

- 使用append对slice进行追加有什么问题没？如果slice太长了呢？
- 用到map如果需要顺序遍历怎么办？
- 如果只需要chan来控制开关或者触发事件，什么样的类型更节省内存？
- for和range有什么不一样？

### 优化建议

#### 1. 尽量创建定长的slice，避免被多次reslice

在清楚知道需要数据长度的情况，make创建的时候指定容量capacity以避免被resliced to the new element（重切到新元素）**。

因为当我们使用append对slice进行追加的时候，若slice的capacity小于1024则每次reslice 2倍，大于1024的时候每次reslice 1.25倍。

下面是slice的reslice过程capacity变化长度示例，来看看默认情况下reslice如何增长capacity。

```go
s := make([]int, 0)
m := make(map[int]struct{}, 0)
for i := 0; i < 2000; i++ {
  m[cap(s)] = struct{}{}
  s = append(s, i)
}
for k := range m {
  fmt.Println("cap", k)
}
```

这里输出的 cap 排序后为：0，1，2，4，8，16，32，64，128，256，512，1024，1280，1696，2304

这么多次的reslice则对应每次都要将数据copy到新的slice上，这样既浪费时间也浪费空间又浪费时间。在已知slice长度的情况下直接指定长度创建更好。将上面的例子改成如下

```go
// make(type, length, capacity) ，指定容量
s := make([]int, 0, 2000)

```

则此处输出的cap只有：20000，没有进行reslice，更加快速。

#### 2. 使用chan只想作为一个触发器不需要发送数据，建议使用 chan struct{}

一般不发送数据的chan建议使用chan struct{}，这样最节省内存空间，几乎不会有额外内存浪费。

空的结构体struct{}难道不占内存空间吗?来看看下面的输出：

```go
fmt.Println(unsafe.Sizeof(struct{}{})) // 输出：0
fmt.Println(unsafe.Sizeof(true)) // 输出：1
```

其实空结构体struct{}确实不占内存空间，所以空结构体广泛用来做一些占位符效果的东西。

#### 3. 遍历少用range多用for的下标，range会有副本拷贝

range遍历有3种写法：只遍历下标、只遍历值、同时遍历下标和值。

```go
// 第一种，只遍历下标
for index := range intSlice {
  fmt.Println(intSlice[index])
}
// 第二种，只遍历值，有数据副本拷贝
for _, value := range intSlice {
  fmt.Println(value)
}
// 第三种，同时遍历下标和值，有数据副本拷贝
for index, value := range intSlice {
  fmt.Println(index, value)
}

// for 的下标遍历，无额外开销
for index := 0; index < len(intSlice) {
  fmt.Println(intSlice[index])
}

```

但是呢！range遍历值的时候会有数据副本拷贝，也就是额外的开销。这就导致会有性能问题，所以在遍历的过程中尽量少偷懒，多写几个下标的表达式节省更多资源开销也节省时间。

再但是！如果只是迭代下标，那么for和range几乎没有区别。或者数据本身是指针类型，那么使用range也是可以的，因为range的副本直接引用到了指针上，不会有额外开销。

### 使用技巧

#### 1. 需要顺序遍历map怎么办？排序key

使用map的时候可能会遇到想要顺序遍历的情况，通常比较简单的做法就是单独存一份key的slice，然后排序key的slice，再遍历slice得到map的value。

有没有更简单的做法呢？目前对map顺序输出还没有什么好的解决办法，毕竟map的实现就是如此，只能借助外力因素来干涉map的输出顺序。如果有更好的解决办法欢迎评论区讨论留言。

### 复习

- slice 创建指定好容量避免被多次reslice
- 空的数据结构推荐使用**struct{}**不占内存，适合 chan struct{} 作为触发式channel
- range会拷贝数据副本，尽量避免
- 顺序遍历map需要额外开销存储并排序一份map的key
