---
title: "在dart代码中使用list时的注意事项"
date: 2021-08-15T19:27:36+08:00
draft: false
tags: ["Flutter"]
categories: ["Flutter"]
---

列表是 Flutter 开发人员最喜欢的数据结构。但是有一些错误即使是资深开发者也可能会犯，这不会影响用户，但会严重影响您的应用程序的性能！那么让我们一一看看它们。





假设我们有一个 int 类型列表，如下所示：

```
List<int> _items = [];
```

# 1. 添加元素：

现在，要添加元素，我们有以下 4 种方法：

- .add(E value)：这个方法在列表的末尾添加一个类型为 E 的元素(这里是 int，因为我们有 List<int>)。
- .addAll(List<E> value)：此方法将列表中存在的所有元素添加`value`到使用 addAll 的另一个列表中。
- .insert(int index, E element)：这个方法在提到的索引处插入一个 E 类型的元素到列表中。
- .insertAll(int index, List<E> value)：此方法将`value`提到的索引处的列表插入到使用 insertAll 的列表中。

如果您希望在列表中添加单个元素，可以使用这些方法。

现在，如果您希望向我们的`_items`列表中添加一些初始值，通常我们使用 for 循环如下：

```
for(int i=0; i<10; i++){ 
   _items.add(i); 
}
```

## 但是，应尽可能限制*for*循环！它需要大量资源。

要做与上面相同的的事情，我们可以使用**.generate**的方法**列表**如下类别：

```
_items = List.generate(10, (index) => index);
print(_items);
```

上面的输出将是：

```
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
```

与 for 循环相比，这占用更少的内存。

# 2. 删除元素：

- 要删除特定元素，我们可以使用`.remove(E Value)`这 将删除`value`List 中第一次出现的元素。例如：`_items.remove(2)`这将从列表 _items 中删除该值`2`。
- 要从列表中删除最后一个元素，我们可以使用`.removeLast()`. 例如：`_items.removeLast()`。这将从列表中删除最后一个元素(此处为 value `9`)`_items`
- 要删除特定索引处的元素，我们可以使用`.removeAt(int index)`. 例如：`_items.removeAt(5)`。这将删除 的值`_items[5]`。

现在，假设我们要从列表中删除所有偶数。我们可以通过以下方式做到这一点：

- **使用 for 循环(不要使用)**：

```
for (int i = 0; i < _items.length; i++) { 
    if (_items[i] % 2 == 0) 
        _items.removeAt(i); 
}
```

**同样，尽可能少地使用 for 循环！**

- **使用 for-each 循环(比**`for`***更好***，我们有一个更好的解决方案)：

```
_items.forEach((element) { 
    if (element % 2 == 0) _items.remove(element); 
});
```

但是，如果直接执行上面的代码，则会出现异常

```
Concurrent modification during iteration: Instance(length:9) of '_GrowableList'.
```

它说你不能在访问它的同时删除元素。因此，要使用 for-each，您需要将`_items`列表分配给`_temp`列表，从`_temp`列表中删除数据并再次将`_temp`列表分配给`_items`列表，同时循环`_items`.

```dart
  List<int> _temp = [];
  _temp.addAll(_items);
  _items.forEach((element) {
    if (element % 2 == 0) _temp.remove(element);
  });
  _items.clear();
  _items.addAll(_temp);
```

所以，你可以看到很多代码只是为了删除一些元素。

- **使用**`.removeWhere`**这是根据某些条件删除元素的最有效方法之一！


```
_items.removeWhere((element) => element % 2 == 0);
```

# 3.更新元素：

要更新特定元素，我们没有任何内置方法。明显的 2 个解决方案是使用`for`和`for-each`循环。但它们的效率并不高。所以这里有一个技巧：

```
_items = _items.map((e) => e % 2 == 0 ? e + 1 : e).toList();
```

我们首先将`_items`列表转换为map ，然后对每个元素进行更新。然后我们再次将map 转换为列表并将其分配回`_items`

希望对你有用