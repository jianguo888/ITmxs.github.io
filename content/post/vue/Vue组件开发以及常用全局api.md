---
title: "Vue组件开发以及常用全局api"
date: 2021-09-16T08:49:43+08:00
draft: false
tags: ["Vue"]
categories: ["Vue"]
---

### 生命周期

**Vue 官网给出的生命周期图**

![Vue 实例生命周期](https://luckly007.oss-cn-beijing.aliyuncs.com/image/lifecycle.png)

![img](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)
这张图里包含了基本的生命周期，实际上生命周期钩子函数还有两个不在图内（activated，deactivated），这两个生命周期函数这里不给大家讲，如果大家有兴趣可以自行到 VUE 官网看文档，工作中用到他们的机会不多，这边讲也会扰乱大家的学习进程。这里我们通过代码来讲解 Vue 的生命周期

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
			},
            beforeCreate:function(){
                console.log('页面初始化之后立刻执行');
            },
            created:function(){
                console.log('页面初始化完成之后created,这里模板还没有渲染呢！');
            },
            beforeMount:function(){
                console.log('组件挂载之前beforeMount');
            },
            mounted:function(){
                console.log('组件挂载之后mounted');
            },
            beforeUpdate:function(){
                console.log('组件更新前beforeUpdate');
            },
            updated:function(){
                console.log('组件更新后updated');
            },
        //   activated:function(){
        //      console.log('activated');
        //    },
        //    deactivated:function(){
        //        console.log('deactivated');
        //    },    你只需要知道这两个生命周期是在这个位置进行就好了，别的可以不要想太多。
            beforeDestroy:function(){
                console.log('组件销毁之前beforeDestroy');
            },
            destroyed:function(){
                console.log('组件销毁后destroyed')
            }
        })
    </script>
</body>
</html>
```

这些生命周期函数大体就演示到这里，剩下的两个没能给大家展示的请大家牢记，我们开始学组件的时候，就可以体会到这两个钩子函数的用法了。

### 组件创建

**Vue.component**
注册全局组件
Vue.component("组件名"，{ 组件的各种属性（模板啊，data 啊，方法啊，生命周期啊之类的。） }), （视频里有一句口误，应该是小驼峰。手动捂脸）

这个全局 API 看过以后，我们来实现一个组件的 hello world

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
       <shuai-qm></shuai-qm>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        Vue.component("shuai-qm",{
            data(){
                return{
                    hello:'hello world'
                }
            },
            template:'<h1>{{hello}}</h1>'
        })
        
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello Word',
                isTrue:true,
            },
        })
    </script>
</body>
</html>
```

一个全局组件的 hello world 就完成了，但是大家是不是觉得全局组件非常不踏实？随时可能一个变动摧毁我们的项目？
那没关系，接下来是局部组建的注册方法。

**Vue 的 components 属性**

我们在注册局部组建的时候，需要把局部组建挂载到 Vue 构造器的 components 内，注意：全局 API 不带 s，而这个属性带 s。不说废话，我们先来构造一个。

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
	   <shuai-qm></shuai-qm>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
 
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello World',
                isTrue:true,
            },
              components:{
                   "shuaiQm":{
                       data(){
                           return{
                               hello:"hello world"
                           }
                       },
                       template:'<h1>{{hello}}</h1>'
                       }
                }
            
        })
    </script>
</body>
</html>
```

这样我们就得到了一个可以 hello world 的局部组件，但是，一堆组建都这个样子罗列出来的话，会将我们的项目弄得体无完肤？
所以我们抽离一下代码。变成这样

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
	  <shuai-qm></shuai-qm>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var shuaiQm = {
            data(){
                return{
                    hello:"hello world"
                }
            },
            template:'<h1>{{hello}}</h1>'
            }
 
 
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello World',
                isTrue:true,
            },
            components:{
                   "shuaiQm":shuaiQm,
            }
        })
    </script>
</body>
</html>
```

是不是用 vue-cli 开发过的同学就比较眼熟了？那么问题又来了，组件里面的 HTML 全都是写的字符串，都这么玩下去，要乱死啊，没问题，我们继续抽离。

这里介绍的就是 vue 的 template 用法了，马上又是一个 cli 党眼熟的东西

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<template id="shuaiQm">
    <h1>{{hello}}</h1>
</template>
 
<!-- 上面是html模板的用法，下面是script模板的用法，使用<script>标签时，type指定为text/x-template，意在告诉浏览器这不是一段js脚本，浏览器在解析HTML文档时会忽略<script>标签内定义的内容。-->
 
<!-- 推荐 -->
<script type="x-template" id="shuaiQm2">
    <h2 style="color:blue">{{hello}}</h2>
</script>
 
<body>
    <div id="app">
    {{message}}
	   <shuai-qm></shuai-qm>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var shuaiQm = {
            data(){
                return{
                    hello:"hello world"
                }
            },
            template:"#shuaiQm2"
            }
 
 
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello World',
                isTrue:true,
            },
            components:{
                   "shuaiQm":shuaiQm,
            }
        })
    </script>
</body>
</html>
```

拆出来以后是不是莫名其妙引起了极度舒适？当然后面我们学到使用 vue-cli 的时候 我们还会用到新的书写方法 .vue 文件的方法，这里就先不给大家多说了。

### 组件里面套组件（父子组件）

可能由同学会问了，组件里面还可以嵌套组件吗？我可以很负责任地告诉你，完全没问题！怎么写呢？你如果会在 Vue 实例的构造器里引用组件，你就会在别的组件内部引用组件，他们其实是一个写法。这边我使用我们的第三种模板写法来给大家书写。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<script type="x-template" id="father">
  <div>
    <h2 style="color:blue">{{hello}}</h2>
    <childer  />
  </div>
</script>
 
<script type="x-template" id="childer">
    <h2 style="color:blue">{{hello}}</h2>
</script>
 
<body>
    <div id="app">
    {{message}}
	<shuai-qm></shuai-qm>
    </div>
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var childer = {
            data(){
                return{
                    hello:"hello i'm dawangraoming"
                }
            },
            template:"#childer"
        }
 
 
        var father = {
            data(){
                return{
                    hello:"hello world"
                }
            },
            template:"#father",
            components:{
                "childer":childer
            }
            }
 
 
 
 
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello World',
                isTrue:true,
            },
            components:{
                   "shuaiQm":father,
            }
        })
    </script>
</body>
</html>
 
```

这里大家也可以看到，我们在 app 内部只引用了 shuaiQm 这一个组件， shuaiQm 又包含他的子组件 childer，因此父子都被渲染出来了。这就是父子组件的写法。

### 插槽 slot

这时候又有朋友要问了，如果我想在组件里面继续书写 HTML 怎么办呢？ slot 插槽就是个很好的东西了，这里我用代码给大家演示一下 slot 插槽的用法。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<script type="x-template" id="shuaiQm2">
    <div style="color:blue">
        <h2>{{hello}}</h2>
        <solt name="solt1">我是插槽solt1</solt>
        <solt name="solt2">我是插槽solt2</solt>
        <!-- 如果插槽不使用的话，内部的默认文字就会展示出来 -->
    </div>
</script>
 
<body>
    <div id="app">
    {{message}}
	   <shuai-qm>
           <span slot="solt1">hello world</span>
       </shuai-qm>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var shuaiQm = {
            data(){
                return{
                    hello:"hello world"
                }
            },
            template:"#shuaiQm2"
            }
 
 
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello World',
                isTrue:true,
            },
            components:{
                   "shuaiQm":shuaiQm,
            }
        })
    </script>
</body>
</html>
```

插槽只有这一个作用吗？不，那你就太小看插槽了，接下来要介绍一下插槽的作用域插槽用法。

**作用域插槽，听不懂可跳过，后面还会详细讲解**

使用时候子组件标签 Child 中要有 template scope=”scopeName” 标签，再通过 scopeName.childProp 就可以调用子组件模板中的 childProp 绑定的数据，所以作用域插槽是一种子传父传参的方式，解决了普通 slot 在 parent 中无法访问 child 数据的去问题；
这么说太复杂了，直接上个例子显而易见。
如果这里听不懂可以暂时跳过，只需要会用 slot 插槽的基础用法即可，在后面讲 Element 项目的时候，我会结合实例给大家讲解这个作用域插槽。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<script type="x-template" id="shuaiQm2">
    <div style="color:blue">
        <h2>{{hello}}</h2>
        <slot name="item" v-for="item in items" :text="item.text" :myname="item.myname" >
            slot的默认内容
        </slot>
    </div>
</script>
 
<body>
    <div id="app">
    {{message}}
	   <shuai-qm>
          <template slot="item" scope="props">
            <li>{{props.myname}}</li>
            <li>{{props.text}}</li>
          </template>
       </shuai-qm>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var shuaiQm = {
            data(){
                return{
                    hello:"hello world",
                    items:[{
                      text:"我在子组件内，通过插槽展示在父组件",
                      myname:"Qm",
                    },{
                      text:"我在子组件内，通过插槽展示在父组件",
                      myname:"奇淼",
                    }]
                }
            },
            template:"#shuaiQm2"
            }
 
 
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello World',
                isTrue:true,
            },
            components:{
                   "shuaiQm":shuaiQm,
            }
        })
    </script>
</body>
</html>
```

### prop 传递参数给组件（父传值给子）

讲到这里，已经到了 VUE 一个需要理解的地方了，父子传值，我们先讲解一下，如何将值传递给子组件，这个整体来说还是比较简单。引用我们的组件的标签上写上属性，并且把参数传入，这样我们在组件内部使用 props 就可以获得传过来的值了，我们还是以上面的代码为例。

```html
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<script type="x-template" id="father">
  <div>
    <h2 style="color:blue">{{hello}}</h2>
    {{apptoshuaiqm}}
    <childer :shuai-qmtochilder="shuaiQmGiveMe" />
  </div>
</script>
 
<script type="x-template" id="childer">
    <div>
        <h2 style="color:blue">{{hello}}</h2>
        {{shuaiQmtochilder}}
    </div>
</script>
 
<body>
    <div id="app">
	<shuai-qm apptoshuaiqm="我是app传过来的值" ></shuai-qm>
    </div>
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        var childer = {
          props:['shuaiQmtochilder'],
            data(){
                return{
                    hello:"hello i'm dawangraoming",
                }
            },
            template:"#childer"
        }
 
 
        var father = {
            props:["apptoshuaiqm"],// 这里大家一定要注意，请完全使用小写传参
            data(){
                return{
                    hello:"hello world",
                    shuaiQmGiveMe:"我是从shuaiQm传过来的值"
                }
            },
            template:"#father",
            components:{
                "childer":childer
            }
            }
 
 
 
 
        var app = new Vue({
            el:"#app",
            data:{
                message:'Hello World',
                isTrue:true,
            },
            components:{
                   "shuaiQm":father,
            }
        })
    </script>
</body>
</html>
 
```

这一段代码注意，再给 HTML 上面添加属性的时候，我们是不可以直接添加驼峰命名的，因为 HTML 不会区分大小写，所以我们建议属性的命名方式是完全小写或者横线命名的方式。如果我们使用横线命名来传递参数的话，在接收的时候，横线后面的首字母大写，变成小驼峰来接受，否则使用的时候它会被渲染成 NaN，这是为什么呢？别忘了我们一档讲过的，在插值表达式内，是支持简单计算的，- 会被当作减号处理，这里我会在视频中给大家详细讲解。

### 子组件传值给父组件

学到这里，如果大家已经有些迷茫，现在请先停下，喘口气，这里难度已经慢慢加大。我也会放慢讲解的速度。
如果我们想要获取到子组件内部的值，该怎么办呢？有什么办法能够让我们回去到内部的值呢？在这里，先给大家插播一个 JS 写法，我觉得这有助于理解子传父值。

```js
function thief (gold) {
    console.log(gold)
}
 
function richMan (){
    var money = 1000086
    thief(money)
}
 
richMan()
```

我们想要在 vue 中做到子传参给父，那我们的父组件就要像子组件伸出小偷之手。我在代码中为大家书写一下

```html
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<script type="x-template" id="shuaiQm">
  <div>
   
  </div>
</script>
 
<body>
    <div id="app">
      {{qmGold}}
	<shuai-qm :father="thief"></shuai-qm>
    </div>
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
 
        var shuaiQm = {
            props:["father"],
            data(){
                return{
                    money:"10000",
                }
            },
            template:"#shuaiQm",
            created() {
              this.father(this.money)
            },
            }
 
 
 
        var app = new Vue({
            el:"#app",
            data:{
              qmGold:0,
            },
            components:{
                   "shuaiQm":shuaiQm,
            },
            methods:{
              thief(gold){
                this.qmGold = gold
              }
            }
        })
    </script>
</body>
</html>
 
```

这样 你理解子传参给父了吗？

### 其余会用到的全局 API

**Vue.directivet**
Vue.directive 我们用来编写全局指令，它也有自己的生命周期

```js
// 注册
Vue.directive('my-directive', {
  bind: function () {},
  inserted: function () {},
  update: function () {},
  componentUpdated: function () {},
  unbind: function () {}
})
 
/*
bind：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。

inserted：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。

update：所在组件的 VNode 更新时调用，但是可能发生在其子 VNode 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。

componentUpdated：指令所在组件的 VNode 及其子 VNode 全部更新后调用。

unbind：只调用一次，指令与元素解绑时调用。

接下来我们来看一下钩子函数的参数 (即 el、binding、vnode 和 oldVnode)。

在这些钩子函数内部，都可以接受三个参数，我们来看看文档中的写法。
el：指令所绑定的元素，可以用来直接操作 DOM 。

binding：一个对象，包含以下属性：
    name：指令名，不包括 v- 前缀。
    value：指令的绑定值，例如：v-my-directive="1 + 1" 中，绑定值为 2。
    oldValue：指令绑定的前一个值，仅在 update 和 componentUpdated 钩子中可用。无论值是否改变都可用。
    expression：字符串形式的指令表达式。例如 v-my-directive="1 + 1" 中，表达式为 "1 + 1"。
    arg：传给指令的参数，可选。例如 v-my-directive:foo 中，参数为 "foo"。
    modifiers：一个包含修饰符的对象。例如：v-my-directive.foo.bar 中，修饰符对象为 { foo: true, bar: true }。
vnode：Vue 编译生成的虚拟节点。
oldVnode：上一个虚拟节点，仅在 update 和 componentUpdated 钩子中可用。

这里我会在视频中结合官方样例讲解
*/
```

上面我们罗列了这么多它的特性，不过真正开发中，我们最常用的只有 bind 和 update 这两个时期
我们可以简写为

```js
    Vue.directive('color', function (el, binding) {
        el.style.backgroundColor = binding.value
    })
```

下面我们来举个例子

```html
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<script type="x-template" id="shuaiQm">
  <div>
   
  </div>
</script>
 
<body>
    <div id="app">
      <div v-color="color">
          我来测试测试directive
      </div>
    </div>
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
        Vue.directive("color",function(el,binding){
            el.style.color = binding.value
        })
 
        var app = new Vue({
            el:"#app",
            data:{
                color:"red"
            }
        })
    </script>
</body>
</html>
```

好了我们可以看到加上 v-color 的这个 div 内部的文字变红了

**Vue.set**

Vue.set 官网给出的用法是 Vue.set( target, key, value ) 向响应式对象中添加一个属性，并确保这个新属性同样是响应式的，且触发视图更新。它必须用于向响应式对象上添加新属性，因为 Vue 无法探测普通的新增属性
这么听起来是有些笼统的，我给大家用代码展示一下它在我们日常开发中经常出现的场景。

```html
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<body>
    <div id="app">
      <ul>
        <li v-for="(item,ket) in list" :key="key">{{item.hello}}</li>
      </ul>
      <button @click="addList">+</button>
    </div>
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
 
        var app = new Vue({
            el:"#app",
            data:{
                list:[{hello:"hello world"},{hello:"hello two"}]
            },
            methods:{
                addList(){
                   this.list[0] = {hello:"ttt"}
                    console.log(this.list)
                }
            }
        })
    </script>
</body>
</html>
```

在上述代码中，我们通过 this.list[0]直接修改了数组中的第 0 项目对象，那么视图是没有更新的，但是数据确实变更了，这是为什么呢？因为 Vue 是通过 Object.defineProperty()来进行数据的监听，它的机制导致了它无法直接检测出数组中这种情况的变化。这时候我们就需要使用 Vue.set 了

```html
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<body>
    <div id="app">
      <ul>
        <li v-for="(item,ket) in list" :key="key">{{item.hello}}</li>
      </ul>
      <button @click="addList">+</button>
    </div>
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
 
        var app = new Vue({
            el:"#app",
            data:{
                list:[{hello:"hello world"},{hello:"hello two"}]
            },
            methods:{
                addList(){
                   this.list[0] = {hello:"ttt"}
                    console.log(this.list)
                }
            }
        })
    </script>
</body>
</html>
<!DOCTYPE html>
<html lang="zh-cn">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
 
<body>
    <div id="app">
      <ul>
        <li v-for="(item,ket) in list" :key="key">{{item.hello}}</li>
      </ul>
      <button @click="addList">+</button>
    </div>
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
 
        var app = new Vue({
            el:"#app",
            data:{
                list:[{hello:"hello world"},{hello:"hello two"}]
            },
            methods:{
                addList(){
                //    this.list[0] = {hello:"ttt"}
                    Vue.set(this.list,0, {hello:"我强制改变了！"})
                    // this.$set(this.list,0,{hello:"我强制改变了！"})  在methods 中可以写成 this.$set 
                    console.log(this.list)
                }
            }
        })
    </script>
</body>
</html>
 
```

看 是不是强制将它改变了呢？ 有了 Vue.set 数据就都不再得瑟了
