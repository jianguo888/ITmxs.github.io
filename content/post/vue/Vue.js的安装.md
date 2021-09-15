---
title: "Vue.js 安装"
date: 2021-09-15T14:50:35+08:00
draft: false
tags: ["Vue"]
categories: ["Vue"]
---

# Vue.js 安装

## 什么是 VUE

VUE 是一套用于构建用户界面的渐进式框架，VUE 并不是一个真正意义上的 mvvm 框架，它更倾向是一种数据驱动框架。所以我们在学习 VUE 之前，无论你是传统 JS 开发者，还是后端开发人员，都需要把思维进行一次转化，在 VUE 里，数据就是一切，你所看见的所有东西，都是数据。

## VUE 基础入门

```
在我们日常的开发中,我们经常会使用VUE-CLI脚手架来搭建VUE项目,但是如果我们学习VUE,最好不要直接上手脚手架,这样对于你在VUE的进阶上会增加难度,我们现在采用传统的引入js的方法来开始VUE的讲解
```

### 开始 HelloWord

国际惯例，在学习一个新的东西之前，一定要先开始一个 HelloWord，这里我们先从官网引入 vue 的 js 文件。

```html
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

这两个 JS 文件对应了我们的不同环境，学习过程中我们将选择上面的 JS 文件来进行学习。

## 1、独立版本

我们可以在 Vue.js 的官网上直接下载 vue.min.js 并用 **<script>** 标签引入。

[下载 Vue.js](https://vuejs.org/js/vue.min.js)

------

## 2、使用 CDN 方法

以下推荐国外比较稳定的两个 CDN，国内还没发现哪一家比较好，目前还是建议下载到本地。

- **Staticfile CDN（国内）** : https://cdn.staticfile.org/vue/2.2.2/vue.min.js
- **unpkg**：https://unpkg.com/vue/dist/vue.js, 会保持和 npm 发布的最新的版本一致。
- **cdnjs** : https://cdnjs.cloudflare.com/ajax/libs/vue/2.1.8/vue.min.js

------

## 3、NPM 方法

由于 npm 安装速度慢，本教程使用了淘宝的镜像及其命令 cnpm，安装使用介绍参照：[使用淘宝 NPM 镜像]()。



## 使用淘宝 NPM 镜像

大家都知道国内直接使用 npm 的官方镜像是非常慢的，这里推荐使用淘宝 NPM 镜像。

淘宝 NPM 镜像是一个完整 npmjs.org 镜像，你可以用此代替官方版本(只读)，同步频率目前为 10分钟 一次以保证尽量与官方服务同步。

你可以使用淘宝定制的 cnpm (gzip 压缩支持) 命令行工具代替默认的 npm:

```
$ npm install -g cnpm --registry=https://registry.npm.taobao.org
```

这样就可以使用 cnpm 命令来安装模块了：

```
$ cnpm install [name]
```

更多信息可以查阅：http://npm.taobao.org/。

npm 版本需要大于 3.0，如果低于此版本需要升级它：

```
# 查看版本
$ npm -v
6.14.14

#升级 npm
cnpm install npm -g


# 升级或安装 cnpm
npm install cnpm -g
```

在用 Vue.js 构建大型应用时推荐使用 cnpm 安装：

```
# 最新稳定版
$ cnpm install vue
```

------

## 命令行工具

Vue.js 提供一个官方命令行工具，可用于快速搭建大型单页应用。

```
# 全局安装 vue-cli
$ cnpm install --global vue-cli
# 创建一个基于 webpack 模板的新项目
$ vue init webpack my-project
# 这里需要进行一些配置，默认回车即可
This will install Vue 2.x version of the template.

For Vue 1.x use: vue init webpack#1.0 my-project

? Project name my-project
? Project description A Vue.js project
? Author runoob <test@runoob.com>
? Vue build standalone
? Use ESLint to lint your code? Yes
? Pick an ESLint preset Standard
? Setup unit tests with Karma + Mocha? Yes
? Setup e2e tests with Nightwatch? Yes

   vue-cli · Generated "my-project".

   To get started:
   
     cd my-project
     npm install
     npm run dev
   
   Documentation can be found at https://vuejs-templates.github.io/webpack
```

我准备用mpvue开发小程序，结果初始一个新项目一直不成功，大神帮我看一下咋回事？
首先我用的命令是

```csharp
      vue init mpvue/mpvue-quickstart .
      
```

然后提示我：

```csharp
     Command vue init requires a global addon to be installed.
      Please run yarn global add @vue/cli-init and try again.
```

然后我就按照要求全局安装了@vue/cli-init，也成功了



进入项目，安装并运行：

```
$ cd my-project
$ cnpm install
$ cnpm run dev
 DONE  Compiled successfully in 4388ms

> Listening at http://localhost:8080
```

成功执行以上命令后访问 http://localhost:8080/，输出结果如下所示：

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/56219E04-D156-43EC-AC59-BFE7E38A62C3.jpg)

> **注意：**Vue.js 不支持 IE8 及其以下 IE 版本。

------

## Vue 项目打包

打包 Vue 项目使用以下命令：

```
npm run build
```

执行完成后，会在 Vue 项目下生成一个 **dist** 目录，一般包含 index.html 文件及 static 目录，static 目录包含了静态文件 js、css 以及图片目录 images。

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/BEE1DA18-407F-4979-9DFD-D61FB77E2671.jpg)

如果直接双击 index.html 打开浏览器，页面可能是空白了，想要修改下 index.html 文件中 js、css 路径即可。

例如我们打开 dist/index.html 文件看到路径是绝对路径：

```
<link href=/static/css/app.33da80d69744798940b135da93bc7b98.css rel=stylesheet>
<script type=text/javascript src=/static/js/app.717bb358ddc19e181140.js></script>
```

我们把 js、css 路径路径修改为相对路径：

```
<link href=static/css/app.33da80d69744798940b135da93bc7b98.css rel=stylesheet>
<script type=text/javascript src=static/js/app.717bb358ddc19e181140.js></script>
```

这样直接双击 dist/index.html 文件就可以在浏览器中看到效果了。
