# AtoZ CSS 截屏:关键帧动画

> 原文：<https://www.sitepoint.com/atoz-css-screencast-keyframe/>

这个截屏是我们 AtoZ CSS 系列的一部分。你可以在这里找到系列[的其他条目。](https://www.sitepoint.com/blog/)

## 副本

动画曾经是 JavaScript 的领域。

现在，在现代的浏览器中，我们可以使用 CSS 动画元素。

`@keyframes`块和`animation`属性允许我们指定在什么时候激活什么。

在这一集中，我们将看看适合 CSS 的动画种类，指定一系列`@keyframes`的概念，以及最后这些关键帧与`animation`设置相结合的方式，以使页面变得生动。

### CSS 中的动画

由于 CSS 动画没有最深的浏览器支持，它们最适合视觉天赋，而不是页面内容或设计的关键部分。

动画可以运行一次或多次，也可以无限循环。也可以向同一个元素添加多个动画。

当页面加载时，动画可以在 CSS 中触发，在一段延迟后或通过某种基于状态的改变，如`:hover`、`:focus`或`:active`，我们在*、[第 8 集:](https://www.sitepoint.com/atoz-css-screencast-hover-pseudo-class)、`Hover`、*中看到过。

CSS 动画也可以通过切换`animation-play-state`属性在 Javascript 中开始和停止。稍后我们将看看其他动画属性和语法。

### 关键帧

为了激活一个元素(或元素的选择)，我们需要指定一系列关键帧。

关键帧动画最基本的形式是在一定时间内，在开始时使用一组样式，在结束时使用另一组样式。

在动画制作过程中，浏览器会自动计算关键帧之间的样式，这个过程称为`tweening`。

每个关键帧被定义为 CSS 属性的样式块，该样式块将应用于使用该关键帧的任何元素。

```
@keyframes moveLeft {
  from {left: 0;}
  to   {left: 500px;}
}
```

给定这组关键帧，动画的起始关键帧将如下所示；结局会是这样的。

```
@keyframes grow {
  0%   {font-size: 20px;}
  75%  {font-size: 100px;}
  100% {font-size: 10px;}
}
```

可以使用百分比语法指定附加关键帧。如果动画持续时间是 10 秒，在第一个 7.5 秒，元素的`font-size`将增长到`100px`，然后在下一个 2.5 秒，它将收缩回`10px`。您可以为每个关键帧指定任意多个属性。

### 动画

当`@keyframes`被定义后，它们就可以和`animation-name`属性一起使用了。

有一系列的`animation`属性可以根据需要配置您的动画:

*   `animation-name`指定要使用的`@keyframes`块
*   `animation-duration`指定动画持续的时间
*   `animation-delay`指定动画开始前的任何延迟
*   `animation-iteration-count`指定重复的次数
*   `animation-direction`指定方向；动画可以向前播放(正常)，反向播放或前后交替播放
*   `animation-play-state`允许暂停和继续播放动画
*   `animation-timing-function`决定动画如何在关键帧之间播放的加速曲线
*   `animation-fill-mode`决定动画前后如何应用样式

这 8 个属性可以组合成一个简写的`animation`属性，如下所示:

```
.box {
  animation: name duration delay count direction play-state timing fill-mode;
}
```

一个动画至少可见一次的唯一必要属性是:`animation-name`和`animation-duration`。

### 弹跳球动画

让我们看一个实际的例子。

我们可以使用一个相等的`width`和`height`框创建一个球，并将`border-radius`设置为`100%`。我们可以通过精确定位球并随着时间的推移制作`top`或`bottom`值的动画来使球上下弹跳。

我们可以通过在动画底部挤压球，然后让它以稍微慢一点的速度向上行进，来赋予球更多的真实感。

```
.ball {
  position: absolute;
  width: 100px;
  height: 100px;
  border-radius: 100%;
  animation: bounce 3s linear infinite;
}
@keyframes bounce {
  0% {bottom: 100%;}
  25% {
    bottom: 0;
    width: 100px;
    height: 100px;
  }
  30% {
    bottom: 0;
    height: 50px;
    width: 110px;
  }
  35% {
    bottom: 0;
    width: 100px;
    height: 100px;
  }
  70% {
    bottom: 100%;
  }
  100% {
    bottom: 100%;
  }
}
```

我们可以通过添加第二个动画来激活球的`left`属性，使球在屏幕上移动。它们可以用逗号分隔，所以它们都应用于同一个元素。

为了让球看起来好像在屏幕上缓慢反弹，我们可以增加第二个动画的持续时间。

```
@keyframes moveLeft {
  from {left: 0;}
  to   {left: 100%;}
}
.ball {
  animation: bounce 3s linear, moveLeft 12s linear infinite;
}
```

### 浏览器支持

IE9 或更低版本或 Opera Mini 不支持 CSS 动画。在 IE10、IE11 和 Firefox 中，`@keyframes`和`animation`属性没有前缀，但是`-webkit`前缀在其他地方都是需要的。

请关注我们即将发布的快速技巧文章！

## 分享这篇文章