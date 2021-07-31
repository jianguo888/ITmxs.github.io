---
title: "《Git常用命令》"
subtitle: ""
date: 2021-07-30T23:05:12+08:00
lastmod: 2021-07-30T23:05:12+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["git"]
categories: ["git"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: ""
featuredImagePreview: ""

toc:
  enable: true
  auto: true
math:
  enable: false
lightgallery: false
license: ""
share:
  enable: true
comment: true
mapbox:
  accessToken: ""
---



> 本文主要介绍

<!--more-->

### 创建新的仓库

- 新建文件夹，打开，执行

  ```
  git init
  ```

### 检出仓库

- 创建一个本地仓库克隆版本

  ```
  git clone /path/to/repository
  ```

- 创建远程服务器上的仓库

  ```
  git clone username@host:/path/to/repository
  ```

### Git工作流

一个本地仓库由git维护的3客”树”组成。

1. 工作目录`working dic`。 – 它持有实际文件
2. 暂存区`Index`。 – 它像个缓冲区域，临时保存你的改动
3. `HEAD`。 – 它指向你最后一次提交的结果

work flow：`working dic` –add– ==> `Index` –commit– ==>`HEAD`

### 添加和提交

- 添加到暂存区

  ```
  git add <filename>
  git add *
  git add .
  ```

- 提交到HEAD

  ```
  git commit -m 'some comment'
  git commit -m "some comment"
  ```

- 添加提交二合一

  ```
  git commit -a -m 'some comment'
  ```

### 查看仓库修改状态

```
git status
```

### 推送更改

- 现在的改动已经提交到本地仓库的`HEAD`中，提交到远程仓库

  ```
  git push
  ```

> 格式`git push (remote) (branch)`,
> 第一次push必须这样写 `git push origin master:master`

### 推送到具体某一分支

```
git push origin someBranch
```

### 分支操作

- 查看分支

  ```
  git branch
  ```

- 查看分支最后提交状态

  ```
  git branch -v
  ```

  > `--merged` 与 `--no-merged` 这两个有用的选项可以过滤这个列表中已经合并或尚未合并到当前分支的分支。 如果要查看哪些分支已经合并到当前分支，可以运行 `git branch --merged`

  ```
    aBranch
    iss53
  * master
  ```

- 新建分支

  ```
  git branch aBranch
  ```

- 切换到`aBranch`分支

  ```
  git checkout aBranch
  ```

- 新建切换分支2合一

  ```
  git checkout - b aBranch
  ```

- 删除分支

  ```
  git branch -d aBranch
  ```

如果将要删除的分支还有没有合并的内容，删除将会失败，使用`-D`强制删除

```
error: The branch 'testBranch' is not fully merged.
If you are sure you want to delete it, run 'git branch -D testBranch'.
```

- 分支重命名

  ```
  git branch -m oldBranchName newBranchName
  ```

### 分支操作实例

`master`是默认的分支。在其他分支上进行开发，完成后再将他们合并到主分支上。

1. 创建一个叫“feature_x”的分支，并切换过去

   ```
   git checkout -b feature_x
   ```

2. 切换回主分支：

   ```
   git checkout master
   ```

3. 删除新建分支

   ```
   git branch -d feature_x
   ```

4. 将分支推送到远程仓库

   ```
   git push origin <branch>
   ```

### 更新与合并

- 更新你的本地仓库到最新改动

  ```
  git pull
  ```

会在你的工作目录 抓取`fetch`并合并`merge`远端的改动

- 合并`aBranch`分支回`master`分支

  ```
  git checkout master
  git merge aBranch
  ```

这种方式叫做直接合并（straight merge）
注意没参数的情况下`merge`是`fast-forward`的，即Git将`master`分支的指针直接移到`aBranch`的最前方。

换句话说，如果顺着一个分支走下去可以到达另一个分支的话，那么Git在合并两者时，只会简单移动指针，所以这种合并成为快进式(`Fast-forward`)

### 如何避免每次输入密码

如果你正在使用 HTTPS URL 来推送，Git 服务器会询问用户名与密码。 默认情况下它会在终端中提示服务器是否允许你进行推送。

如果不想在每一次推送时都输入用户名与密码，你可以设置一个 `credential cache`。 最简单的方式就是将其保存在内存中几分钟，可以简单地运行 `git config --global credential.helper cache` 来设置它。

### 压合合并`squashed commits`

将一条分支上的若干个提交条目压合成一个提交条目，提交到另一条分支的末梢。

把`aBranch`分支上的所有提交压合成主分支上的一个提交，即压合提交：

```
git checkout master
git merge --squash aBranch
```

此时，`aBranch`上的所有提交已经合并到当前工作区并暂存，但还没有作为一个提交，可以像其他提交一样，把这个改动提交到版本库中：

```
git commit -m 'some comment'
```

### 拣选合并`cherry-picking`

拣选另一条分支上的某个提交条目的改动带到当前分支上。

每一次提交都会产生一个全局唯一的提交名称，利用这个名称就可以进行拣选提交。

比如在dev上的某个提交叫：`321d76f`

把它合并到`master`中：

```
git checkout master
git cherry-pick 321d76f
```

要拣选多个提交，可以给`git cherry-pick`命令传递`-n`选项，比如：

```
git cherry-pick –n 321d76f
```

这样在拣选了这个改动之后，进行暂存而不立即提交，接着可以进行下一个拣选操作，一旦拣选完需要的各个提交，就可以一并提交。

如果发生冲突，解决冲突完后执行`git cherry-pick --continue`继续拣选合并。

### 解决冲突（conflicts）

1. 通过`CONFLICT (content):`找到冲突文件所在的位置
2. 打开冲突文件，修改冲突后保存
3. 通过`git status`查看冲突是否解决
4. 通过`git diff <source_branch> <target_banch>`预览差异
5. 提交更改`git commit -a -m 'some comment'`

### 查看远程分支的完整列表

```
git ls-remote
```

### 获取远程分支更多信息

```
git remote show
```

### 新建远程分支

```
git push --set-upstream origin newBranch
```

### 提交分支数据到远程服务器

```
git push origin <local_branch_name>:<remote_branch_name>
```

### 删除远程分支

```
git push origin :develop
git push origin --delete develop
```

### 标签

创建一个1.0.0的标签

```
git tag 1.0.0 1b2e1d63ff
```

**1b2e1d63ff**是你想要标记的提交ID的前10位字符，可使用下列命令获取提交ID

```
git log
```

### 替换本地改动

```
git checkout -- <filename>
```

此命令会使用`HEAD`中的最新内容替换掉你的工作目录中的文件。已添加到暂存区的改动以及新文件都不会受到影响。

假如你想丢弃你在本地的所有改动与提交，可以到服务器上获取最新的版本历史，并将你本地主分支指向它

```
git fetch orgin
git reset --hard origin/master
```

参考链接

- [Git - 简明指南](http://rogerdudler.github.io/git-guide/index.zh.html)
- [Git社区参考指南](https://git-scm.com/book/en/v2)
- [Github Help](https://help.github.com/)
- [图解Git](http://marklodato.github.io/visual-git-guide/index-zh-cn.html)
- [Think like a git](http://think-like-a-git.net/)