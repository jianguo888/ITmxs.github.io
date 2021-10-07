---
title: "Gitbook使用1"
date: 2021-10-07T09:05:31+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

> 本文主要介绍http://book.levy.net.cn/doc/frontend/uiframe/module_split.html

gitbook文档中文版

https://chrisniael.gitbooks.io/gitbook-documentation/content/platform/domains.html

# 什么是Gitbook

GitBook) 是一个使用 Git 和 Markdown 来构建书籍的工具。它可以将你的书输出很多格式：PDF，ePub，mobi，或者输出为静态网页。

GitBook工具链是开源并且完全免费的，

所有在 **Gitbook.com** 上的书的http地址为 `http://{author}.gitbooks.io/{book}/`，而书内容的地址是 `http://{author}.gitbooks.io/{book}/content/`。

但是你也可以使用你自定义的域名（GitBook的免费功能）。域名可以绑定到你的主页或者内容上（或两者都）。

很容易就可以添加一个自定义域名。

## 域名提供商设置

为了完成域名绑定，你需在你的域名提供商那边做一些设置：

1. 登陆你的域名注册商的网站，找到允许你 添加/编辑 主机记录的页面，通常这个页面会在 `编辑 DNS`，`主机记录` 或者 `域文件控制` 的设置里。
2. 设置一个值为 `www` 的 **CNAME** 记录，URL域指向： `www.gitbooks.io`。
3. 为了 **重定向** 顶级域名（`yourdomain.com`）到 `www.yourdomain.com` 上，你需要开启 *“域名转发”*。这个功能通常在 `转发`，`URL 转发` 或者 `URL 重定向` 中（大陆域名提供商已于2009年12月29日起禁用域名转发功能）。

DNS 解析的转播可能需要花上几个小时的时间。

# 安装Gitbook

1. 安装 [Node.js](https://nodejs.org/)

2. 通过 npm 来安装 gitbook

   ```
   npm install gitbook-cli -g
   
   yarn global add gitbook-cli
   ```

   安装完成后，输入 `gitbook --version`来查看是否安装成功 **第一次使用，cli 会自动安装`gitbook`,安装时间视网络环境而定，请耐心等待**

   若要卸载，就执行 `npm uninstall gitbook-cli -g`来删除

   由于我这里已经安装了，安装完成可用以下命令查看。

   ![img](https://luckly007.oss-cn-beijing.aliyuncs.com/img/1750888-20200827093429403-214093467.png)

   # npm通过gitbook-cli安装gitbook出现问题，一直安装Gitbook 3.2.3报错

   问题原因
   大概意思是：gitbook-cli引用了旧版的graceful-fs，导致出现问题

   解决方式
   需要更新graceful-fs库

   执行如下命令

   ```
   # 进入gitbook-cli全局安装目录的node依赖文件夹
   
   F:\environment\nodejs\node_modules\gitbook-cli\node_modules\npm\node_modules
   
   # 更新graceful-fs库
   
   npm install graceful-fs@latest --save
   
   ```

   再次执行，gitbook -V即可 ，需要耐心等待一会。

   之后再次查看，OK。

nodejs历史版本下载：https://nodejs.org/dist/

![image-20211007094646266](https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20211007094646266.png)

我选择的是node-v12.22.3-x64.msi：

https://nodejs.org/dist/latest-v12.x/node-v12.22.3-x64.msi

或者：

降级nodejs版本为：[node-v9.11.2-x64.msi](https://nodejs.org/dist/latest-v9.x/node-v9.11.2-x64.msi)

重新安装nodejs。

重试：


### 基本使用

```
gitbook init
```

 

初始化书籍目录

> `README.md` 和 `SUMMARY.md` 是两个必须文件, `README.md` 是对书籍的简单介绍, `SUMMARY.md` 是书籍的目录结构

```
gitbook serve
```

 serve 命令也可以指定端口：



```undefined
gitbook serve --port 2333
```

编译和预览书籍

> 注: `gitbook serve` 命令实际上会首先调用 `gitbook build` 编译书籍，完成以后会打开一个 web 服务器，监听在本地的 4000 端口



6、生成静态网页
 执行 gitbook build 命令构建书籍，默认将生成的静态网站输出到 _book 目录。实际上，这一步也包含在 gitbook serve 里面，因为它们是 HTML，所以 gitbook 通过 Node.js 提供服务了。



```bash
gitbook build #生成静态网页
```

可以生成 PDF 格式的电子书：



```undefined
gitbook pdf ./ ./mybook.pdf
```

生成 epub 格式的电子书：



```undefined
gitbook epub ./ ./mybook.epub
```

生成 mobi 格式的电子书：



```undefined
gitbook mobi ./ ./mybook.mobi
```

如果生成不了，还需要安装工具[ebook-convert](https://links.jianshu.com/go?to=https%3A%2F%2Fcalibre-ebook.com)，安装好后，还需要执行以下命令



```bash
ln -s /Applications/calibre.app/Contents/MacOS/ebook-convert /usr/local/bin
```

编辑 SUMMARY.md 文件，内容修改为：



book.json，该文件用于存放配置信息。

【title】书本的标题
 【author】作者的相关信息
 【description】本书的简单描述
 【language】gitbook使用的语言
 【root】指定存放 GitBook 文件（除了 book.json）的根目录
 【structure】指定自述文件，摘要，词汇表等的路径

gitbook支持许多插件，可以扩展gitbook的功能。

【打赏功能：donate】



```json
{
    "plugins": ["donate"],
    "pluginsConfig": {
        "donate": {
          "wechat": "例：/images/qr.png",
          "alipay": "http://blog.willin.wang/static/images/qr.png",
          "title": "默认空",
          "button": "默认值：Donate",
          "alipayText": "默认值：支付宝捐赠",
          "wechatText": "默认值：微信捐赠"
        }
    }
}
```

【广告功能：ad】



```json
{
  "plugins": ["ad"],
  "pluginsConfig": {
    "ad": {
      "contentTop": "<div>Ads at the top of the page</div>",
      "contentBottom": "%3Cdiv%3EAds%20at%20the%20bottom%20of%20the%20page%3C/div%3E"
    }
  }
}

// note: contentBottom is escape('<div>Ads at the bottom of the page</div>')
```

【目录宽度可调节：splitter】



```json
{
    "plugins": ["splitter"]
}
```

【github图标】



```json
{
    "plugins": [ "github" ],
    "pluginsConfig": {
        "github": {
            "url": "https://github.com/your/repo"
        }
    }
}
```

【自定义页脚：tbfed-pagefooter】



```json
{
    "plugins": [ "tbfed-pagefooter" ],
    "pluginsConfig": {
        "tbfed-pagefooter": {
             "copyright":"&copy Taobao FED Team",
             "modify_label": "该文件修订时间：",
             "modify_format": "YYYY-MM-DD HH:mm:ss"
        }
    }
}
```

【目录章节可折叠：expandable-chapters】



```bash
{
    {
        plugins: ["expandable-chapters"]
    }
    {
        "pluginsConfig": {
            "expandable-chapters":{}
        }
    }
}
```

【畅言评论：changyan】



```json
{
    "plugins": [
        "changyan"
    ],
    "pluginsConfig": {
        "changyan": {
            "appid": "your changyan's appid",
            "conf": "the conf in the code generate by changyan"
        }
    }
}
```

【返回顶部：back-to-top-button】



```json
{
    "plugins" : [ "back-to-top-button" ]
}
```

  上面支持列举了一些常用的插件，想要了解更多可以阅读官方文档，插件在book.json配置好后，需要安装。

book.json模板



```json
{
  "title": "UI",
  "description": "UI组件库",
  "author": "zhuyongbo",
  "language": "zh-hans",
  "links": {
    "sidebar": {
      "开放平台": "http://e.cnpc.com.cn/opensdk/"
    }
  },
  "styles":{
    "website":"style.css"
  },
  "plugins": [
    "-lunr",
    "-search",
    "-livereload",
    "-sharing",
    "expandable-chapters",
    "search-plus",
    "splitter",
    "github",
    "-sharing",
    "emphasize",
    "include-codeblock",
    "tbfed-pagefooter",
    "back-to-top-button",
    "anchor-navigation-ex"
  ],
  "pluginsConfig": {
    "github": {
      "url": "https://github.com/webzhuyongbo"
    },
    "sharing": {
      "douban": false,
      "facebook": false,
      "google": false,
      "hatenaBookmark": false,
      "instapaper": false,
      "line": false,
      "linkedin": false,
      "messenger": false,
      "pocket": false,
      "qq": false,
      "qzone": false,
      "stumbleupon": false,
      "twitter": false,
      "viber": false,
      "vk": false,
      "weibo": false,
      "whatsapp": false,
      "all": [
        "weibo","qq","qzone","google","douban"
      ]
    },
    "anchor-navigation-ex": {
      "associatedWithSummary":false,
      "showLevel":true,
      "multipleH1": true,
      "mode": "float",

      "pageTop": {
        "showLevelIcon": false,
        "level1Icon": "fa fa-hand-o-right",
        "level2Icon": "fa fa-hand-o-right",
        "level3Icon": "fa fa-hand-o-right"
      }
    },
    "tbfed-pagefooter": {
      "copyright": "&copy;北京信息技术有限责任公司",
      "modify_label": "文档更新时间：",
      "modify_format": "YYYY-MM-DD HH:mm:ss"
    }
  }
}
```

**去掉gitbook的版权信息**：

创建样式表文件“styles/website.css”，添加代码如下：



```css
.gitbook-link {
    display: none !important;
}
```

编辑“book.json”文件，添加如下代码：



```json
{
    "styles": {
        "website": "styles/website.css"
    }
}
```

好了，让我们看一下我们文档的效果图。





### 目录结构

# 简单书写



# 插件

gitbook 还支持许多插件，用户可以从 [NPM](https://www.npmjs.com/) 上搜索 gitbook 相关的插件

gitbook 的插件信息会保存在 `book.json` 文件里

以下是我个人常用的插件:
gitbook-plugin-donate(打赏按钮): https://www.npmjs.com/package/gitbook-plugin-donate

gitbook-plugin-github-buttons(GitHub按钮): https://www.npmjs.com/package/gitbook-plugin-github-buttons

gitbook-plugin-edit-link(GitHub编辑按钮): https://www.npmjs.com/package/gitbook-plugin-edit-link

```yaml
{
    "title": "编写gitbook电子书教程",
    "description": "gitbook电子书教程",
    "author": "sphard",
    "language": "zh-hans",
    "root": ".",

    "plugins": [
        "donate",
        "github-buttons@2.1.0",
        "edit-link"
    ],

    "pluginsConfig": {
        "donate": {
            "wechat": "https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210922202901895.png",
            "alipay": "https://luckly007.oss-cn-beijing.aliyuncs.com/img/image-20210922202843746.png",
            "title": "",
            "button": "打赏",
            "alipayText": "支付宝",
            "wechatText": "微信"
        },
        "github-buttons": {
            "repo": "ITmxs/gitbook-Information-System-Engineer",
            "types": [
                "star"
            ],
            "size": "small"
        },
        "edit-link": {
            "base": "https://github.com/ITmxs/gitbook-Information-System-Engineer/edit/master",
            "label": "Edit This Page"
        }
    }
}
```

参考本人完整配置详情：

```js
{
    "author": "Levy",
    "description": "This is record sharing by Levy.",
    "extension": null,
    "generator": "site",
    "isbn": "",
    "links": {
        "sharing": {
            "all": null,
            "facebook": null,
            "google": null,
            "twitter": null,
            "weibo": null
        },
        "sidebar": {
            "谷歌": "https://www.google.com"
        }
    },
    "output": null,
    "pdf": {
        "fontSize": 12,
        "footerTemplate": null,
        "headerTemplate": null,
        "margin": {
            "bottom": 36,
            "left": 62,
            "right": 62,
            "top": 36
        },
        "pageNumbers": true,
        "paperSize": "a4"
    },
    "plugins": ["page-treeview",
                "code",
                "pageview-count",
                "popup",
                "tbfed-pagefooter",
                "favicon",
                
    "search-plus", 
               
                "expandable-chapters",
                "hide-element", "back-to-top-button",
                "splitter",
    "-lunr", "-search", "-sharing", "sharing-plus", "chapter-fold"],
    "pluginsConfig": {
        "hide-element": {
            "elements": [".gitbook-link"]
        },
        "tbfed-pagefooter": {
            "copyright": "Copyright © levywang123@gmail.com 2020",
            "modify_label": "该文章修订时间：",
            "modify_format": "YYYY-MM-DD HH:mm:ss"
        },
        "favicon": {
            "shortcut": "assets/favicon.ico",
            "bookmark": "assets/favicon.ico",
            "appleTouch": "assets/favicon.ico",
            "appleTouchMore": {
                "120x120": "assets/favicon.ico",
                "180x180": "assets/favicon.ico"
            }
        },
        "fontsettings": {
            "theme": "white",
            "family": "sans",
            "size": 2
        },
        "page-treeview": {
            "copyright": "Copyright © levywang123@gmail.com 2020",
            "minHeaderCount": "2",
            "minHeaderDeep": "2"
        },
        "sharing": {
            "all": ["facebook", "google", "linkedin", "twitter", "weibo", "qq"]
        }
    },
    "language": "zh-hans",
    "title": "Levy",
    "variables": {},
    "styles": {
        "website": "assets/styles/website.css"
    }
}
```

# 插件说明

## page-treeview 目录

[GitHub 地址](https://github.com/aleen42/gitbook-treeview)

不需要在文档中插入标签，能够支持到 6 级目录，安装即可用。
这个插件生成目录以后，下面有一行关于版权的文字。
这行文字可以通过样式来进行控制，让它不显示出来。

```css
.treeview__container {
    margin-bottom: 0px !important;
}
.treeview__container-title {
    display: none !important;
}
```

## code 代码

[GitHub 地址](https://github.com/TGhoul/gitbook-plugin-code)

为代码块添加行号和复制按钮，复制按钮可关闭
单行代码无行号。

```js
"code": {
        "copyButtons": false
      }
```

## pageview-count 阅读量计数

该插件用来统计当前页面被访问次数

## popup 图片点击查看

[GitHub 地址](https://github.com/somax/gitbook-plugin-popup)

插件用于点击图片时，打开新的网页用来查看高清大图。

## tbfed-pagefooter 页面添加页脚（简单版）

[GitHub 地址](https://github.com/zhj3618/gitbook-plugin-tbfed-pagefooter)

在每个页面的最下方自动添加页脚信息，配置如下：

```js
"tbfed-pagefooter": {
            "copyright": "Copyright © levywang123@gmail.com 2020",
            "modify_label": "该文章修订时间：",
            "modify_format": "YYYY-MM-DD HH:mm:ss"
        },
```

## page-copyright 页面添加页脚（复杂版）

[GitHub 地址](https://github.com/skyFi/gitbook-plugin-page-footer)

在每个页面的最下方自动添加页脚配置的各个信息，配置如下：

```js
 "page-copyright": {
          "description": "footer",
          "copyright": "Copyright © levywang123@gmail.com 2020",
          "timeColor": "#ccc",
          "copyrightColor": "#ddd",
          "utcOffset": "8",
          "style": "normal",
          "noPowered": false,
          "signature": "levy",
          "wisdom": "footer",
          "format": "YYYY-MM-dd hh:mm:ss",
        },
```

## favicon 修改图标

修改网页标题的图标，显示个性化 ico

```js
        "favicon": {
            "shortcut": "assets/favicon.ico",
            "bookmark": "assets/favicon.ico",
            "appleTouch": "assets/favicon.ico",
            "appleTouchMore": {
                "120x120": "assets/favicon.ico",
                "180x180": "assets/favicon.ico"
            }
        },
```

## search-plus 替换原搜索插件

原搜索插件不支持中文搜索，所以使用该插件进行替换。需要将原插件进行去除掉。

```js
    "plugins": [ "search-plus", "-lunr", "-search"]
```

## expandable-chapters 及 chapter-fold 导航目录

两个插件配合使用，使导航目录使用更正常，以免出现导航栏问题。

一个支持多层目录，一个是在目录前方加上箭头。使点击两个都有效。

## hide-element 隐藏界面元素

[GitHub 地址](https://github.com/gonjay/gitbook-plugin-hide-element)

可以隐藏不想看到的元素，比如导航栏中 下方的 `Published by GitBook`

```js
"elements": [".gitbook-link"]
```

## back-to-top-button 返回顶部

[GitHub 地址](https://github.com/stuebersystems/gitbook-plugin-back-to-top-button)

在页面篇幅过长时，在界面右下角自动添加上返回顶部的按钮。

## splitter 侧边栏宽度调整

[GitHub 地址](https://github.com/yoshidax/gitbook-plugin-splitter)

添加完插件后，在界面上 侧边栏可自行调整宽度。

## sharing-plus 分享插件

[插件地址](https://plugins.gitbook.com/plugin/sharing-plus)

需要将自带的插件给隐藏掉 `-sharing`
分享当前页面，比默认的 sharing 插件多了一些分享方式。

```js
"sharing": {
             "douban": false,
             "facebook": false,
             "google": false,
             "pocket": false,
             "qq": false,
             "qzone": false,
             "twitter": false,
             "weibo": false,
          "all": [
              "qq", "qzone","viber","whatsapp",
               "douban", "facebook", "google", "instapaper", "linkedin",
               "messenger","twitter", "weibo"
           ]
       }
```

## donate 打赏插件

配置打赏模块，在每篇文章底部都会加上一个按钮，点击显示图片

```js
"donate": {
            "wechat": "/assets/img/donate/wechat.png",
            "alipay": "/assets/img/donate/alipay.png",
            "title": "",
            "button": "赏",
            "alipayText": "支付宝打赏",
            "wechatText": "微信打赏"
        }
```



# 将Gitbook电子书部署到GitHub上



master 分支保存书籍的源码

gh-pages 分支保存书籍编译后的 HTML 文件

```
git checkout -b gh-pages 
```



# 将Gitbook电子书部署到Coding上

master 分支保存书籍的源码

coding-pages 分支保存书籍编译后的 HTML 文件



# 购买域名

## 将域名绑定到GitHub

## 将域名绑定到Codinggitbook 运行 bug 报错

下载较新版本的 node.js 会导致 gitbook 运行报错，如果你也遇到了下面的报错，可以参考一下本章节的教程:

# 修改 gitbook 内容字体大小

自定义网页样式，默认情况下各 generator 对应的 css 文件

```
"styles": {
    "website": "styles/website.css",
    "ebook": "styles/ebook.css",
    "pdf": "styles/pdf.css",
    "mobi": "styles/mobi.css",
    "epub": "styles/epub.css"
}
```

# 结语

这本书会持续更新，尽量让它与时俱进！

最后，也希望有更多的人能看到这本书，如果你觉得这本书写的还可以，欢迎分享给你身边的朋友们。