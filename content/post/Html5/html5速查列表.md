---
title: "Html5速查列表"
date: 2021-09-18T08:39:13+08:00
draft: false
tags: ["html"]
categories: ["html"]
---

# HTML 速查列表

------

HTML 速查列表. 你可以打印它，以备日常使用。

## HTML 基本文档

```html
<!DOCTYPE html>
<html>
<head>
<title>文档标题</title>
</head>
<body>
可见文本...
</body>
</html>
```



## 基本标签（Basic Tags）



```html
<h1>最大的标题</h1>
<h2> . . . </h2>
<h3> . . . </h3>
<h4> . . . </h4>
<h5> . . . </h5>
<h6>最小的标题</h6>
 
<p>这是一个段落。</p>
<br> （换行）
<hr> （水平线）
<!-- 这是注释 -->
```



## 文本格式化（Formatting）

```html
<b>粗体文本</b>
<code>计算机代码</code>
<em>强调文本</em>
<i>斜体文本</i>
<kbd>键盘输入</kbd> 
<pre>预格式化文本</pre>
<small>更小的文本</small>
<strong>重要的文本</strong>
 
<abbr> （缩写）
<address> （联系信息）
<bdo> （文字方向）
<blockquote> （从另一个源引用的部分）
<cite> （工作的名称）
<del> （删除的文本）
<ins> （插入的文本）
<sub> （下标文本）
<sup> （上标文本）
```



## 链接（Links）

```html
普通的链接：<a href="http://www.example.com/">链接文本</a>
图像链接： <a href="http://www.example.com/"><img src="URL" alt="替换文本"></a>
邮件链接： <a href="mailto:webmaster@example.com">发送e-mail</a>
书签：
<a id="tips">提示部分</a>
<a href="#tips">跳到提示部分</a>
```



## 图片（Images）

```html
<img loading="lazy" src="URL" alt="替换文本" height="42" width="42">
```



## 无序列表

```html
<style type="text/css">
h1 {color:red;}
p {color:blue;}
</style>
<div>文档中的块级元素</div>
<span>文档中的内联元素</span>
```

## 有序列表

```html
<ul>
    <li>项目</li>
    <li>项目</li>
</ul>
```

## 样式/区块（Styles/Sections）

```html
<ol>
    <li>第一项</li>
    <li>第二项</li>
</ol>
```

## 定义列表

```html
<dl>
  <dt>项目 1</dt>
    <dd>描述项目 1</dd>
  <dt>项目 2</dt>
    <dd>描述项目 2</dd>
</dl>
```

## 表格（Tables）

```html
<table border="1">
  <tr>
    <th>表格标题</th>
    <th>表格标题</th>
  </tr>
  <tr>
    <td>表格数据</td>
    <td>表格数据</td>
  </tr>
</table>
```

## 框架（Iframe）

```html
<iframe src="demo_iframe.htm"></iframe>
```

## 表单（Forms）

```html
<form action="demo_form.php" method="post/get">
<input type="text" name="email" size="40" maxlength="50">
<input type="password">
<input type="checkbox" checked="checked">
<input type="radio" checked="checked">
<input type="submit" value="Send">
<input type="reset">
<input type="hidden">
<select>
<option>苹果</option>
<option selected="selected">香蕉</option>
<option>樱桃</option>
</select>
<textarea name="comment" rows="60" cols="20"></textarea>
 
</form>
```



## 实体（Entities）

```html
&lt; 等同于 <
&gt; 等同于 >
&#169; 等同于 ©
```



**对于vuejs的学习我大概分三个层次：**

# 第一层：了解概念，理解原理，不求甚解即可。

大到nodejs/webpack，搞清楚作用，运作流程；小到vuex/vue-router作用，预期目标。搞清楚这些原理，你基本就告别了盲人摸象的层次，就算遇到问题都有一个基本的思考能力了。

# 第二层：学习方法，快速上手，代码实践。

我一直秉承学习就是一个抄袭的过程，而且是效率最高的。最简单直接的方法就是download一个好的项目看一下别人是怎么写的，随着学习的精进直接看像elementUI iview这样的框架，看别人是怎么实现的，来一场与高手的脑洞碰撞。

# 第三层：独立思考，贡献开源造轮子。

技术栈储备到达一定程度，对Vue的使用及设计思想有了一定的深入理解，此时不妨考虑造个轮子，造轮子能让你的思维更加缜密，能更快的整合知识点解决针对性问题，最重要的是这玩意儿还可以装逼，加薪升职的最佳助力。

封神之路尚不可知，路漫漫其修远兮，一直努力未尝不可。

当然如果是首次学框架，之前没有学过框架的话。建议先不要急着去学习Vue的单页面开发（使用.vue文件）。可以先使用<script>引入的方式，在html文件里使用vue，从而去学习一些数据绑定，条件渲染，列表渲染等特性。如果学习能力强一点可以直接看文档学习，Vue的官方文档写得不错，实在不行可以看视频入门。



学习步骤的话：首先了解Vue是什么东西，然后了解一下SPA（单页面应用）-> 学习Vue的声明周期 -> 学习Vue的一些基本API（比如：watch/computed 等） -> 学习一下单文件组件 -> 就可以做练习项目了
