---
title: "Vue构造期内常用属性"
date: 2021-09-16T13:56:04+08:00
draft: false
tags: ["Vue"]
categories: ["Vue"]
---

## 你能学到什么

VUE 构造器内部的各种属性的使用，methods，computed，watch，filters 这四个属性，在工作中会经常用到。还有很多混合属性例如 mixin 等，后期会有视频单独介绍，我们现在先做到入门可以开发，不常用的用法后期会给大家补充，学完这一期，你已经可以开发一个小项目玩一下了。

## methods 选项

```html
methods是我们VUE中的事件处理器，你可以把方法写在这里面，并且在构造器内部通过this.方法名调用，之前的学习中我们已经使用过这个方法，但是这里强调一下，千万不可以用箭头函数，毕竟这里我们的this是需要指向VUE实例的。在VUE构造器的外部我们也可以通过实例来调用方法。这里给大家举个例子。
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
        <!-- 这里我们通过click来调用了add和minus方法 -->
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
                    this.testMet()//这里我们通过this来调用事件处理器内的方法
                },
                testMet(){
                    console.log("我被调用了")
                }
			}
        })
        app.testMet()//这里我们通过app实例来调用事件处理器内的方法
    </script>
</body>
</html>
 
```

## computed 选项

```
还记得我们第一期讲的那个在差值表达式内写简单的JS表达式吗{{message.split('').reverse().join('')}}，
实际上这样处理数据是不优雅的，VUE为我们提供了computed这个选项来处理数据，我们称它为计算属性，当逻辑复杂的时候
我们就应当使用 computed计算属性了。
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
        <p>Original message: "{{ message }}"</p>
        <p>Computed reversed message: "{{ reversedMessage }}"</p>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
       var app = new Vue({
            el: '#app',
            data: {
                message: 'Hello'
            },
            computed: {
                // 计算属性的 getter
                reversedMessage: function () {
                // `this` 指向 app 实例
                return this.message.split('').reverse().join('')
                }
            }
        })
    </script>
</body>
</html>
```

这是一个计算属性的简单用例，实际上，计算属性内部存在 get 和 set 两个方法，我们称他为 getter 和 setter，这里我给大家上代码讲解。

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
        <p>{{firstName}}</p>
        <p>{{lastName}}</p>
        <p>{{fullName}}</p>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
       var app = new Vue({
    el: '#app',
    data: {
        firstName: 'Foo',
        lastName: 'Bar',
    },
  computed: {
    // 计算属性的 getter
    fullName:{
        // getter 在使用数据的时候触发
        get: function () {
            return this.firstName + ' ' + this.lastName
            },
        // setter 在修改数据的时候触发 newValue 就是我们修改完成以后的数据
        set: function (newValue) {
            var names = newValue.split(' ')
            this.firstName = names[0]
            this.lastName = names[names.length - 1]
            }
        }
    }
})
    </script>
</body>
</html>
```

vm.fullName = 'John Doe' 大家可以在外部直接修改 fullName 或者在控制台修改 都可以触发 fullName 的 set 事件来修改 firstName 和 lastName

## watch 选项

```html
Vue中我们想要盯着一个数据，在它发生变化的时候就要做什么事，这时候我们就要用到watch侦听器。
具体用法也是很简单的，我们先来一个浅监听。每次改变都会触发监听的function 它接受两个参数 当前的val和改变前的
oldval，我们可以根据这个做出判断
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
        <p>{{Name}}</p>
        <p>{{ChangeName}}</p>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
       var app = new Vue({
        el: '#app',
            data: {
                Name: 'QM',
                ChangeName: 'Cool',
                Names: 'QMS',
                NameDeep: 'QMDeep',
                NameImm: 'QMImm',
                NameObj:{
                    QM: 'shuai'
                }
                },
            watch:{
                Name:function (val,oldval) {
                    // 可以直接在这里写方法
                    console.log(val,oldval)
                },
                Names:[
                    function (val,oldval) {
                        // 多个方法可以使用数组的形式
                        console.log(val,oldval)
                    },
                    function (val,oldval) {
                        // 多个方法可以使用数组的形式
                        console.log(val,oldval)
                    }
                ],
                 // 深度 watcher
                NameDeep: {
                handler: function (val, oldVal) { /* ... */ },
                deep: true
                },
                // 该回调将会在侦听开始之后被立即调用
                NameImm: {
                handler: function (val, oldVal) { /* ... */ },
                immediate: true
                },
                //Obj的形式
                'NameObj.QM':function (val, oldVal){
                     /* ... */
                }
                
            }
        })
    </script>
</body>
</html>
```

## filters 选项

```html
Vue.js 允许你自定义过滤器，可被用于一些常见的文本格式化。过滤器可以用在两个地方：双花括号插值和 v-bind 表达式 (后者从 2.1.0+ 开始支持)。过滤器应该被添加在 JavaScript 表达式的尾部，由“管道”符号指示
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
        <!-- 在 `v-bind` 中 -->
            <div v-bind:id="rawId | capitalize">
                <!-- 在双花括号中 -->
                {{ message | capitalize }}
            </div>
    </div>
 
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
    <script>
       var app = new Vue({
        el: '#app',
            data: {
               message: "test",
               rawId: "change"
            },
            filters:{
                'capitalize':function(value){
                    if (!value) return ''
                    value = value.toString()
                    return value.charAt(0).toUpperCase() + value.slice(1)
                }
            }
                
            
        })
    </script>
</body>
</html>
```
