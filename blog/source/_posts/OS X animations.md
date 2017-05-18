---
title: OS X animations
date: 2017-05-17 18:44:52
tags: [OSX, Animation, Layer]
---
新项目做一个背景渐变，同时图片缩放的动画，在设计老板的电脑上卡的厉害，所以展开了一系列对动画的研究。

先上脑图：
![脑图](https://raw.githubusercontent.com/melody5417/blogResources/master/OS%20X%20animations.png)
  
  从图中可以看出是不推荐使用 NSAnimationContext，它是在主线程不断调用 setFrame: 和 setFrameSize: 等实现的移动和缩放动画，显然效率低下。同时我的图片又是采用离屏渲染的，所以性能更加不好。
  
  经过这几篇文章的学习，最终采用以下步骤优化：

1. 采用layer-based 的 CoreAnimation。
2. 优化重绘策略：NSView 的property 设置为 NSViewLayerContentsRedrawOnSetNeedsDisplay
3. 图片展示不再用 NSImageView， 直接设置为 layer 的 contents。

最终实现效果老板终于满意啦，做的 [Demo](https://github.com/melody5417/Animation/tree/master)。
  