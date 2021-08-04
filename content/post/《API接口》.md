---
title: "《API接口》"
date: 2021-02-24T16:52:30+08:00
lastmod: 2021-02-24T16:52:30+08:00
draft: false
keywords: []
description: ""
tags: []
categories: []
author: ""

# You can also close(false) or open(true) something for this content.
# P.S. comment can only be closed
comment: true
toc: true
autoCollapseToc: true
postMetaInFooter: true
hiddenFromHomePage: false
# You can also define another contentCopyright. e.g. contentCopyright: "This is another copyright."
contentCopyright: false
reward: false
mathjax: false
mathjaxEnableSingleDollar: false
mathjaxEnableAutoNumber: false

# You unlisted posts you might want not want the header or footer to show
hideHeaderAndFooter: true

# You can enable or disable out-of-date content warning for individual post.
# Comment this out to use the global config.
#enableOutdatedInfoWarning: false

flowchartDiagrams:
  enable: true
  options: ""

sequenceDiagrams: 
  enable: true
  options: ""

---





今天我就来单纯做个API的“搬砖工”，方便广大开发同学，你想要的这一篇就足够了。（tips：部分接口来源于第三方，需注册会员就可以免费使用）

![img](https://luckly007.oss-cn-beijing.aliyuncs.com/images/20210224165252.jpeg)

先上一些我觉得好玩的API接口：

- [网易云音乐](https://link.zhihu.com/?target=https%3A//binaryify.github.io/NeteaseCloudMusicApi/%23/)
- [知乎专栏](https://link.zhihu.com/?target=https%3A//github.com/TonnyL/Zhihu_Zhuanlan_APIs/wiki)
- [百度音乐](https://link.zhihu.com/?target=https%3A//my.oschina.net/skiden/blog/102025)
- [豆瓣一刻](https://link.zhihu.com/?target=https%3A//github.com/ZongweiBai/DoubanOpenDaily/blob/b87473b90911e687d291e847b133b5c4c4a700a3/app/src/main/java/com/monosky/daily/constant/APIConstData.java)
- [干货集中营](https://link.zhihu.com/?target=https%3A//gank.io/api)
- [二十四节气查询](https://link.zhihu.com/?target=https%3A//www.apishop.net/%23/api/detail/%3FproductID%3D88)
- [笑话大全](https://link.zhihu.com/?target=https%3A//www.apishop.net/%23/api/detail/%3FproductID%3D122)
- [炉石传说卡牌](https://link.zhihu.com/?target=https%3A//www.apishop.net/%23/api/detail/%3FproductID%3D203)
- [考拉FM](https://link.zhihu.com/?target=https%3A//github.com/kaolafm/api)
- [蜻蜓FM](https://link.zhihu.com/?target=https%3A//open.qingting.fm/)
- [Musixmatch](https://link.zhihu.com/?target=https%3A//developer.musixmatch.com/)
- [Spotify](https://link.zhihu.com/?target=https%3A//developer.spotify.com/)
- [Disqus](https://link.zhihu.com/?target=https%3A//disqus.com/api/docs/)

**《[系统架构设计 System Design 2020 版](https://link.zhihu.com/?target=https%3A//www.jiuzhang.com/course/77/%3Futm_source%3Dsc-zhanswer-cyc0721)》**全新升级，加入通过大量调研，票选最高的系统设计和热门知识点，总共覆盖了18大系统架构设计的知识点和面试题。

现在开放**免费试听，体验《[网站系统，API设计与短网址](https://link.zhihu.com/?target=https%3A//www.jiuzhang.com/course/77/%3Futm_source%3Dsc-zhanswer-cyc0721)》**。

此外，还有许多实用的API接口：

## 平台接口：

- [阿里云市场](https://link.zhihu.com/?target=https%3A//market.aliyun.com/data)
- [聚合数据](https://link.zhihu.com/?target=https%3A//www.juhe.cn/docs)
- [阿凡达数据](https://link.zhihu.com/?target=https%3A//www.avatardata.cn/Docs)
- [API-免费API数据接口平台-京东万象](https://link.zhihu.com/?target=https%3A//wx.jdcloud.com/api)
- [小白开放平台](https://link.zhihu.com/?target=http%3A//api.yesapi.cn/docs.php)
- [进制数据](https://link.zhihu.com/?target=https%3A//www.binstd.com/api/)
- [APIShop](https://link.zhihu.com/?target=https%3A//www.apishop.net/%23/)
- [iDataAPI](https://link.zhihu.com/?target=https%3A//www.idataapi.cn/)

## 技术接口：

- [开源中国](https://link.zhihu.com/?target=https%3A//www.oschina.net/openapi)
- [V2EX](https://link.zhihu.com/?target=https%3A//www.v2ex.com/p/7v9TEc53)
- [Coding](https://link.zhihu.com/?target=https%3A//help.coding.net/docs/project/open/webhook.html)
- [CNode](https://link.zhihu.com/?target=https%3A//cnodejs.org/api)
- [diycode](https://link.zhihu.com/?target=https%3A//www.diycode.cc/api)
- [玩Android](https://link.zhihu.com/?target=https%3A//www.wanandroid.com/blog/show/2)

## 地图接口：

- [高德地图](https://link.zhihu.com/?target=https%3A//lbs.amap.com/)
- [百度地图](https://link.zhihu.com/?target=http%3A//lbsyun.baidu.com/)
- [腾讯地图](https://link.zhihu.com/?target=https%3A//lbs.qq.com/)
- [搜狗地图](https://link.zhihu.com/?target=http%3A//map.sogou.com/api/)

## 新闻信息：

- [纽约时报](https://link.zhihu.com/?target=https%3A//developer.nytimes.com/)
- [今日美国](https://link.zhihu.com/?target=https%3A//developer.usatoday.com/docs/)
- [BrewereyDB](https://link.zhihu.com/?target=https%3A//www.brewerydb.com/developers)
- [Diigo](https://link.zhihu.com/?target=https%3A//www.diigo.com/api_dev)
- [feedly](https://link.zhihu.com/?target=https%3A//developer.feedly.com/)
- [Genius](https://link.zhihu.com/?target=https%3A//docs.genius.com/)
- [goodreads](https://link.zhihu.com/?target=https%3A//www.goodreads.com/api)
- [HackerNews](https://link.zhihu.com/?target=https%3A//github.com/HackerNews/API)
- [Inoreader](https://link.zhihu.com/?target=https%3A//www.inoreader.com/developers/)
- [Instapaper](https://link.zhihu.com/?target=https%3A//www.instapaper.com/api)
- [Narro](https://link.zhihu.com/?target=https%3A//docs.narro.co/%23introduction)
- [Newsblur](https://link.zhihu.com/?target=https%3A//newsblur.com/api)
- [NRP](https://link.zhihu.com/?target=http%3A//www.npr.org/api/index)
- [Pinboard](https://link.zhihu.com/?target=https%3A//pinboard.in/api)
- [Pocket](https://link.zhihu.com/?target=https%3A//getpocket.com/developer/)
- [Product Hunt](https://link.zhihu.com/?target=https%3A//api.producthunt.com/v1/docs)

## 健身：

- [Adidas AG](https://link.zhihu.com/?target=https%3A//developers.adidas.com/services)
- [Fitbit](https://link.zhihu.com/?target=https%3A//dev.fitbit.com/)
- [Jawbone UP](https://link.zhihu.com/?target=https%3A//jawbone.com/up/developer)

## 天气查询接口：

- [免费天气查询](https://link.zhihu.com/?target=https%3A//www.kancloud.cn/ccjin/yingq/603579)
- [丫丫天气](https://link.zhihu.com/?target=http%3A//www.yytianqi.com/)
- [彩云天气](https://link.zhihu.com/?target=http%3A//wiki.swarma.net/index.php/%E5%BD%A9%E4%BA%91%E5%A4%A9%E6%B0%94API/v2)
- [心知天气](https://link.zhihu.com/?target=https%3A//www.seniverse.com/)
- [和风天气](https://link.zhihu.com/?target=https%3A//www.heweather.com/)

## 快递查询接口：

- [快递100](https://link.zhihu.com/?target=https%3A//www.kuaidi100.com/openapi/applyapi.shtml)
- [快递网](https://link.zhihu.com/?target=http%3A//www.kuaidi.com/openapi.html)
- [TrackingMore](https://link.zhihu.com/?target=https%3A//www.51tracking.com/api-index-cn)

## AI：

- [百度AI市场](https://link.zhihu.com/?target=https%3A//aim.baidu.com/)
- [智能机器人](https://link.zhihu.com/?target=https%3A//www.sojson.com/api/semantic.html)

## 笔记：

- [OneNote](https://link.zhihu.com/?target=https%3A//docs.microsoft.com/en-us/previous-versions/office/office-365-api/%3Fredirectedfrom%3DMSDN)
- [有道云笔记](https://link.zhihu.com/?target=http%3A//note.youdao.com/open/)
- [印象笔记](https://link.zhihu.com/?target=https%3A//dev.yinxiang.com/)
- [为知笔记](https://link.zhihu.com/?target=https%3A//www.wiz.cn/manual/plugin/)