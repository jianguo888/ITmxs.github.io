---
title: "Vue.js 目录结构"
date: 2021-09-15T15:10:28+08:00
draft: false
tags: ["Vue"]
categories: ["Vue"]
---

# Vue.js 目录结构

上一章节中我们使用了 npm 安装项目，我们在 IDE（Eclipse、Atom等） 中打开该目录，结构如下所示：

vscode开发时插件安装

1。Vetur —— 语法高亮、智能感知、Emmet等

2。EsLint —— 语法纠错

3。Path Intellisense —— 自动路径补全

4。Beautify——格式化代码，值得注意的是，beautify插件支持自定义格式化代码规则

5。Bracket Pair Colorizer——给括号加上不同的颜色，便于区分不同的区块，使用者可以定义不同括号类型和不同颜色

6。open in browser——直接右键项目单击启动



![img](https://luckly007.oss-cn-beijing.aliyuncs.com/image/B6E593E3-F284-4C58-A610-94C6ACDAD485.jpg)

### 目录解析

| 目录/文件    | 说明                                                         |
| :----------- | :----------------------------------------------------------- |
| build        | 项目构建(webpack)相关代码                                    |
| config       | 配置目录，包括端口号等。我们初学可以使用默认的。             |
| node_modules | npm 加载的项目依赖模块                                       |
| src          | 这里是我们要开发的目录，基本上要做的事情都在这个目录里。里面包含了几个目录及文件：assets: 放置一些图片，如logo等。components: 目录里面放了一个组件文件，可以不用。App.vue: 项目入口文件，我们也可以直接将组件写这里，而不使用 components 目录。main.js: 项目的核心文件。 |
| static       | 静态资源目录，如图片、字体等。                               |
| test         | 初始测试目录，可删除                                         |
| .xxxx文件    | 这些是一些配置文件，包括语法配置，git配置等。                |
| index.html   | 首页入口文件，你可以添加一些 meta 信息或统计代码啥的。       |
| package.json | 项目配置文件。                                               |
| README.md    | 项目的说明文档，markdown 格式                                |

在前面我们打开 APP.vue 文件，代码如下（解释在注释中）：

# vscode 保存修复vue文件的eslint语法错误

https://blog.csdn.net/SiShen654/article/details/109704530

```toml
{
    "workbench.colorTheme": "Default Dark+",
    "[dart]": {
        "editor.formatOnSave": true,
        "editor.formatOnType": true,
        "editor.rulers": [
            80
        ],
        "editor.selectionHighlight": false,
        "editor.suggest.snippetsPreventQuickSuggestions": false,
        "editor.suggestSelection": "first",
        "editor.tabCompletion": "onlySnippets",
        "editor.wordBasedSuggestions": false
    },
    "editor.formatOnSave": true,
    "workbench.startupEditor": "none",
    "security.workspace.trust.untrustedFiles": "open",
    "editor.suggestSelection": "first",
    "vsintellicode.modify.editor.suggestSelection": "automaticallyOverrodeDefaultValue",
    "git.autofetch": true,
    "go.alternateTools": {},
    "go.formatTool": "gofmt",
    "workbench.editorAssociations": {
        "*.ipynb": "jupyter-notebook"
    },
    "notebook.cellToolbarLocation": {
        "default": "right",
        "jupyter-notebook": "left"
    },
    "git.enableSmartCommit": true,
    "eslint.validate": [],
    "eslint.workingDirectories": [],
    "eslint.trace.server": null,
    "[vue]": {
        "editor.defaultFormatter": "octref.vetur"
    },
    // 让vue中的js按编辑器自带的ts格式进行格式化 
    "vetur.format.defaultFormatter.js": "vscode-typescript",
    // 让函数(名)和后面的括号之间加个空格
    "javascript.format.insertSpaceBeforeFunctionParenthesis": true,
    //vue的模板文件中的 html 使用自带的 js-beautify-html 进行格式化
    "vetur.format.defaultFormatter.html": "js-beautify-html",
    // vue tab 大小为2个空格
    "vetur.format.options.tabSize": 2,
    // vutur 覆盖默认设置
    "vetur.format.defaultFormatterOptions": {
        // 让html的attributes不换行，看起来会更美观
        "js-beautify-html": {
            "wrap_line_length": 240,
            "wrap_attributes": "auto",
            "end_with_newline": false
        }
    }
}
```

app.vue

```vue
<!-- 展示模板 -->
<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view />
  </div>
</template>
// 导入组件
<script>
export default {
  name: 'App'
}
</script>
<!-- 样式代码 -->
<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>

```

helloword.vue

```vue
<template>

  <div class="hello">
    <h1>{{ msg }}</h1>
  </div>
</template>

<script>
export default {
  name: 'HelloWorld',
  data () {
    return {
      msg: '欢迎来到德莱联盟'
    }
  }
}
</script>

```

