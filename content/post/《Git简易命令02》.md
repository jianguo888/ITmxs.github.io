---
title: "《Git简易命令02》"
subtitle: ""
date: 2019-05-23T14:14:45+08:00
lastmod: 2019-05-23T14:14:45+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["git","入门"]
categories: ["git"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/git.jpg"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/git.jpg"

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment:
  enable: true
mapbox:
  accessToken: ""
---



> 本文主要介绍简易的Git命令行入门教程

<!--more-->

### Git 全局设置:

```
git config --global user.name "name"
git config --global user.email "你的邮箱@qq.com"
```

### 创建 git 仓库:

```
mkdir graduation-project
cd graduation-project
git init
touch README.md
git add README.md
git commit -m "first commit"
git remote add origin https://gitee.com/用户名/仓库名.git
git push -u origin master
```

## 已有仓库?

```
cd existing_git_repo
git remote add origin https://gitee.com/用户名/仓库名.git
git push -u origin master
```