---
title: "如何将flutter_Web项目部署到github上"
date: 2021-08-15T17:49:29+08:00
draft: false
tags: ["flutter"]
categories: ["flutter"]
---

![如何将 flutter web 项目部署到 GitHub](https://miro.medium.com/max/1400/1*1orxIbVfgZa4mB_qEL17Yg.jpeg)

**是的，Flutter** 2 上线了！！！您知道**Flutter** 2 中的新功能、**Flutter Web 支持**达到了稳定的里程碑、宣布 Dart 2.12 和**Flutter** 2**发行**说明。从[这里](https://flutter.dev/web)检查更多

我假设你现在已经有了 Flutter 项目，就像我现在所拥有的一样，在这篇文章中，我们将看到如何将 Flutter 应用程序托管到 GitHub。

是的，在此之前，请确保您有一个 GitHub 帐户以使其正常工作。如果没有[创建](https://github.com/join)新的 GitHub 帐户



我现在本地有一个flutter项目，但是几分钟之后，是的，我说的几分钟之后，他就会被我托管到github上



![如何将 flutter web 项目部署到 GitHub](https://miro.medium.com/max/1210/1*cy5gXXcqgGDVcO4wg44q-Q.png)

让我们开始吧。

1. 首先为 Web 构建您的 Flutter 应用程序

在您的项目终端中运行命令来构建 Web 版本

<iframe src="https://codingwithtashi.medium.com/media/b8adb88c63a212b61b30de133ddef4c6" allowfullscreen="" frameborder="0" height="61" width="680" title="要点：d7c25de5e5512202fed40ef56973a30c" class="fd ek eg jj x" scrolling="auto" style="box-sizing: inherit; width: 680px; left: 0px; top: 0px; position: absolute; height: 61px;"></iframe>

这将需要几分钟，您将在**根项目的 build 文件夹中**获得已发布的**web 文件夹，**其中包含所有发布内容，例如 sql-wasm.js、index.html、main.dart 等。

2. 现在，您可以通过删除其中的旧内容，将所有 Web 文件夹内容从那里移动到根 Web 文件夹。

呼！完毕？

好的，一旦完成，使用以下格式创建新的 GitHub 存储库

*“username* .github.io”，其中 username 是您的用户名。下面的例子。

> **注意：它应该是确切的 username.github.io**

![如何将 flutter web 项目部署到 GitHub](https://miro.medium.com/max/60/1*KSfercdNKyk2js4fADmWVQ.png?q=20)

![如何将 flutter web 项目部署到 GitHub](https://miro.medium.com/max/700/1*KSfercdNKyk2js4fADmWVQ.png)

3. 现在将您的所有项目提交到 GitHub 。您应该在[https://github.com/username/username.github.io 上](https://github.com/username/username.github.io.)看到您的项目[。](https://github.com/username/username.github.io.)

Boom，大功告成，现在如果您检查“https://username.github.io/web”，您应该能够看到您的应用程序启动并运行。

**但我已将文件夹“web”重命名为“** [**moor-dart-example**](https://github.com/CodingWithTashi/codingwithtashi.github.io/tree/master/moor-dart-example) **”以使 url 提供更多信息。因此你可以找到我的项目托管在**

[https://codingwithtashi.github.io/moor-dart-example](https://codingwithtashi.github.io/moor-dart-example/#/)。下面的截图

![如何将 flutter web 项目部署到 GitHub](https://miro.medium.com/max/60/1*KshytiIXY70GqyY557LQNw.png?q=20)

![如何将 flutter web 项目部署到 GitHub](https://miro.medium.com/max/700/1*KshytiIXY70GqyY557LQNw.png)

就这些，谢谢大家，我希望你喜欢这个糟糕的博客。请务必鼓掌👏