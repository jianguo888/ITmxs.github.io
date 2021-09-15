---
title: "Vue"
date: 2021-09-15T15:46:53+08:00
draft: true
---

# Vue.js 起步

每个 Vue 应用都需要通过实例化 Vue 来实现。

语法格式如下：

```
var vm = new Vue({
  // 选项
})
```



```vue
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>Vue 测试实例 - </title>
	<script src="https://cdn.staticfile.org/vue/2.4.2/vue.min.js"></script>
</head>
<body>
	<div id="vue_det">
		<h1>site : {{site}}</h1>
		<h1>url : {{url}}</h1>
		<h1>{{details()}}</h1>
	</div>
	<script type="text/javascript">
		var vm = new Vue({
			el: '#vue_det',
			data: {
				site: "Breeze的blog",
				url: "www.luckly.work",
				alexa: "10000"
			},
			methods: {
				details: function() {
					return  this.site + " - 学习的不仅是技术，更是梦想！";
				}
			}
		})
	</script>
</body>
</html>
```

## 声明式渲染

Vue.js 的核心是一个允许采用简洁的模板语法来声明式地将数据渲染进 DOM 的系统：

```vue
<div id="app">
  {{ message }}
</div>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```

Hello Vue!

我们已经成功创建了第一个 Vue 应用！看起来这跟渲染一个字符串模板非常类似，但是 Vue 在背后做了大量工作。现在数据和 DOM 已经被建立了关联，所有东西都是**响应式的**。我们要怎么确认呢？打开你的浏览器的 JavaScript 控制台 (就在这个页面打开)，并修改 `app.message` 的值，你将看到上例相应地更新。

注意我们不再和 HTML 直接交互了。一个 Vue 应用会将其挂载到一个 DOM 元素上 (对于这个例子是 `#app`) 然后对其进行完全控制。那个 HTML 是我们的入口，但其余都会发生在新创建的 Vue 实例内部。

除了文本插值，我们还可以像这样来绑定元素 attribute：

```vue
<div id="app-2">
  <span v-bind:title="message">
    鼠标悬停几秒钟查看此处动态绑定的提示信息！
  </span>
</div>
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: '页面加载于 ' + new Date().toLocaleString()
  }
})
```

鼠标悬停几秒钟查看此处动态绑定的提示信息！

这里我们遇到了一点新东西。你看到的 `v-bind` attribute 被称为**指令**。指令带有前缀 `v-`，以表示它们是 Vue 提供的特殊 attribute。可能你已经猜到了，它们会在渲染的 DOM 上应用特殊的响应式行为。在这里，该指令的意思是：“将这个元素节点的 `title` attribute 和 Vue 实例的 `message` property 保持一致”。

如果你再次打开浏览器的 JavaScript 控制台，输入 `app2.message = '新消息'`，就会再一次看到这个绑定了 `title` attribute 的 HTML 已经进行了更新。

## 条件与循环

控制切换一个元素是否显示也相当简单：

```vue
<div id="app-3">
  <p v-if="seen">现在你看到我了</p>
</div>
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

现在你看到我了

继续在控制台输入 `app3.seen = false`，你会发现之前显示的消息消失了。

这个例子演示了我们不仅可以把数据绑定到 DOM 文本或 attribute，还可以绑定到 DOM **结构**。此外，Vue 也提供一个强大的过渡效果系统，可以在 Vue 插入/更新/移除元素时自动应用[过渡效果](https://cn.vuejs.org/v2/guide/transitions.html)。





![image-20210915163131144](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210915163131144.png)

还有其它很多指令，每个都有特殊的功能。例如，`v-for` 指令可以绑定数组的数据来渲染一个项目列表：

```vue
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: '学习 JavaScript' },
      { text: '学习 Vue' },
      { text: '整个牛项目' }
    ]
  }
})
```

1. 学习 JavaScript
2. 学习 Vue
3. 整个牛项目

在控制台里，输入 `app4.todos.push({ text: '新项目' })`，你会发现列表最后添加了一个新项目。

## 处理用户输入

为了让用户和你的应用进行交互，我们可以用 `v-on` 指令添加一个事件监听器，通过它调用在 Vue 实例中定义的方法：

```
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">反转消息</button>
</div>
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```

Hello Vue.js!

反转消息

注意在 `reverseMessage` 方法中，我们更新了应用的状态，但没有触碰 DOM——所有的 DOM 操作都由 Vue 来处理，你编写的代码只需要关注逻辑层面即可。

Vue 还提供了 `v-model` 指令，它能轻松实现表单输入和应用状态之间的双向绑定。

```
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```

Hello Vue!

### 核心基础 模板语法 数据绑定

上面代码中我们用了 {{message}}这样一个东西，这是什么呢？我们管这两个大括号叫插值表达式，插值表达式内部可以是简单的 JS 表达式，这里的{{message}}则被替换为了 data 内的 message 的数据。我们接下来举例一下简单的 JS 表达式。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
       {{message}}
       {{1 + 1}}
       {{isTrue?"真的":"假的"}}
       {{message.split('').reverse().join('')}}
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
 
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello Word',
                isTrue:true,
            }
        })
    </script>
</body>
</html>
```

大家可以看到，这些在插值表达式内的 JS 表达式直接返回了运行完成的结果，值得一提的是，差值表达式内的规则和 script 标签内的规则是类似的。

**接下来就是 VUE 的精髓，双向数据绑定。**
v-model 这个指令是 vue 中用来进行双向数据绑定的重要指令。大家需要注意，只有在表单元素中才可以使用 v-model。并且 v-model 把 dom 中的 value 和 vue 实例中的 data 绑定到了一起。两者的变化会相互影响。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
	    <input v-model="message" placeholder="我是v-model绑定的数据"></input>
       {{message}}
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
 
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello Word',
                isTrue:true,
            }
        })
    </script>
</body>
</html>
```

上述代码中，我们改变 input 中的 value 值，会发现，message 数据也在同时发生着变化。

### 常用指令 v-if v-show v-for

```
v-if v-else 根据表达式的值的真假条件渲染元素。
v-show 和v-if 差不多 但是 v-show会渲染dom只是隐藏掉了，而v-if则连dom都不会渲染，当满足条件的时候，dom才会出现。
```

在我们日常开发中，很多时候想要隐藏掉某些东西，只有在特定条件下才渲染出来，这里我们举个例子，模拟一下管理员权限。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
    <div id="app">
	    <ul>
		    <li v-for="(item,index) in list" :key="index">{{item.name}}</li>
	    </ul>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
 
        var app = new Vue({
            el:"#app",
            data:{
               list:[{name:"奇妙"},{name:"奇淼"},{name:"QM"}]
            }
        })
    </script>
</body>
</html>
```

这样我们通过 v-for 渲染出了一个列表。

### 数据绑定和事件绑定

**v-bind 数据绑定**
v-bind 我们称之为数据绑定，我们想要在标签内使用我们 data 中的数据怎么办？通过 v-bin 就可以实现。我们下面来看例子。v-bind:xxxx 可以简写为：xxxx

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
		.active{
			color:red
		}
	</style>
</head>
<body>
    <div id="app">
	    <div v-bind:class="myClass">
		    我是v-bind影响的数据
		</div>
		<div :style="bindColor">
		    我是v-bind影响的数据
		</div>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
               myClass:'active',
               bindColor:{
                   color:'red'
               }
            }
        })
    </script>
</body>
</html>
```

**v-on 事件绑定**

```
我们可以通过v-on来绑定事件，同样v-on可以简写为@，这里还需要提到在VUE示例中注册事件的地方，methods
方法我们都写在这个里面，废话不说，上代码。
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <style>
		.active{
			color:red
		}
	</style>
</head>
<body>
    <div id="app">
	    <button v-on:click="add">+</button>{{num}}<button @click="minus">-</button>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var app = new Vue({
            el:"#app",
            data:{
               num:1
            },
            methods:{
	            add(){
		            this.num++
	            },
	            minus(){
		             this.num--
	            }
			}
        })
    </script>
</body>
</html>
```

### 总结

现在这些是 VUE 中最简单的指令部分，学会他们，你已经基本可以写简单的 VUE 页面了，但是距离真正在工作中使用还有距离，这是 VUE 的第一步，看完视频后多多熟悉 API ，敲敲代码。祝你早日步入高级前端的行列。
