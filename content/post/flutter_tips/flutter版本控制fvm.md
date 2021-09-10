---
title: "Flutter版本控制fvm"
date: 2021-08-25T09:16:53+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["Flutter"]
categories: ["Flutter"]
---



![image-20210825091710065](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210825091710065.png)

![image-20210825092145431](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210825092145431.png)





安装fvm后，考虑设置环境变量 `FVM_HOME` 或 `FVM_GIT_CACHE`，否则默认安装 flutter SDK 缓存路径为 `~/fvm/versions`；

![image-20210827084411967](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210827084411967.png)

## fvm releases

`fvm releases`: View all Flutter SDK releases available for install.

列举远程可用的 flutter SDK 发行版。

```javascript
Microsoft Windows [版本 10.0.19042.1165]
(c) Microsoft Corporation。保留所有权利。

C:\Users\85285>fvmlist
'fvmlist' 不是内部或外部命令，也不是可运行的程序
或批处理文件。

C:\Users\85285>fvm list

No SDKs have been installed yet. Flutter. SDKs installed outside of fvm will not be displayed.


C:\Users\85285>fvm releases
Feb 27 18  │ v0.1.6
Mar 6 18   │ v0.1.8
Mar 7 18   │ v0.1.9
Mar 7 18   │ v0.2.0
Mar 12 18  │ v0.2.1
Mar 13 18  │ v0.1.5
Mar 16 18  │ v0.2.2
Mar 20 18  │ v0.2.3
Mar 26 18  │ v0.2.4
Mar 30 18  │ v0.2.5
Mar 30 18  │ v0.2.6
Apr 2 18   │ v0.2.7
Apr 2 18   │ v0.2.3
Apr 3 18   │ v0.2.8
Apr 5 18   │ v0.2.9
Apr 9 18   │ v0.2.8
Apr 9 18   │ v0.2.10
Apr 12 18  │ v0.2.11
Apr 16 18  │ v0.3.0
Apr 20 18  │ v0.3.1
Apr 20 18  │ v0.3.2
Apr 23 18  │ v0.3.3
Apr 24 18  │ v0.3.4
Apr 25 18  │ v0.3.5
May 2 18   │ v0.3.1
May 4 18   │ v0.3.6
May 7 18   │ v0.3.2
May 7 18   │ v0.4.0
May 9 18   │ v0.4.1
May 9 18   │ v0.4.2
May 11 18  │ v0.4.3
May 14 18  │ v0.4.4
May 22 18  │ v0.4.4
May 29 18  │ v0.5.0
May 30 18  │ v0.5.1
Jun 8 18   │ v0.5.2
Jun 11 18  │ v0.5.3
Jun 11 18  │ v0.5.4
Jun 18 18  │ v0.5.5
Jun 19 18  │ v0.5.1
Jul 3 18   │ v0.5.6
Jul 16 18  │ v0.5.7
Aug 10 18  │ v0.5.8
Aug 20 18  │ v0.6.0
Aug 21 18  │ v0.6.1
Aug 21 18  │ v0.6.2
Aug 22 18  │ v0.7.0
Aug 27 18  │ v0.7.1
Aug 28 18  │ v0.6.0
Aug 28 18  │ v0.7.2
Aug 29 18  │ v0.7.3
Aug 30 18  │ v0.7.4
Aug 31 18  │ v0.7.5
Sep 5 18   │ v0.7.3
Sep 6 18   │ v0.8.0
Sep 7 18   │ v0.8.1
Sep 10 18  │ v0.8.2
Sep 14 18  │ v0.8.3
Sep 14 18  │ v0.8.4
Sep 17 18  │ v0.8.5
Sep 18 18  │ v0.8.6
Sep 18 18  │ v0.8.2
Sep 18 18  │ v0.8.7
Sep 18 18  │ v0.9.0
Sep 18 18  │ v0.9.1
Sep 20 18  │ v0.9.2
Sep 25 18  │ v0.9.3
Sep 27 18  │ v0.9.4
Sep 28 18  │ v0.9.5
Oct 4 18   │ v0.9.6
Oct 9 18   │ v0.9.4
Oct 10 18  │ v0.10.0
Oct 20 18  │ v0.10.1
Nov 1 18   │ v0.10.2
Nov 6 18   │ v0.10.2
Nov 8 18   │ v0.11.0
Nov 9 18   │ v0.11.1
Nov 10 18  │ v0.11.2
Nov 11 18  │ v0.11.3
Nov 13 18  │ v0.11.3
Nov 13 18  │ v0.11.4
Nov 14 18  │ v0.11.5
Nov 14 18  │ v0.11.6
Nov 19 18  │ v0.11.7
Nov 19 18  │ v0.11.7
Nov 20 18  │ v0.11.8
Nov 20 18  │ v0.11.8
Nov 21 18  │ v0.11.9
Nov 21 18  │ v0.11.9
Nov 27 18  │ v0.11.10
Nov 27 18  │ v0.11.10
Nov 28 18  │ v0.11.11
Nov 28 18  │ v0.11.11
Nov 29 18  │ v0.11.12
Nov 29 18  │ v0.11.12
Nov 30 18  │ v0.11.13
Nov 30 18  │ v0.11.13
Dec 4 18   │ v1.0.0
Dec 4 18   │ v1.0.0
Dec 4 18   │ v1.0.0
Dec 12 18  │ v1.1.0
Jan 26 19  │ v1.1.8
Jan 29 19  │ v1.1.8
Jan 31 19  │ v1.2.0
Feb 15 19  │ v1.2.1
Feb 26 19  │ v1.2.1
Feb 26 19  │ v1.2.1
Feb 28 19  │ v1.2.2
Feb 28 19  │ v1.3.0
Mar 1 19   │ v1.3.1
Mar 1 19   │ v1.3.2
Mar 4 19   │ v1.3.3
Mar 5 19   │ v1.3.4
Mar 7 19   │ v1.3.6
Mar 7 19   │ v1.3.7
Mar 7 19   │ v1.3.8
Mar 14 19  │ v1.3.9
Mar 15 19  │ v1.3.10
Mar 15 19  │ v1.3.8
Mar 18 19  │ v1.3.11
Mar 18 19  │ v1.3.12
Mar 19 19  │ v1.3.13
Mar 26 19  │ v1.3.14
Mar 26 19  │ v1.4.0
Mar 27 19  │ v1.4.1
Mar 27 19  │ v1.4.2
Mar 27 19  │ v1.4.3
Mar 28 19  │ v1.4.4
Mar 28 19  │ v1.4.5
Mar 28 19  │ v1.4.6
Mar 29 19  │ v1.4.6-hotfix.1
Mar 29 19  │ v1.4.7
Apr 3 19   │ v1.4.8
Apr 4 19   │ v1.4.9
Apr 4 19   │ v1.4.10
Apr 8 19   │ v1.4.11
Apr 9 19   │ v1.4.12
Apr 10 19  │ v1.4.13
Apr 10 19  │ v1.4.14
Apr 10 19  │ v1.4.15
Apr 11 19  │ v1.4.16
Apr 11 19  │ v1.4.17
Apr 12 19  │ v1.4.18
Apr 12 19  │ v1.4.9-hotfix.1
Apr 16 19  │ v1.5.0
Apr 17 19  │ v1.5.1
Apr 17 19  │ v1.5.2
Apr 19 19  │ v1.5.3
Apr 22 19  │ v1.5.4
Apr 23 19  │ v1.5.5
Apr 24 19  │ v1.5.6
Apr 24 19  │ v1.5.7
Apr 26 19  │ v1.5.8
Apr 26 19  │ v1.5.4
Apr 30 19  │ v1.5.4-hotfix.1
May 2 19   │ v1.5.4-hotfix.2
May 7 19   │ v1.5.4-hotfix.2
May 17 19  │ v1.6.0
May 22 19  │ v1.6.1
May 23 19  │ v1.6.2
May 24 19  │ v1.6.3
May 28 19  │ v1.6.4
May 28 19  │ v1.6.5
May 29 19  │ v1.6.6
May 30 19  │ v1.6.3
May 31 19  │ v1.6.7
Jun 3 19   │ v1.7.0
Jun 4 19   │ v1.7.1
Jun 6 19   │ v1.7.2
Jun 7 19   │ v1.7.3
Jun 14 19  │ v1.7.4
Jun 21 19  │ v1.7.6
Jun 22 19  │ v1.7.7
Jun 22 19  │ v1.7.8
Jun 25 19  │ v1.7.9
Jun 26 19  │ v1.7.10
Jun 28 19  │ v1.7.11
Jul 2 19   │ v1.7.8+hotfix.2
Jul 8 19   │ v1.7.8+hotfix.2
Jul 9 19   │ v1.7.8+hotfix.3
Jul 9 19   │ v1.7.8+hotfix.3
Jul 11 19  │ v1.8.1
Jul 19 19  │ v1.8.2
Jul 19 19  │ v1.7.8+hotfix.4
Jul 24 19  │ v1.7.8+hotfix.4
Jul 31 19  │ v1.8.3
Aug 8 19   │ v1.8.3
Aug 15 19  │ v1.9.0
Aug 21 19  │ v1.9.1
Aug 21 19  │ v1.9.2
Aug 22 19  │ v1.9.3
Aug 23 19  │ v1.9.4
Aug 24 19  │ v1.9.5
Aug 29 19  │ v1.9.6
Aug 29 19  │ v1.9.7
Sep 4 19   │ v1.9.1+hotfix.1
Sep 6 19   │ v1.10.0
Sep 8 19   │ v1.9.1+hotfix.2
Sep 9 19   │ v1.10.1
Sep 10 19  │ v1.9.1+hotfix.2
Sep 14 19  │ v1.10.2
Sep 17 19  │ v1.10.3
Sep 19 19  │ v1.10.4
Sep 20 19  │ v1.10.5
Sep 25 19  │ v1.10.6
Sep 26 19  │ v1.9.1+hotfix.3
Sep 27 19  │ v1.9.1+hotfix.4
Oct 1 19   │ v1.9.1+hotfix.4
Oct 2 19   │ v1.10.7
Oct 4 19   │ v1.10.8
Oct 4 19   │ v1.10.9
Oct 4 19   │ v1.10.10
Oct 4 19   │ v1.10.11
Oct 4 19   │ v1.10.12
Oct 8 19   │ v1.10.13
Oct 8 19   │ v1.10.14
Oct 10 19  │ v1.10.7
Oct 17 19  │ v1.9.1+hotfix.5
Oct 23 19  │ v1.9.1+hotfix.6
Nov 7 19   │ v1.10.15
Nov 11 19  │ v1.10.16
Nov 13 19  │ v1.11.0
Nov 14 19  │ v1.12.0
Nov 15 19  │ v1.12.1
Nov 15 19  │ v1.12.2
Nov 18 19  │ v1.12.3
Nov 18 19  │ v1.12.4
Nov 21 19  │ v1.12.5
Nov 21 19  │ v1.12.6
Nov 21 19  │ v1.12.7
Nov 22 19  │ v1.11.0
Nov 22 19  │ v1.12.8
Nov 22 19  │ v1.12.9
Nov 23 19  │ v1.12.10
Nov 23 19  │ v1.12.11
Nov 25 19  │ v1.12.12
Nov 26 19  │ v1.12.13
Nov 26 19  │ v1.12.14
Nov 27 19  │ v1.12.15
Dec 2 19   │ v1.12.16
Dec 3 19   │ v1.12.13+hotfix.1
Dec 4 19   │ v1.12.13+hotfix.2
Dec 5 19   │ v1.13.0
Dec 6 19   │ v1.12.13+hotfix.3
Dec 10 19  │ v1.12.13+hotfix.4
Dec 11 19  │ v1.12.13+hotfix.5
Dec 11 19  │ v1.12.13+hotfix.5
Dec 11 19  │ v1.12.13+hotfix.6
Dec 12 19  │ v1.13.1
Dec 13 19  │ v1.13.2
Dec 19 19  │ v1.13.3
Dec 20 19  │ v1.13.4
Dec 21 19  │ v1.13.5
Jan 2 20   │ v1.13.6
Jan 7 20   │ v1.13.7
Jan 10 20  │ v1.13.8
Jan 14 20  │ v1.13.9
Jan 15 20  │ v1.13.6
Jan 15 20  │ v1.14.0
Jan 16 20  │ v1.14.1
Jan 21 20  │ v1.14.2
Jan 22 20  │ v1.14.3
Jan 25 20  │ v1.14.4
Jan 27 20  │ v1.12.13+hotfix.7
Jan 27 20  │ v1.14.5
Jan 29 20  │ v1.14.6
Feb 5 20   │ v1.14.6
Feb 6 20   │ v1.15.0
Feb 6 20   │ v1.15.1
Feb 7 20   │ v1.15.2
Feb 11 20  │ v1.15.3
Feb 11 20  │ v1.12.13+hotfix.8
Mar 4 20   │ v1.15.4
Mar 4 20   │ v1.15.5
Mar 5 20   │ v1.15.6
Mar 5 20   │ v1.15.7
Mar 5 20   │ v1.15.8
Mar 5 20   │ v1.15.9
Mar 5 20   │ v1.15.10
Mar 5 20   │ v1.15.11
Mar 5 20   │ v1.15.12
Mar 6 20   │ v1.15.13
Mar 6 20   │ v1.15.14
Mar 6 20   │ v1.15.15
Mar 6 20   │ v1.15.16
Mar 6 20   │ v1.15.17
Mar 6 20   │ v1.15.18
Mar 10 20  │ v1.15.19
Mar 11 20  │ v1.15.20
Mar 13 20  │ v1.15.21
Mar 16 20  │ v1.15.22
Mar 17 20  │ v1.15.17
Mar 18 20  │ v1.16.0
Mar 19 20  │ v1.16.1
Mar 24 20  │ v1.16.2
Mar 27 20  │ v1.16.3
Mar 31 20  │ 1.17.0-dev.0.0
Apr 1 20   │ 1.17.0-dev.1.0
Apr 2 20   │ 1.17.0-dev.2.0
Apr 2 20   │ 1.17.0-dev.3.0
Apr 2 20   │ 1.17.0-dev.4.0
Apr 4 20   │ 1.17.0-dev.5.0
Apr 4 20   │ 1.18.0-dev.0.0
Apr 6 20   │ 1.18.0-dev.1.0
Apr 6 20   │ 1.17.0-dev.3.1
Apr 6 20   │ 1.18.0-dev.2.0
Apr 7 20   │ 1.18.0-dev.3.0
Apr 7 20   │ 1.18.0-dev.4.0
Apr 13 20  │ 1.18.0-dev.5.0
Apr 17 20  │ v1.12.13+hotfix.9
Apr 21 20  │ 1.18.0-6.0.pre
Apr 22 20  │ 1.17.0-3.2.pre
Apr 24 20  │ 1.18.0-7.0.pre
Apr 24 20  │ 1.18.0-8.0.pre
Apr 28 20  │ 1.17.0-3.3.pre
May 2 20   │ 1.17.0-3.4.pre
May 5 20   │ 1.18.0-9.0.pre
May 6 20   │ 1.18.0-10.0.pre
May 6 20   │ 1.17.0
May 6 20   │ 1.18.0-11.0.pre
May 6 20   │ 1.18.0-12.0.pre
May 7 20   │ 1.18.0-13.0.pre
May 8 20   │ 1.19.0-0.0.pre
May 12 20  │ 1.19.0-1.0.pre
May 13 20  │ 1.17.1
May 14 20  │ 1.18.0-11.1.pre
May 28 20  │ 1.17.2
Jun 1 20   │ 1.19.0-2.0.pre
Jun 2 20   │ 1.19.0-3.0.pre
Jun 5 20   │ 1.19.0-4.0.pre
Jun 5 20   │ 1.17.3
Jun 9 20   │ 1.19.0-5.0.pre
Jun 10 20  │ 1.19.0-4.1.pre
Jun 11 20  │ 1.20.0-0.0.pre
Jun 18 20  │ 1.17.4
Jun 26 20  │ 1.20.0-2.0.pre
Jul 1 20   │ 1.17.5
Jul 1 20   │ 1.19.0-4.2.pre
Jul 1 20   │ 1.19.0-4.3.pre
Jul 5 20   │ 1.20.0-3.0.pre
Jul 9 20   │ 1.20.0-7.0.pre
Jul 11 20  │ 1.20.0-7.1.pre
Jul 17 20  │ 1.21.0-1.0.pre
Jul 21 20  │ 1.20.0-7.2.pre
Jul 29 20  │ 1.20.0-7.3.pre
Aug 3 20   │ 1.20.0-7.4.pre
Aug 5 20   │ 1.20.0
Aug 6 20   │ 1.20.1
Aug 7 20   │ 1.21.0-7.0.pre
Aug 13 20  │ 1.21.0-9.0.pre
Aug 13 20  │ 1.20.2
Aug 14 20  │ 1.20.2
Aug 19 20  │ 1.21.0-9.1.pre
Aug 21 20  │ 1.22.0-1.0.pre
Aug 28 20  │ 1.21.0-9.2.pre
Sep 1 20   │ 1.22.0-9.0.pre
Sep 2 20   │ 1.20.3
Sep 14 20  │ 1.22.0-12.0.pre
Sep 15 20  │ 1.20.4
Sep 16 20  │ 1.22.0-12.1.pre
Sep 22 20  │ 1.23.0-4.0.pre
Sep 28 20  │ 1.22.0-12.2.pre
Sep 29 20  │ 1.22.0-12.3.pre
Oct 1 20   │ 1.22.0
Oct 1 20   │ 1.23.0-7.0.pre
Oct 8 20   │ 1.22.0-12.4.pre
Oct 8 20   │ 1.22.1
Oct 12 20  │ 1.23.0-13.0.pre
Oct 14 20  │ 1.23.0-18.0.pre
Oct 15 20  │ 1.23.0-18.1.pre
Oct 16 20  │ 1.22.2
Oct 20 20  │ 1.24.0-1.0.pre
Oct 22 20  │ 1.24.0-3.0.pre
Oct 30 20  │ 1.22.3
Oct 30 20  │ 1.24.0-6.0.pre
Nov 5 20   │ 1.24.0-7.0.pre
Nov 13 20  │ 1.22.4
Nov 16 20  │ 1.24.0-10.1.pre
Nov 18 20  │ 1.24.0-10.2.pre
Nov 19 20  │ 1.24.0-10.2.pre
Dec 2 20   │ 1.25.0-4.0.pre
Dec 10 20  │ 1.25.0-8.0.pre
Dec 10 20  │ 1.22.5
Dec 16 20  │ 1.25.0-8.1.pre
Dec 16 20  │ 1.26.0-1.0.pre
Jan 6 21   │ 1.25.0-8.2.pre
Jan 13 21  │ 1.26.0-8.0.pre
Jan 15 21  │ 1.25.0-8.3.pre
Jan 21 21  │ 1.26.0-12.0.pre
Jan 25 21  │ 1.22.6
Jan 29 21  │ 1.26.0-17.1.pre
Feb 4 21   │ 1.26.0-17.2.pre
Feb 4 21   │ 1.26.0-17.2.pre
Feb 5 21   │ 1.26.0-17.3.pre
Feb 10 21  │ 1.26.0-17.4.pre
Feb 10 21  │ 1.27.0-1.0.pre
Feb 11 21  │ 1.26.0-17.5.pre
Feb 16 21  │ 1.26.0-17.6.pre
Feb 17 21  │ 1.27.0-4.0.pre
Feb 24 21  │ 1.26.0-17.7.pre
Feb 24 21  │ 1.27.0-8.0.pre
Feb 25 21  │ 1.26.0-17.8.pre
Mar 3 21   │ 2.0.0
Mar 3 21   │ 2.0.0
Mar 3 21   │ 2.1.0-10.0.pre
Mar 4 21   │ 2.0.1
Mar 4 21   │ 2.0.1
Mar 12 21  │ 2.0.2
Mar 13 21  │ 2.1.0-12.1.pre
Mar 15 21  │ 2.0.2
Mar 18 21  │ 2.1.0-12.2.pre
Mar 19 21  │ 2.0.3
Apr 2 21   │ 2.0.4
Apr 15 21  │ 2.2.0-10.1.pre
Apr 15 21  │ 2.2.0-10.1.pre
Apr 16 21  │ 2.0.5
Apr 27 21  │ 2.3.0-0.1.pre
Apr 29 21  │ 2.2.0-10.2.pre
Apr 30 21  │ 2.0.6
May 10 21  │ 2.2.0-10.3.pre
May 10 21  │ 2.3.0-1.0.pre
May 18 21  │ 2.2.0
May 18 21  │ 2.3.0-12.1.pre
May 19 21  │ 2.2.0
May 27 21  │ 2.2.1
May 28 21  │ 2.3.0-16.0.pre
Jun 11 21  │ 2.2.2
Jun 11 21  │ 2.2.2
Jun 17 21  │ 2.3.0-24.0.pre
Jun 25 21  │ 2.3.0-24.1.pre
--------------------------------------
Jul 1 21   │ 2.2.3             stable
--------------------------------------
Jul 2 21   │ 2.4.0-0.0.pre
Jul 13 21  │ 2.4.0-4.0.pre
Jul 22 21  │ 2.4.0-4.1.pre
Jul 22 21  │ 2.4.0-4.2.pre
Jul 27 21  │ 2.5.0-1.0.pre
Aug 5 21   │ 2.5.0-5.0.pre
Aug 12 21  │ 2.5.0-5.1.pre
--------------------------------------
Aug 12 21  │ 2.5.0-6.0.pre     dev
--------------------------------------
--------------------------------------
Aug 19 21  │ 2.5.0-5.2.pre     beta
--------------------------------------

C:\Users\85285>
```

## fvm install

`fvm install`: Installs Flutter SDK Version. Gives you the ability to install Flutter `releases` or `channels`.

- `fvm install dev/beta/stable/...`: 安装指定channel的当前版本 flutter SDK；
- `fvm install 2.2.0`: 安装指定版本(2.2.0)的 flutter SDK；

> 如果没有配置 `FVM_HOME` 或 `FVM_GIT_CACHE` 环境变量，则默认安装到 `~/fvm/versions/` 目录下。

以下安装指定版本和channel：

`fvm install 1.20.4`：老项目用的这个版本；



![image-20210825093129665](https://luckly007.oss-cn-beijing.aliyuncs.com/image/image-20210825093129665.png)

- `fvm install 1.22.6`：安装 flutter 2.0 之前最后一个 stable 版本 1.22.6；
- `fvm install stable`：由于当前的 stable 为 2.2.0，安装的 stable 等效于 `fvm install 2.2.0`；
- `fvm install dev`：安装最新的 dev 2.3.0-12.1.pre 预览版本，以便完成一些实验性功能开发和验证；

执行了 `fvm install 2.2.0` 后可执行 `fvm remove 2.2.0` 卸载上安装的 2.2.0 版本。

```javascript
fantasy@MBP ~ $ tree -L 1 ~/fvm/versions
/Users/fantasy/fvm/versions
├── 1.22.6
├── dev
└── stable

3 directories, 0 files

  
 
```

## fvm list

列举查看本地已安装的版本。

```javascript
fantasy@MBP ~ $ fvm list
Cache Directory:  /Users/fantasy/fvm/versions

stable
dev
1.22.6

  
 
```

## fvm use

在当前 flutter_project 目录执行 `fvm use version` 即可切换flutter SDK版本。

FVM will create a relative *symlink* in your project from `.fvm/flutter_sdk` to the cache of the selected version. Add it to your `.gitignore`.

> 当前目录下会生成一个 `.fvm` 文件夹，考虑将 `.fvm/*` 添加到 .gitignore 文件，避免误传上库。

在flutter项目 my_flutter_project 下执行 `fvm use stable` 切换 flutter SDK 为最新 stable 版本：

```javascript
fantasy@MBP ~/Projects//my_flutter_project $ fvm use stable
Project now uses Flutter [stable]

  
 
```

> 其中 `.fvm/flutter_sdk` 将软链到 `~/fvm/versions/stable`；配置文件 `.fvm/fvm_config.json` 中同步记录了 `"flutterSdkVersion": "stable"`。

接下来，可用 `fvm flutter` 代替 `flutter` 命令，以便自动pick当前repo所用的SDK。

------

在当前项目目录下执行 `fvm list`，当前正在使用的 flutter SDK 版本将会标识为 *active*：

```javascript
fantasy@MBP ~/Projects//my_flutter_project $ fvm list
Cache Directory:  /Users/fantasy/fvm/versions

stable (active)
dev
1.22.6

  
 
```

在当前项目目录下执行 `fvm flutter --version`，可查看使用的 flutter SDK 版本信息：

```javascript
fantasy@MBP ~/Projects//my_flutter_project $ fvm flutter --version
Flutter 2.2.0 • channel stable • https://github.com/flutter/flutter.git
Framework • revision b22742018b (11 days ago) • 2021-05-14 19:12:57 -0700
Engine • revision a9d88a4d18
Tools • Dart 2.13.0

  
 
```

执行其他代理命令安装依赖包更新、运行：

```javascript
fvm flutter clean
fvm flutter pub get
fvm flutter run

  
 
```

## fvm flavor

`project flavors`：某个项目外发版本还是用稳定的 1.22.6，但是 flutter 2 的适配工作同步进行中，则可为该项目创建多个 flavor，方便主干和适配分支切换 flavors 中预配的 flutter SDK 版本。

### Pin flavor version

指定某个 flutter SDK 版本为 flavor 别名。

To choose a Flutter SDK version for a specific flavor you just use the `use` command.

```javascript
fvm use {version} --flavor {flavor_name}

  
 
```

This will pin `version` to `flavor_name`，flavors 映射(flavor_name -> version)将被记录到配置文件 `.fvm/fvm_config.json` 中。

```javascript
fantasy@MBP ~/Projects//my_flutter_project $ fvm use 1.22.6 --flavor production
Project now uses Flutter [1.22.6] on [production] flavor.

fantasy@MBP ~/Projects//my_flutter_project $ fvm use stable --flavor stable
Project now uses Flutter [stable] on [stable] flavor.

fantasy@MBP ~/Projects//my_flutter_project $ fvm use dev --flavor dev
Project now uses Flutter [dev] on [dev] flavor.

fantasy@MBP ~/Projects//my_flutter_project $ cat .fvm/fvm_config.json
{
  "flutterSdkVersion": "stable",
  "flavors": { "production": "1.22.6", "stable": "stable", "dev": "dev"
  }
}

  
 
```

### View flavors

列举当前项目配置的 flavors(To list all configured flavors)。

```javascript
fantasy@MBP ~/Projects//my_flutter_project $ fvm flavor
Project flavors configured for "my_flutter_project":

[1] production
[2] dev
[3] stable

Select an environment: ^C

  
 
```

按照提示，可在 Select an environment 后面输入 `2` 或 `dev` 切换到 dev 开发环境。如果仅为查看，可按下 Ctrl+C 退出。

### Switch flavors

Will get the version configured for the flavor and set as the project version.

```javascript
fvm flavor {flavor_name}

  
 
```

为当前项目切换到 *flavor_name* 对应的版本。

例如 `fvm flavor production` 将切换到 1.22.6 版本，等效于 `fvm use 1.22.6`。

> 切换后，`.fvm/fvm_config.json` 中的 flutterSdkVersion 将同步更新为 `1.22.6`。

## IDE配置

### vscode

可全局配置 vscode(`～/Library/Application\ Support/Code\ -\ Insiders/User/settings.json`)：

```javascript
{
  "dart.flutterSdkPaths": ["/Users/usr/fvm/versions"]
}

  
 
```

或

```javascript
{
  "dart.flutterSdkPaths": [ "/Users/usr/fvm/versions/stable", "/Users/usr/fvm/versions/dev"
  ]
}

  
 
```

则可在 vscode 控制面板中执行 `Flutter: Change SDK` 切换 Flutter SDK 版本，将同步记录到当前项目的 vscode 配置文件 `.vscode/settings.json` 中(dart.flutterSdkPath)。

------

也可直接编辑当前项目的 vscode 配置文件 `.vscode/settings.json`：

```json
{
  "dart.flutterSdkPath": ".fvm/flutter_sdk",
  // or "dart.flutterSdkPaths": [".fvm/flutter_sdk"] // Remove .fvm files from search
  "search.exclude": { "**/.fvm": true
  },
  // Remove from file watching
  "files.watcherExclude": { "**/.fvm": true
  }
}

  
 
```

### AndroidStudio

参考 configuration。

## fvm global

如果APP目前还未完成适配flutter 2.0，暂时可将 1.22.6 设置为全局主力版本。

执行 `fvm global 1.22.6`，提示需要将相关路径添加到 PATH：

```javascript
fantasy@MBP ~ $ fvm global 1.22.6
Flutter "1.22.6" has been set as global
However your "flutter" path current points to:

.
to use global Flutter SDK through FVM you should change it to:

/Users/fantasy/fvm/default/bin

  
 
```

`vim ~/.zshrc` 打开编辑 zsh 配置，将 default Flutter SDK 可执行文件所在路径添加到环境变量 PATH：

```javascript
export PATH=$HOME/fvm/default/bin:$PATH

  
 
```

> 进入 `～/fvm` 目录可以看到，default 实际上是 `versions/1.22.6` 的替身软链。

如果iOS工程报以下错误，考虑执行 `flutter precache` 重拉工具链解决。

```javascript
[!] Invalid `Podfile` file: No such file or directory @ rb_file_s_stat - /Users/fantasy/fvm/versions/1.22.6/bin/cache/artifacts/engine/ios/Flutter.framework.

  
 
```

重新执行 `fvm list`，可以看到 1.22.6 已经被标识为 *global*：

```javascript
fantasy@MBP ~ $ fvm list
Cache Directory:  /Users/fantasy/fvm/versions

stable
beta
1.22.6 (global)

  
 
```

此时，执行 `flutter --version`，将显示全局版本为 `Flutter 1.22.6, Dart 2.10.5`。

## fvm taps

以下梳理网上比较常见的两个民间 fvm，可选使用。

### dashixiong91

基于 Flutter 的⼩程序框架实践 文末介绍 xinfeng-tech/fvm

[dashixiong91 / fvm](https://github.com/dashixiong91/fvm) - [dashixiong91 / homebrew-fvm](https://github.com/dashixiong91/homebrew-fvm)

```javascript
brew tap dashixiong91/fvm
brew install fvm

  
 
```

- Flutter Versions Manager
- Flutter SDK 多版本管理工具

以下基于旧的 xinfeng-tech/fvm：

- 使用fvm助力你吃上Flutter2 - vscode 配置
- FVM - Mac上管理 Flutter 多版本的神器 - Android Studio 配置

### befovy

go-fvm：flutter 版本切换助手：[befovy / fvm](https://github.com/befovy/fvm)

其核心逻辑就是在本地文件夹中缓存多个 Flutter 版本，并为项目创建指定 Flutter 版本的软链接。或者在全局环境创建指定版本的 Flutter 软链接。



