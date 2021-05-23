---
title: "《Git上传文件报错》"
subtitle: ""
date: 2019-05-22T23:17:35+08:00
lastmod: 2019-05-22T23:17:35+08:00
draft: false
author: ""
authorLink: ""
description: "git错误处理"
images: []
tags: ["git"]
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



> 如何解决git上传文件出错[rejected] master -> master (fetch first) error: failed to push some refs to '



<!--more-->

刚开始用git上传文件的时候遇到了一些问题
第一个问题
上传步骤：

```
git add .
git commit -m "提示消息"
git push origin master
```

出错：

```
! [rejected] master -> master (fetch first) error: failed to push some refs to ' 。。。'
```

出现这个问题是因为github中的README.md文件不在本地代码目录中，可以通过如下命令进行代码合并

```
git pull --rebase origin master
```

第二个问题
! [remote rejected] master -> master (pre-receive hook declined)

在推送代码时报错如上，网上搜到的方法说是项目的setting中master是受保护的项目，所以开发者无法推送，解决方法是修改protected的设置，或者新建一个分支，推送到自己的分支上

前天准备上传一个project到GitLab上，但是试了很多次都上传不上去，报错如下：

! [remote rejected] master -> master (pre-receive hook declined)

截图：

![img](http://i.imgur.com/YCD9tA2.png)

一开始还以为自己用户名和密码错误，试了好多次，网上搜所索也没搜索到明确的结果，不过最后还是找到了解决的办法。

git push不上去的原因在于所push的分支权限为protected,只有项目的管理员或者项目的管理员指派的具有相应权限的人才能进行push,要进行项目的push,有如下两种方法：

### 1.将所要push的内容所在的分支的protected权限关闭

(1)进入所在项目的settings

![img](http://i.imgur.com/F8Lgf37.png)

(2)点击进入Protected branches,点击unprotected将master分支的权限改变，即关闭master的protected权限

![img](http://i.imgur.com/aLQQpJn.png)

### 2.新建其它分支，将项目push到新建的分支上，后期再进行merge

(1)新建分支

```
git branch 分支名
```

(2)切换分支

```
git checkout 分支名
```

(3)进行项目上传

```
git add .

git commit -m "提交的信息"

git remote add origin 远程仓库地址

git push -u origin 分支名
```

没事就来写写博客哈哈哈！

第三个问题

```
git add .
git push origin master
```

出现了这样的问题 everything up-to-date

原因：git提交改动到缓存，要push的时候不会将本地所有的分支都push掉，所以出现这个问题。那么我们就需要新建分支提交改动然后合并分支。

1.先创建一个新的分支提交改动

```
 git branch newbranch
```

2.检查这条命令是否创建成功

```
 git branch
```

这时终端会输出：

```
newbranch

*master
```

这样就创建成功了，前面的*代表的是当前你所在的工作分支，接下来就要切换工作分支。

3.git checkout newbranch

4.然后将你的改动提交到新的分支上

```
git add .


git commit -m "提示消息"
```

此时可以$ git status 检查下提交情况。如果提交成功，我们接下来就要回主分支了，$ git checkout master

5.我们将新分支提交的改动合并到主分支上

```
 git merge newbranch
```

合并分支可能产生冲突这是正常的，虽然我们这是新建的分支不会产生冲突，但还是在这里记录下。可以用

git diff 来查看产生冲突的文件，然后做对应的修改再提交一次就可以了。

6.我们的问题解决了，接下来就可以push代码了

```
git push -u origin master
```

7.最后，新建分支的朋友别忘了删除分支

```
 git branch -D newbranch
```

如果想保留分支只是想删除已经合并的部分只要把大写的D改成小写的d就行了。

总结，我在上传的过程中大概就遇到了这几个主要问题，网上搜了很多解决方案，发现即使遇到的问题一样，解决方法也可能不适用自己。记录一下，以后再看有没有其他解决方法和问题。
