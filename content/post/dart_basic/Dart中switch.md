---
title: "Dart中switch"
date: 2021-09-01T15:52:24+08:00
draft: true
---

在 Dart 中，switch-case 语句是嵌套 if-else 语句的简化版本。它的方法与Java中的方法相同。

**句法：**

```dart
switch ( expression ) { 
   case value1: { 
      // Body of value1
   } break; 
   case value2: { 
      //Body of value2 
   } break; 
   .
   .
   .
   default: { 
      //Body of default case  
   } break; 
} 
```

**默认情况下，如果上述情况均不符合条件，则执行其主体的情况。**

**切换案例要遵循的规则：**

1. 可以有任意数量的情况。但值不应重复。
2. case 语句只能包含常量。它不应是变量或表达式。
3. 应该有一个流量控制，即在案例中中断。如果省略它会显示错误。
4. 默认情况是可选的。
5. 嵌套开关也在那里，因此您可以在开关内部使用开关。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/output194.png)

**示例 1：普通 switch-case 语句**

```dart
void main()
{
	int gfg = 1;
	switch (gfg) {
	case 1: {
		print("GeeksforGeeks number 1");
	} break;
	case 2: {
		print("GeeksforGeeks number 2");
	} break;
	case 3: {
		print("GeeksforGeeks number 3");
	} break;
	default: {
		print("This is default case");
	} break;
	}
}

```

**示例 2：嵌套 switch-case 语句**

```dart
void main()
{
	int gfg1 = 1;
	String gfg2 = "Geek";
	switch (gfg1) {
	case 1: {
		switch (gfg2) {
		case 'Geek': {
			print("Welcome to GeeksforGeeks");
		}
		}
	} break;
	case 2: {
		print("GeeksforGeeks number 2");
	} break;
	default: {
		print("This is default case");
	} break;
	}
}

```

