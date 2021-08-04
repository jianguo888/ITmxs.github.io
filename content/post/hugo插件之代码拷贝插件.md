---
title: "Hugo插件之代码拷贝插件"
date: 2021-08-04T20:15:53+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

# hugo插件之代码块拷贝插件

很多网站都有代码块拷贝插件，但是查阅了很多的文档，找到hugo主题下有2个带代码拷贝的主题[hugo-theme-learn](https://themes.gohugo.io/hugo-theme-learn/)和[bulma](https://themes.gohugo.io/bulma)，页面布局个人不是很喜欢，改动起来难度很大，还查了一些国外的网站，最后从[Adding click-to-copy buttons to a Hugo powered blog](https://tomspencer.dev/blog/2018/09/14/adding-click-to-copy-buttons-to-a-hugo-powered-blog/)文章中找到的代码，特分享给大家。

## 添加按钮

我们通过检查博客内容页面发现，`hugo`生成的每个代码块格式如下：

没有语法突出显示的代码块具有相同的结构，但周围没有 `<div class=highlight>` 。为了考虑到这两种情况，我选择了作为`<pre>`元素下面的`<code>`子元素。

`copy-to-clipboard.css`：

```css
 1.highlight {
 2    position: relative;
 3}
 4
 5.highlight pre {
 6    padding-right: 75px;
 7    background-color:#f8f8f8 !important;
 8}
 9
10.highlight-copy-btn {
11    position: absolute;
12    bottom: 7px;
13    right: 7px;
14    border: 0;
15    border-radius: 4px;
16    padding: 1px;
17    font-size: 0.8em;
18    line-height: 1.8;
19    color: #fff;
20    background-color: #777;
21    min-width: 55px;
22    text-align: center;
23}
24
25.highlight-copy-btn:hover {
26    background-color: #666;
27}
```

Copy

## 与剪切板交互

按钮有了，接下来就是怎么使用JavaScript拷贝代码到剪切板，废话少说，直接上代码：

```
copy-to-clipboard.js
 1(function() {
 2  'use strict';
 3
 4  if(!document.queryCommandSupported('copy')) {
 5    return;
 6  }
 7
 8  function flashCopyMessage(el, msg) {
 9    el.textContent = msg;
10    setTimeout(function() {
11      el.textContent = "Copy";
12    }, 1000);
13  }
14
15  function selectText(node) {
16    var selection = window.getSelection();
17    var range = document.createRange();
18    range.selectNodeContents(node);
19    selection.removeAllRanges();
20    selection.addRange(range);
21    return selection;
22  }
23
24  function addCopyButton(containerEl) {
25    var copyBtn = document.createElement("button");
26    copyBtn.className = "highlight-copy-btn";
27    copyBtn.textContent = "Copy";
28
29    var codeEl = containerEl.firstElementChild;
30    copyBtn.addEventListener('click', function() {
31      try {
32        var selection = selectText(codeEl);
33        document.execCommand('copy');
34        selection.removeAllRanges();
35
36        flashCopyMessage(copyBtn, 'Copied!')
37      } catch(e) {
38        console && console.log(e);
39        flashCopyMessage(copyBtn, 'Failed :\'(')
40      }
41    });
42
43    containerEl.appendChild(copyBtn);
44  }
45
46  // Add copy button to code blocks
47  var highlightBlocks = document.getElementsByClassName('highlight');
48  Array.prototype.forEach.call(highlightBlocks, addCopyButton);
49})();
```

Copy

## 修改主题

将`copy-to-clipboard.css`和`copy-to-clipboard.js`分别放到`static/css`和`static/js`目录下

然后在页面引用这两个文件

1.修改`config.toml`添加自定义css

```bash
1custom_css = ["/css/copy-to-clipboard.css"]
```

Copy

2.修改主题文件下的`layouts/partials/footer.html`

在结尾加上下面这一句，之前测试在`config.toml`下面添加，不生效

```html
1<script src="/js/copy-to-clipboard.js"></script>
```

Copy

## 运行hugo

最终的效果如下：

![copy-to-clipboard](https://luckly007.oss-cn-beijing.aliyuncs.com/img/copy-to-clipboard.png)

