---
title: "Vue_cli相关vuex以及Router"
date: 2021-09-16T13:57:48+08:00
draft: false
tags: ["Vue"]
categories: ["Vue"]
---

# Vue-cli3 Vuex Vue-router

## 你能学到什么

在这一期的学习进度中，我们会开始学习在我们工作开发中真正要使用的开发模式，vue-cli3 脚手架的运用以及 Vue-router 和状态管理仓 Vuex

## Vue-cli3 搭建前置

**安装 node 环境**

因为 Vue-cli3 也是基于 webpack 搭建的，所以我们还是必须要用到 node，如果你的电脑还没有安装 node 的话，可以到 node 官网自行下载安装，[https://nodejs.org/zh-cn/](https://bbs.gin-vue-admin.com/redirect?url=https%3A%2F%2Fnodejs.org%2Fzh-cn%2F) 推荐大家下载 10.14.0 版本。 安装过后 到命令行执行 node -v 检查版本，如果弹出版本 v10.14.0 的话 恭喜你 安装成功，我们开始进行下面的步骤了。

**全局安装 vue-cli3**
Vue CLI 的包名称由 vue-cli 改成了 @vue/cli。 如果你已经全局安装了旧版本的 vue-cli (1.x 或 2.x)，你需要先通过 npm uninstall vue-cli -g 卸载它。

官方要求安装的 node 版本是大于 8.9 的，我们安装的 node 为 10.14.0，如果你本身的版本低于 8.9 那么可以用 nvm 进行升级

然后我们到命令行执行

```
npm install -g @vue/cli
```

等待运行完毕，我们命令行执行

```
vue --version
```

如果版本在 3.0 以上，恭喜你安装成功了

接下来我们在命令行运行

```
vue create hello-world
```

这时候它会提醒我们来选择需要安装的选项

```
Vue CLI v3.1.3
┌───────────────────────────┐
│  Update available: 3.2.1  │
└───────────────────────────┘
? Please pick a preset: (Use arrow keys)
> my (vue-router, vuex, less, babel, eslint)
  default (babel, eslint)
  Manually select features
```

第一个是我已经保存过的了，我们初次搭建，选择 Manually select features

```
Vue CLI v3.1.3
┌───────────────────────────┐
│  Update available: 3.2.1  │
└───────────────────────────┘
? Please pick a preset: Manually select features
? Check the features needed for your project: (Press <space> to select, <a> to toggle all, <i> to invert selection)
>( ) Babel
 ( ) TypeScript
 ( ) Progressive Web App (PWA) Support
 ( ) Router
 ( ) Vuex
 ( ) CSS Pre-processors
 ( ) Linter / Formatter
 ( ) Unit Testing
 ( ) E2E Testing
```

我们看到如上界面以后，选择 Babel Router Vuex CSS Pre-processors （使用空格键选中） 进行下一步

```
Vue CLI v3.1.3
┌───────────────────────────┐
│  Update available: 3.2.1  │
└───────────────────────────┘
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, CSS Pre-processors
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a CSS pre-processor (PostCSS, Autoprefixer and CSS Modules are supported by default): Sass/SCSS
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In package.json
? Save this as a preset for future projects? Yes
? Save preset as: MyTest
```

接下来大家按照如上配置，基本可以完成项目搭建。等待安装完毕后执行

```
 
cd helloworld
npm run serve
 
```

进入浏览器 打开 localhost:8080 如果看到 Vue 的欢迎界面，恭喜你，我们已经迈出成功第一步了

## ui 图形化配置界面

在 Vue-cli3 中 为大家提供了图形化配置界面 执行

```
 
vue ui 
 
```

会开到提示在 localhost:8000 打开了 UI 配置界面，由于个人感觉图形化配置还是不灵活的，这里就先不给大家做太多演示了，要想做好一个 VUE 项目，需要做太多的搭配，我这里会配置一套基本可以使用的 VUE 框架放到个人 GitHub 供大家使用。

## .vue 文件的基本结构

```html
<template>
  <!-- 这里面写html -->
</template>
 
<script>
// 如果要引入外部的文件 请在 export default外面引入
// 例如  // @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'  //这里引入了 HelloWorld组件

export default {
  name: 'home',
  components: {
    HelloWorld  //使用组件
  }
}
export default {
  name:'vue', //这里面就写VUE组件内的属性即可
}
 
</script>
 
<style>
/* 这里写css 当然你也可以写 lass scss 需要babel支持并且在 style上添加 lang属性 例如   <style lang="scss"> */
</style>
```

## Vue-Router

我们使用 VUE 一般是用来开发单页程序，那么在单页程序内的跳转就要用到路由的形式，由于这里课程是要带大家快速熟悉 VUE 并且使用 VUE，所以原理这里暂且跳过，直接告诉大家使用方法。
我们在开始选择框架要用到的东西的时候已经选择了 Vue-Router，这里我们直接上代码使用即可。我们这里就搭配着 Vue-Cli3 的脚手架来讲解，这里顺便为大家讲解一下 Vue-cli3 脚手架为我们创建的目录结构

│ .gitignore Git 屏蔽提交文件
│ babel.config.js 自定义 babel 的地方
│ package.json 你的所有依赖啥的
│ README.md 不介绍
│ yarn.lock yarn 如果你是 npm 就是 package-lock.json
│
├─public 通过执行 npm run build 产生
│ favicon.ico
│ index.html
│
└─src 主要我们要修改的地方
│ App.vue App 主组件
│ main.js 入口 JS
│ router.js 路由文件
│ store.js vueX store 文件
│
├─assets 静态文件存放处
│ logo.png
│
├─components 我们自己写的组件
│ HelloWorld.vue
│
└─views VUE 页面，这么安排为了方便区分是组件还是用于路由跳转的页面
About.vue
Home.vue

我们直接看到入口文件

```js
// main.js

import Vue from 'vue'
import App from './App.vue'
import router from './router'   //在入口文件引用了router 并且 在newVue的时候 把router挂在到了router属性上
import store from './store'
 
Vue.config.productionTip = false
 
new Vue({
  router,   // 挂载
  store,
  render: h => h(App)
}).$mount('#app')
 
```

接着我们看到 router.js 文件

```js
//router.js

import Vue from 'vue'
import Router from 'vue-router'  //这里引用了vue-router 并且用Vue.use来使用Router
import Home from './views/Home.vue'
 
Vue.use(Router)
 
// Router的内置属性
export default new Router({
  mode: 'history',  //要使用hash模式还是 history模式 我们一般情况下还是用 hash模式 history在后台支持的情况下可以开启
  base: process.env.BASE_URL,  // 应用的基路径。例如，如果整个单页应用服务在 /app/ 下，然后 base 就应该设为 "/app/"
  routes: [              //这就是真正写路由的地方了 
    {
      path: '/',        //  当路由是/的时候 我们匹配哪个组件(这里是Home.vue)
      name: 'home',
      component: Home
    },
    {
      path: '/about',   //  当路由是/about的时候 我们匹配哪个组件(About.vue)
      name: 'about',
      // route level code-splitting
      // this generates a separate chunk (about.[hash].js) for this route
      // which is lazy-loaded when the route is visited.
      component: () => import(/* webpackChunkName: "about" */ './views/About.vue')   //路游懒加载，推荐使用这种模式，不过我们需要改写一下
    }
  ]
})
 
```

稍微做一下懒加载模式的改写

```js
//router.js

import Vue from 'vue'
import Router from 'vue-router'  //这里引用了vue-router 并且用Vue.use来使用Router

Vue.use(Router)
 
const Home = ()=> import('./views/Home.vue')   //使用常量模式 提前声明 并且在component中使用 这样我们就完成了一个可以简单实用的路由了
const About = ()=> import('./views/About.vue')
 
 
// Router的内置属性
export default new Router({
  mode: 'history',  //要使用hash模式还是 history模式 我们一般情况下还是用 hash模式 history在后台支持的情况下可以开启
  base: process.env.BASE_URL,  // 应用的基路径。例如，如果整个单页应用服务在 /app/ 下，然后 base 就应该设为 "/app/"
  routes: [              //这就是真正写路由的地方了 
    {
      path: '/',        //  当路由是/的时候 我们匹配哪个组件(这里是Home.vue)
      name: 'home',
      component: Home
    },
    {
      path: '/about',   //  当路由是/about的时候 我们匹配哪个组件(About.vue)
      name: 'about',
      // route level code-splitting
      // this generates a separate chunk (about.[hash].js) for this route
      // which is lazy-loaded when the route is visited.
      component: About
    }
  ]
})
 
```

那么 这些路由组件怎么展示呢？在哪里展示呢？
这里我们要看到 App.vue 文件

```html
 
<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> 
       <!-- 使用router-link标签 添加 to属性决定我们要路由到什么地方 -->
      <router-link to="/about">About</router-link>
      
    </div>
    <router-view/>    
    <!-- router-view 就是我们要展示路由的地方  -->
  </div>
</template>
 
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
}
 
#nav a {
  font-weight: bold;
  color: #2c3e50;
}
 
#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
```

到这里 我们一个简单的基础的路由已经写好了 可以使用了

## vue-router 的子路由

如何创建子路由呢？其实也是相当简单的

```js
//router.js

import Vue from 'vue'
import Router from 'vue-router'  //这里引用了vue-router 并且用Vue.use来使用Router

Vue.use(Router)
 
const Home = ()=> import('./views/Home.vue')   //使用常量模式 提前声明 并且在component中使用 这样我们就完成了一个可以简单实用的路由了
const About = ()=> import('./views/About.vue')
const Children = ()=> import('./views/Children.vue')  //我们引入这个页面 页面内容

// Router的内置属性
export default new Router({
  // mode: 'history',  
  base: process.env.BASE_URL,  
  routes: [
    {
      path: '/',       
      name: 'home',
      component: Home,
      children:[
        {
          path: 'about',  
          name: 'about',
          component: About,
        },
        {
          path:'children',
          name:'children',
          component:Children
        },  //这时候我们访问 访问about 实际上是home的子路由了
        // 它是home的子路由，所以我們需要在home里面再书写一个路由显示的组件  <router-view/>   子路由的内容就会显示在其 router-view下面
      ]
    }
  ]
})
 
```

这里我把改进后的 app.vue 和 about.vue 等代码贴出。

```html
<!-- app -->
<template>
  <div id="app">
    <router-view/>
  </div>
</template>
 
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
}
 
#nav a {
  font-weight: bold;
  color: #2c3e50;
}
 
#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
 
<!-- about -->
<template>
  <div class="about">
    <h1>This is an about page</h1>
  </div>
</template>
 
<template>
<!-- children -->
  <div class="children">
    <h1>This is an children page</h1>
  </div>
</template>
<!-- home -->
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <div>
      <!-- <router-link to="/">Home</router-link> | -->
      <router-link to="/about">About</router-link> |
      <router-link to="/children">children</router-link>
    </div>
 
      <router-view></router-view>
  </div>
</template>
 
<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
 
export default {
  name: 'home',
  components: {
    HelloWorld
  }
}
</script>
 
```

## vue-router 传参

**编程式的导航 router.push**
编程式的导航传参我们需要使用 this.$router.push() 这个方法 我们有三种传参方式

```html
<script>
// 字符串

// 这种方式可以实现路由跳转 但是没有办法获取到参数 没有办法传参
this.$router.push("children");
// 对象传参
// 特别注意：命名路由这种方式传递的参数，如果在目标页面刷新是会出错的
// 如果想要在刷新后还可以获取到参数就要用到我们讲的动态路由 例如： /children/:userId 这时候 如果使用了这种模式 目标地址栏会变为 /children/qm
this.$router.push({ name: 'children', params: { userId: 'qm' }})
 
// 想要传递参数主要就是以对象的方式来写，分为两种方式：命名路由、查询参数，下面分别说明两种方式的用法和注意事项。

// 查询参数
this.$router.push({ path: '/children', query: { userId: 'qm' }});
// 这种方式参数是暴露在地址栏上面的
</script>
 
 
```

**声明式的导航**
声明式的导航也分为三种方式

```html
<!-- 字符串 -->
  <router-link to="children">click to news page</router-link>
<!-- 命名路由 -->
  <router-link :to="{ name: 'children', params: { userId: 'qm'}}">click to news page</router-link>
<!-- 查询参数 -->
  <router-link :to="{ path: '/children', query: { userId: 'qm'}}">click to news page</router-link>
```

接受参数使用 this.$router 后面就是搭配路由的名称就能获取到参数的值

**url 传参**

这种传参方法我们需要在书写路由的时候做一个小小的改动

```js
 
//router.js

import Vue from 'vue'
import Router from 'vue-router'
 
Vue.use(Router)
 
const Home = ()=> import('./views/Home.vue')   
const About = ()=> import('./views/About.vue')
const Children = ()=> import('./views/Children.vue')
 
// Router的内置属性
export default new Router({
  // mode: 'history',  
  base: process.env.BASE_URL,  
  routes: [
    {
      path: '/',       
      name: 'home',
      component: Home,
      children:[
        {
          path: 'about/:id',  // 我们在后面加入/:id 这样我们在url输入的地址相应就变成了 /about/3  我们在about组件内 通过 this.$route.params  如何动态监听路由的变化呢？要知道当我们仅仅参数变化组件可是没有刷新的，提示一下 watch可是可以监听某些数据哦~实战课程我会带大家了解一下这个该如何运用
          name: 'about',
          component: About,
        },
        {
          path:'children',
          name:'children',
          component:Children
        }, 
      ]
    }
  ]
})
 
```

相应页面改动如下 home.vue about.vue

```html
<!-- about -->
<template>
  <div class="about">
    <h1>This is an about page</h1>
    {{this.$route.params.id}}
  </div>
</template>
 
<!-- home -->
<template>
  <div class="home">
    <img alt="Vue logo" src="../assets/logo.png">
    <div>
      <router-link to="/about/3">About</router-link> |   
      <router-link to="/children">children</router-link>
    </div>
 
      <router-view></router-view>
  </div>
</template>
 
<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
 
export default {
  name: 'home',
  components: {
    HelloWorld
  },
}
</script>
 
```

**404 的制作**

其实 vue-router 里面还有很多细节内容，这里由于我们是基础课程所以不做太详细的介绍，相信大家在浏览网页的时候经常会看到 404 页面，那么用我们的 vue-router 如何去实现一个 404 呢？实际上是非常简单的
我们需要在 router.js 里面书写一个通配路径放置在最后位置，当所有的路径都不匹配的时候，就会去通配这样一个 404 页面提示大家页面丢失了，下面我详细给大家书写一下

```js
// router.js
import Vue from 'vue'
import Router from 'vue-router'
 
Vue.use(Router)
 
const Home = ()=> import('./views/Home.vue')   
const About = ()=> import('./views/About.vue')
const Children = ()=> import('./views/Children.vue')
const NotFound = ()=> import('./views/notFound.vue')
 
// Router的内置属性
export default new Router({
  // mode: 'history',  
  base: process.env.BASE_URL,  
  routes: [
    {
      path: '/',       
      name: 'home',
      component: Home,
      children:[
        {
          path: 'about/:id', 
          name: 'about',
          component: About,
        },
        {
          path:'children',
          name:'children',
          component:Children
        },
      ]
    },
    {
      path: '*',
      name: '404',
      component: NotFound,
    }
  ]
})
 
```

学完这些，你对于 vue-router 的基本运用已经可以算是合格了，工作中的大部分用法也都接触到了，我们接下来讲解 vuex。

## vuex

首先来一个小 demo 展示一下 vuex 的具体用途

```js
// store.js
import Vue from 'vue'
import Vuex from 'vuex'
 
Vue.use(Vuex)
 
export default new Vuex.Store({
  state: {
    number:0,
  },
  mutations: {
    ADD(state){
      state.number++
    },
    SUB(state){
      state.number--
    }
  },
  actions: {
 
  }
})
<!-- children.vue -->
<template>
  <div class="children">
    <h1>This is an children page</h1>
    <button @click="add">
      +
    </button>
      {{$store.state.number}}
      <button  @click="sub">
      -
    </button>
  </div>
</template>
 
<script>
  export default{
    name: 'children',
    methods: {
      add() {
        this.$store.commit('ADD')
      },
      sub() {
        this.$store.commit('SUB')
      }
    }
  }
</script>
```

这样一个小小的 demo 实际上已经诠释了我们 vuex 的作用，我们需要在刷新之前永久保留的状态，并且想要远距离传参并且及时做出响应，那么都可以使用 vuex 来进行。它就是一个状态管理和加工的仓库，一共有五个重要属性，state，mutations，actions , getter , module 这么五个小玩意 我会带大家一个一个认识他们的作用，并且教会大家基本用法，当然了，它也是有较为高级的小用法的。我们实战课也会使用稍微高级的用法来讲解。

## state 属性

我们用 VUE 文件来类比讲解 store
state 就相当于 vue 中的 data 属性，所有的状态或者说是数据都存储在这个 state 里面，我们在别的地方获取需要使用 this.$store.state.属性名称 来获取相应的值，并且我们可以通过 mutations 和 actions 来改变 state 的值，从而触发所有使用到 state 的地方刷新。state 里可以存储各种数据类型，data 里面可以用的数据类型，state 里面同样可以使用。

## getters 属性

getters 我们类比到 vue 中，那么它应该是 computed 了 我们在使用的时候 要使用 this.$store.getters.属性名 用法也和 computed 类似，它实际上是调用一个方法，然后获取到的数据是经过一系列处理后并且 return 回来的数据，它的具体写法是。

```js
// store.js
import Vue from 'vue'
import Vuex from 'vuex'
 
Vue.use(Vuex)
 
export default new Vuex.Store({
  state: {
    number:0,
  },
  mutations: {
    ADD(state){
      state.number++
    },
    SUB(state){
      state.number--
    }
  },
  actions: {
 
  },
  getters:{
    getNumber(state){   //getter的书写方法
      return state.number + 100
    }
  }
})
<!-- children.vue -->
<template>
  <div class="children">
    <h1>This is an children page</h1>
    <button @click="add">
      +
    </button>
      {{$store.state.number}}
      {{$store.getters.getNumber}}
      <!-- getters的基础调用方法，当然还有更高级的 实战课会讲解 -->
      <button  @click="sub">
      -
    </button>
  </div>
</template>
 
<script>
  export default{
    name: 'children',
    methods: {
      add() {
        this.$store.commit('ADD')
      },
      sub() {
        this.$store.commit('SUB')
      }
    }
  }
</script>
```

这就是 getters 的简单实用

## mutations

mutations 类比到 vuex 中应该是 methods
它是更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数：

```js
// store.js
import Vue from 'vue'
import Vuex from 'vuex'
 
Vue.use(Vuex)
 
export default new Vuex.Store({
  state: {
    number:0,
  },
  mutations: {
    ADD(state){  //我们把state传入 就可以对 state内的数据进行我们想要的变化，它必须按照我们想要得到的格式去变化， 想要直接提交mutations 需要使用 this.$stote.commit('mutations的方法名',参数) 如果想要在mutations方法中传参，写法就要变成 ADD(state,形参){ ... } 这样一种形式了 我举个例子
      state.number++
    },
    SUB(state){
      state.number--
    },
    // ADDPARAM(state,param){
    //   if (typeof param !== 'number'){
    //     param = 0
    //   }
    //   state.number = state.number + param
    // }  这个方法就可以传入我们想用的参数了，相应调用的地方也需要改变
  },
  actions: {
 
  },
  getters:{
    getNumber(state){   //getter的书写方法
      return state.number + 100
    }
  }
})
```

带参数的 commit 如何使用

```html
<!-- children.vue -->
<template>
  <div class="children">
    <h1>This is an children page</h1>
    <button @click="add">
      +
    </button>
      {{$store.state.number}}
      {{$store.getters.getNumber}}
      <!-- getters的基础调用方法，当然还有更高级的 实战课会讲解 -->
      <button  @click="sub">
      -
    </button>
  </div>
</template>
 
<script>
  export default{
    name: 'children',
    methods: {
      add() {
        this.$store.commit('ADD')
      },
      // addparam() {
      //   this.$store.commit('ADDPARAM',5)
      // }, 
      //  this.$store.commit('要提交的mutations名字',要传入的参数)
      sub() {
        this.$store.commit('SUB')
      }
    }
  }
</script>
 
```

## actions 属性

actions 属性用法和 mutations 类似，但是 actions 我们是不可以修改 state 的 需要在 actions 通过 commit 来调用 mutations 来修改数据，那么 action 的意义何在呢？处理异步事件就要用 action 来做了呀。调用方法是，this.$store.dispatch("action 的名字"，参数)

书写的方法呢 我给大家展示一下

```js
// store.js
import Vue from 'vue'
import Vuex from 'vuex'
 
Vue.use(Vuex)
 
export default new Vuex.Store({
  state: {
    number:0,
  },
  mutations: {
    ADD(state){ 
      state.number++
    },
    SUB(state){
      state.number--
    },
    ADDPARAM(state,param){
      if (typeof param !== 'number'){
        param = 0
      }
      state.number = state.number + param
    } 
  },
  actions: {
    ASYNCADD(context,param){  //这里我们传入context上下文，里面包含 commit, state ,getters 这三个属性都可以通过context来调用到并且触发内部方法
      setTimeout(function(){
        context.commit('ADDPARAM',param)
      },1000)
    }
  },
  getters:{
    getNumber(state){   //getter的书写方法
      return state.number + 100
    }
  }
})
```

action 的使用

```html
<!-- children.vue -->
<template>
  <div class="children">
    <h1>This is an children page</h1>
    <button @click="add">
      +
    </button>
      {{$store.state.number}}
      {{$store.getters.getNumber}}
      <button  @click="sub">
      -
    </button>
    
 
    <button @click="actAdd">action</button>
  </div>
</template>
 
<script>
  export default{
    name: 'children',
    methods: {
      add() {
        this.$store.commit('ADD')
      },
      actAdd(){   // 我们通过 dispatch来调用action 然后由action去提交我们的 mutations来达到异步更改状态的目的
        this.$store.dispatch('ASYNCADD',50)
      },
      sub() {
        this.$store.commit('SUB')
      }
    }
  }
</script>
 
```

vuex 是不是非常简单呢，到这里大家可能在想，如果我的项目很大 我有很多的这些个方法啊，状态啊，我都写在这里不就乱了吗？没错，肯定乱了，所以 vuex 还提供了 modules 方便我们分块管理

## modules

由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。
为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割

我们把上面的这些个代码分割出去

```js
// store.js
import Vue from 'vue'
import Vuex from 'vuex'
 
Vue.use(Vuex)
 
const children = {   // 因为我们是写在了children这个组件里面 这个命名规则也好让我们知道它存在于哪里
  state: {
    number:0,
  },
  mutations: {
    ADD(state){  
      state.number++
    },
    SUB(state){
      state.number--
    },
    ADDPARAM(state,param){
      if (typeof param !== 'number'){
        param = 0
      }
      state.number = state.number + param
    }  
  },
  actions: {
    ASYNCADD(context,param){  
      setTimeout(function(){
        context.commit('ADDPARAM',param)
      },1000)
    }
  },
  getters:{
    getNumber(state){   
      return state.number + 100
    }
  }
}
 
 
export default new Vuex.Store({
  modules:{
    children, // 这里我们把children传入以后 还是一样可以使用我们的哪些方法，不过我们是state要加上 modules名字 所以我们的children.vue 要相应修改
  }
})
<!-- children.vue -->
<template>
  <div class="children">
    <h1>This is an children page</h1>
    <button @click="add">
      +
    </button>
      {{$store.state.children.number}}
      <!-- 大家可以看到，这里需要加上modules名字才可以获取到number 但是其余的不需要处理，所以我们开发中一般想要获取到 state 最好是通过getter来获取  -->
      {{$store.getters.getNumber}}
      <button  @click="sub">
      -
    </button>
    
 
    <button @click="actAdd">action</button>
  </div>
</template>
 
<script>
  export default{
    name: 'children',
    methods: {
      add() {
        this.$store.commit('ADD')
      },
      actAdd(){   // 我们通过 dispatch来调用action 然后由action去提交我们的 mutations来达到异步更改状态的目的
        this.$store.dispatch('ASYNCADD',50)
      },
      sub() {
        this.$store.commit('SUB')
      }
    },
  }
</script>
 
```

很多时候，我们在正式使用中都会加上命名空间，也就是 modules 里面的 namespaced 属性我们让它变为 namespaced: true, 如果大家想要学习，可以去 vuex 文档学习，得益于 es6 vuex 给我们提供了
mapState, mapGetters, mapActions 和 mapMutations 这四巨头 我们可以轻松使用命名空间 这里不做过多讲解，实战课程中我们边用边说。
