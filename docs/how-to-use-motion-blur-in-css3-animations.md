# 如何在 CSS3 动画中使用运动模糊

> 原文：<https://www.sitepoint.com/how-to-use-motion-blur-in-css3-animations/>

CSS3 [过渡](/css3-transitions-101/)和[动画](/css3-animations-101/)非常有用，但它们往往过于完美！

你有没有想过为什么电脑游戏以每秒 25-30 帧的速度交错播放，而电影却以同样的速度自然播放？典型的摄像机在 0.04 秒内捕捉每一帧；任何移动较快的东西都会显得有些模糊。然而，一个完美地创建了没有运动模糊的每一帧的游戏看起来会有点奇怪。

不幸的是，在 HTML 元素上创建运动模糊是不可能的。 [CSS3 滤镜效果草案规范](https://dvcs.w3.org/hg/FXTF/raw-file/tip/filters/index.html)可能最终会有所帮助，但它只有实验支持，模糊效果是无方向性的。幸运的是，我们有另外两个标准的 CSS3 属性可以提供帮助:`text-shadow`和`box-shadow`。

[**查看 CSS3 运动模糊演示……**](http://cssdeck.com/labs/motion-blur)

如果一个对象向右移动，我们可以应用一个`text-shadow`，它使用与文本相似的颜色向左延伸。在上面的例子中，文本开始和结束都没有阴影。但是，在动画进行到一半时(最快的点)，它被设置为:

```
text-shadow: -5px 0 5px rgba(255,255,255,1);
```

![motion blur with text shadow](img/1c75eca6bbbcc4464a2c0393860358fd.png)

这很微妙，但应用较重的阴影不一定看起来有效。

同样，我们也可以使用与文本背景颜色相似的`box-shadow`。在中途点，它被设置为:

```
box-shadow: -15px 0 10px -5px rgba(200,0,0,0.5);
```

这定义了:

*   -15px 的 x 偏移
*   模糊半径为 10px，以及
*   -5px 的传播距离。

价差很重要。没有它，`box-shadow`将明显比盒子本身大。在我的演示中，我发现大约一半的模糊半径效果很好，但你可能需要根据元素的大小和方向来调整它。

![motion blur with box shadow](img/a9dadb612efaab04d495f1fb38702c64.png)

完整的关键帧定义在动画的前 50%从左向右移动元素，然后在后半部分从右向左移动元素。因此，最大`text-shadow`和`box-shadow`设置相应地被定义为 25%和 75%:

```
@keyframes motionblur {
  0%
  {
    left: 0;
    text-shadow: 0 0 0 rgba(255,255,255,0);
    box-shadow: 0 0 0 rgba(200,0,0,0);
  }
  5%
  {
    left: 0;
    transform-origin: 0 0;
    transform: scaleX(0.85);
  }
  25%
  {
    text-shadow: -5px 0 5px rgba(255,255,255,1);
    box-shadow: -15px 0 10px -5px rgba(200,0,0,0.5);
    transform: scaleX(1.1) skewX(-4deg);
  }
  50%
  {
    left: 300px;
    text-shadow: 0 0 0 rgba(255,255,255,0);
    box-shadow: 0 0 0 rgba(200,0,0,0);
    transform: scaleX(1) skewX(0deg);
  }
  55%
  {
    left: 300px;
    transform-origin: 100% 0;
    transform: scaleX(0.85);
  }
  75%
  {
    text-shadow: 5px 0 5px rgba(255,255,255,1);
    box-shadow: 15px 0 10px -5px rgba(200,0,0,0.5);
    transform: scaleX(1.1) skewX(4deg);
  }
  100%
  {
    left: 0px;
    text-shadow: 0 0 0 rgba(255,255,255,0);
    box-shadow: 0 0 0 rgba(200,0,0,0);
    transform: scaleX(1) skewX(0deg);
  }
}
```

还添加了一些 2D 变换来增强动画效果。这个盒子是:

*   开始时被挤压，所以看起来好像要猛扑过去
*   在动画中点拉长和倾斜，以产生加速度的效果
*   最终恢复到正常尺寸。

你也应该注意到我使用了一个相当慢的动画持续时间，所以你可以看到发生了什么。

[**查看 CSS3 运动模糊演示……**](http://cssdeck.com/labs/motion-blur)

该效果适用于所有最新的浏览器:

*   IE10
*   火狐浏览器
*   Chrome、Safari 和 Opera 15——尽管你会要求所有的`animation`和`transform`属性都有-webkit 前缀。

Opera 12 出了故障，但是，咳咳，我们会掩饰的…

IE10 中唯一有点奇怪的行为出现了。浏览器不会显示阴影，除非在开始和结束处明确定义了默认值，即

```
text-shadow: 0 0 0 rgba(255,255,255,0);
box-shadow: 0 0 0 rgba(200,0,0,0);
```

换句话说，IE10 从`box-shadow: none`到`box-shadow: *something*`不会动画。我怀疑它会影响其他 CSS 属性，所以它值得在您的古怪浏览器问题箱中归档。

如果你在其他地方使用这项技术，请[把你的网址](http://twitter.com/craigbuckler)发给我！

## 分享这篇文章