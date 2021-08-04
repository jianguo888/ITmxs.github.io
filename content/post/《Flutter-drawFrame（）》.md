---
title: "《Flutter DrawFrame（）》"
subtitle: ""
date: 2021-07-31T21:11:19+08:00
lastmod: 2021-07-31T21:11:19+08:00
draft: false
author: ""
authorLink: ""
description: ""
images: []
tags: ["flutter"]
categories: ["flutter"]

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false

fontawesome: true
linkToMarkdown: true
rssFullText: false


featuredImage: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"
featuredImagePreview: "https://luckly007.oss-cn-beijing.aliyuncs.com/img/90c6cc12-742e-4c9f-b318-b912f163b8d0.png"

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



> 本文主要介绍`RendererBinding.drawFrame()` 负责生成帧。这个方法对于理解 Flutter 渲染过程非常重要。简单了解下。

<!--more-->





`RendererBinding.drawFrame()` 由 `SchedulerBinding.handleDrawFrame()` 方法调用，而后者又自动被引擎调用(如果引擎需要布局和绘制一帧的话)。

`RendererBinding.drawFrame()` 的源码如下：

```
void drawFrame() {
    assert(renderView != null);
    // 布局
    pipelineOwner.flushLayout();
    // 检查是否需要重绘
    pipelineOwner.flushCompositingBits();
    // 重绘
    pipelineOwner.flushPaint();
    renderView.compositeFrame(); // this sends the bits to the GPU
    pipelineOwner.flushSemantics(); // this also sends the semantics to the OS.
}
```

`flushPaint()` 调用 `PaintingContext.repaintCompositedChild(node)` 方法，后者是真正的绘制逻辑。

每帧由以下几个阶段组成：

1. 动画阶段 - `SchedulerBinding.handleBeginFrame` (由 `Window.onBeginFrame` 注册)，按注册顺序调用所有的临时帧回调 (由 `scheduleFrameCallback` 注册)。这些回调包括所有用于驱动 `AnimationController` 对象的 `Ticker` 实例，即该时间点的所有活跃 `Animation` 对象。
2. Microtasks - `handleBeginFrame` 返回后，由临时帧回调注册的方法调度的 microtasks 开始运行。之后，由 `Window.onDrawFrame` 注册的 `handleDrawFrame` 回调开始执行，它调用所有的持久帧回调。其中最重要的是 `drawFrame` 方法，它的处理过程如下
3. 布局阶段 - 对系统中的所有标记为脏的 `RenderObject` (如何将对象标脏以便布局，可参考 `RenderObject.markNeedsLayout`) 进行布局
4. compositing bits 阶段 - 更新所有标脏 `RenderObject` 中的 compositing bits。具体参考 `RenderObject.markNeedsCompositingBitsUpdate`
5. 绘制阶段 - 系统中的所有标脏 `RenderObject` 被重绘。这个过程生成 `Layer` 树。如何将对象标脏以便绘制，可参考 `RenderObject.markNeedsPaint`
6. 合成阶段 - 将 layer tree 转换成 `Scene` 并发送到 GPU
7. semantics 阶段 - 系统中的所有标脏 `RenderObject` 的 semantics 被更新。具体见 `RenderObject.semanticsAnnotator`。这个过程生成 `SemanticsNode` 树。如何将对象标脏以用于 semantics，可参考 `RenderObject.markNeedsSemanticsUpdate`
8. 步骤 3-7 的细节请参考 `PipelineOwner`
9. 结束阶段 - `drawFrame` 返回后，`handleDrawFrame` 调用 post-frame 回调。(由 `addPostFrameCallback` 注册)

注意：某些绑定会向上述过程添加额外步骤，比如，`WidgetsBinding` 会添加 `WidgetsBinding.drawFrame`。