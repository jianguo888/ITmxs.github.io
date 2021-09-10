---
title: "Python学习要读的几篇文章"
date: 2021-09-06T10:43:57+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["go"]
categories: ["go"]
---

**[1. PEP8 Style Guide for Python Code](http://www.python.org/dev/peps/pep-0008/)**

排第一的当属Python的编码规范PEP8，这个就不用我多说吧，无论是初学还是高手PEP8都是必须烂熟于胸的。在遵循里面讲述的规则的同时，大家也要记住两句比较特殊的话，不过这两句话千万别滥用了:)

```
    Two good reasons to break a particular rule:     (1) When applying the rule would make the code less readable, even for
        someone who is used to reading code that follows the rules.     (2) To be consistent with surrounding code that also breaks it (maybe for
        historic reasons) -- although this is also an opportunity to clean up
        someone else's mess (in true XP style).
```

如果要写Python的C扩展库，你还必须好好学习下[PEP7](http://www.python.org/dev/peps/pep-0007/)。

**[2. Code Like a Pythonista: Idiomatic Python](http://python.net/~goodger/projects/pycon/2007/idiomatic/handout.html)**

文如其名，本文篇幅较长，但非常非常实用。本文介绍了一系列Python约定俗成的用法和技巧，学习本文能让你写出来的Python代码马上上一个档次，让你成为一个真正的Pythoneer。

**[3. PEP318 Decorators for Functions and Methods](http://www.python.org/dev/peps/pep-0318/)**

在Python的世界里decorator是非常常见的，常见的比如auth、cache都会提供decorator，decorator所实现的功能就是修改紧接 decorator之后定义的函数和方法，你可以根据自己的需要给一个已有的函数和方法添油加醋。

**[4. Python regular expression documentation](http://docs.python.org/library/re.html)**

掌握正则表达式是编程人员的必备技能，这是re模块的官方文档，用到正则表达式的时候要常来翻翻。Python的正则表达式跟别的语言函数用法有点

不一样，因此也有人抱怨说Python的re模块不好用，文章开头关于正则表达式规则部分可以当正则表达式的入门教材，如果你还不知道正则表达式是个什么

东西的话。个人觉得Python的re模块非常强大的，举个有点意思的例子，比如VERBOSE的正则表达式还支持在正则表达式中间加注释说明正则表达式
的具体含义，因为正则表达式通常都象天书一样比较难懂，因此这个功能显得非常有用。

```
a = re.compile(r"""\d +  # the integral part
                   \.    # the decimal point
                   \d *  # some fractional digits""", re.X)
```

**[5. PEP333 Python Web Server Gateway Interface v1.0](http://www.python.org/dev/peps/pep-0333/)**

如果你要用Python来做Web相关的开发(对大多说人来说主要就是指Web运用)WSGI是必读的，WSGI是Python web server、application、middleware之间的官方规范标准，现在主流的Python web server和framework都肯定支持WSGI的。只有了解了WSGI你才能更好的理解Python的web framework的工作机制、middleware为啥可以串联起来、web server是怎么跟web application交互的。WSGI有两个为人诟病的地方，一是不支持异步，Tornado是一个异步的Web server，但是若要在上面运行WSGI的framework如Django，则Tornado的异步特性完全用不上；二是WSGI v1.0不支持Python3.x，为了支持Python3.x，有两篇还在Draft状态的PEP文档[PEP3333](http://www.python.org/dev/peps/pep-3333/)和[PEP444](http://www.python.org/dev/peps/pep-0444/)可供参考。
