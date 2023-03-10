# 玩火:有机 CSS3 动画

> 原文：<https://www.sitepoint.com/playing-with-fire-organic-css3-animation/>

[![Mythbusters testing the chinese water torture.](img/28d26c347940422f58061fca3df1b777.png)](http://www.youtube.com/watch?v=Dj4uWXxGxQk "Youtube link to Mythbusters Episode")

2008 年流言终结者测试中国水刑。

几年前，我看了一集很棒的《流言终结者》，他们测试了中国水刑的想法。亚当、卡莉、斯科特和托里一个接一个地被牢牢地固定住，然后被缓慢而有节奏的水滴打在额头上。

一个接一个，在不到一个小时的时间里，每个人都崩溃了——在卡莉的情况下，到了流泪的地步。

这不是观看起来最舒服的事情，但它提供了一个明显的启示:虽然一些节奏*可以让人放松，但严格的重复会很快让我们疯狂。*

 *当然，作为网络人，我们已经直观地知道了这一点！

谁没有体验过不停旋转的 logo 的网站？谁没有被那个在“施工中”标志后面辛勤工作的小建筑工人搞得心烦意乱呢？

这提出了一个显而易见的问题:有没有可能在不引入这些折磨人的循环的情况下使用动画？

让我们看看我们能做什么。

### CSS3 动画基础

如果你还没有用过 CSS3 动画，这里有一个速成班。如果您已经对 CSS3 关键帧有所了解，请跳到“使用 CSS3 精灵”

为了便于阅读，我将在整篇文章中使用官方的“无前缀”W3C 语法(即@keyframe)，但是您需要使用特定于浏览器的前缀(即@-webkit-keyframe、@-moz-keyframe、@-ms-keyframe 等)。)来看看它在现实世界浏览器中的工作情况。

CSS3 动画背后的核心概念是让你在 CSS 属性之间转换。像所有动画一样，CSS3 需要一个有开始和结束的时间线，分别设置为 0%和 100%。最简单的动画时间线的代码如下所示:

```
@keyframes mysimpleanimation {
    0% {background-color:#f00;}
    100% {background-color:#00f;}
}
```

正如你可能猜到的那样，上面的动画是为了把红色背景变成蓝色而设计的。然而，就其本身而言，这段代码不会做任何事情，直到我们将它应用于屏幕上的 HTML 对象。我们还需要设置动画的时间长度(秒)。

下面是如何在你的 CSS 中包含一个时间长度:

```
#example {
  ...
  animation: mysimpleanimation 4s infinite;
}
```

下面是一个简单的例子。

我们可以自由地沿着时间轴创建任意多的点，例如，我们可以通过添加以下行，在中间点将动画更改为绿色(#0f0):

```
@keyframes mysimpleanimation {
    0% {background-color:#f00}
50% {background-color:#0f0}
    100% {background-color:#00f}
}
```

还有许多其他有据可查的属性和设置，但是我们已经介绍了您现在需要的基础知识。

### 使用 CSS3 精灵

**注意:**对于本文中的所有演示，我将使用 [Lea Verou 的超级可爱的 dabble et web app](http://dabblet.com/ "Dabblet")。除了看起来很甜美和直接连接到 Github 之外，Dabblet 还可以让你在这里摆弄 CSS 并实时看到结果。Dabblet 的另一个伟大的特性是，它让你编写简单的、无前缀的 CSS3，同时在后台神奇地添加特定于浏览器的代码(使用 Lea 的“[-无前缀](http://leaverou.github.com/prefixfree/)”)。

这对于保持代码示例的易读性来说是极好的，但是请注意，您将需要添加"-moz "、" webkit "和"-ms "前缀来使代码在 Dabblet 之外工作。

因此，尽管只有一个很好的技巧——沿时间轴在 CSS 属性之间转换——CSS3 关键帧为您提供了许多动画的基本构建块，包括:

*   挤压和拉伸(在 CSS 中:宽度，高度)
*   旋转(变换:旋转)
*   空间位置(左、右、上、下)
*   颜色和不透明度变化(颜色、背景色、不透明度)

[![File a Bugs report?](img/4e2f54c789b80b3296c78ebf8e0e5189.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/bugs.gif)

传统的 cel 动画(或者可能是第一个 bug 报告？#dadjoke)

这涵盖了很多潜在的动画场景，从弹跳球到旋转轮胎到发射火箭。

然而，例如，如何操纵 CSS 属性来生成:

*   蠕动的蛇
*   扑翼
*   旋转的地球
*   跑步的人

经典的 cel(或 sprite)动画是这个问题的一个常见答案。我们可能都见过老迪士尼或华纳兄弟手绘的“卡通细胞”时间轴中的每个时刻都被绘制为独立的图像。然后，每个单元连续快速显示，以产生运动的错觉。

那么，我们如何用 CSS3 做到这一点呢？

让我们从一个简单的 cel 动画开始，我们可以把它发展成更复杂的东西。我从单身开始。png 显示三帧手绘火焰。让这个图像无缝循环不会很难。

![3 frame flame](img/dcce5ee425bfe2b1e5284ac26d616e57.png)

我们的 3 帧火焰细胞

每个火焰格子高 136 像素，宽 100 像素。

### 设置

这是我开始使用的基本 HTML 框架:

```
​
```

我使用了一个容器

(#logfire)来保存背景图片，一个占位符(#flamegroup)来保存我们的火焰。在占位符中，我嵌套了第三个。火焰)那将成为我们的火焰。

我已经将火焰图形嵌入到背景图像中，但是我们裁剪掉了图像的大部分，只让左边的火焰显示出来。

```
.flame {
     width:100px;
     height:136px;
     background:url(flame.png) left top no-repeat}
```

[![The basic scene](img/6d9324ea4e9b8a3c05eef6c03af9e29a.png)](https://www.sitepoint.com/wp-content/uploads/2012/02/scene1.jpg)

基本静态场景

这是我们的起始代码。

### 设定我们的时间表

要制作一个无缝循环，我们需要创建一个 4 帧循环，也就是说，我们将再次显示第 1、2、3 和 2 帧，然后重复。我们可以沿着我们不可见的时间轴将该序列表示为位置 0%、25%、50%、75%和 100%(即回到第 1 帧)。在每个阶段，我们在下一帧重新定位背景，所以我们的 CSS 可能看起来像这样:

```
@keyframes flicker { /* flame pulses */
  0% {
    background-position:0px 0px;
  }
  25% {
    background-position:-100px 0px;
  }
  50% {
    background-position:-200px 0px;
  }
  75% {
    background-position:-100px 0px;
  }
  100% {
    background-position:0px 0px;
  }
}​
```

然后，我们可以将我们的“闪烁”动画附加到我们的火焰类，并将其设置为每 1.1 秒循环一次:

```
.flame {
  ...
  animation: flicker 1.1s infinite;
}
```

太棒了。[除了 ***看起来不太对劲的部分***](http://dabblet.com/gist/1849877 "Example 2") 。

如上例所示，它将 ***和*** 很好地动画化，优雅地在帧间滑动。就像你以前在书页的角落里制作的小翻页动画一样，神奇之处在于快速地将每个新帧捕捉到位，以至于你看不到中间的状态。

我们新的锯齿状时间线现在看起来像这样:

```
@keyframes flicker { /* flame pulses */
  0% {
    background-position:0px 0px;
  }
  25% {
    background-position:0px 0px;
  }
  25.1% {
    background-position:-100px 0px;
  }
  50% {
    background-position:-100px 0px;
  }
  50.1% {
    background-position:-200px 0px;
  }
  75% {
    background-position:-200px 0px;
  }
  75.1% {
    background-position:-100px 0px;
  }
  100% {
    background-position:-100px 0px;
  }
}​
```

你可以看到，我们的第一个细胞从 0%-25%保持不动。然后突然在时间轴上的 25.1%，我们将背景向左旋转 100 像素。再次，我们把它留在那里，直到 50%的标记，然后再把它留在 51.1%。

我想你明白了。

如果我们把 CSS 插入到我们的例子中，我们现在得到一个令人愉快的摆动的火焰。

### 添加一个脉冲

火焰不只是移动，它们的强度也有轻微的变化。既然我们已经设置好了时间线，为什么不稍微调整一下不透明度，给它一个微妙的脉动呢？

```
@keyframes flicker { /* flame pulses */
    0% {
        background-position:0px 0px;
        opacity:.8
}
    25% {
        background-position:0px 0px;
}
    25.1% {
        background-position:-100px 0px;
}
    50% {
        background-position:-100px 0px;
        opacity:.4
}
    50.1% {
        background-position:-200px 0px;
}
    75% {
        background-position:-200px 0px;

}
    75.1% {
        background-position:-100px 0px;
}
    100% {
        background-position:-100px 0px;
        opacity:.8
}
}
```

这是结果。

一个不错的结果，但让我们面对它:我们可以得到一个很好的动画非常接近这一点。gif 文件。让我们把事情推进一点。

### 煽风点火

回到我们最初的 HTML，我们将添加更多的火焰元素——幸运的是我们有一个方便的容器

，嗯？

让我们向我们的“flamegroup”添加两个带有“flame”类的元素我们还会给每个

一个自己的 ID，这样我们就可以分别定位它们。 `我们现在有三个火焰元素，但是因为它们在完美的和谐中跳舞，它们看起来仍然像一个单独的火焰。让我们从“火焰”类中删除动画，并将其添加到我们的新 IDs 中。

```
#fl1 {
   -webkit-animation: flicker .7s infinite;
}
#fl2 {
   -webkit-animation: flicker .7s infinite;
}
#fl3 {
   -webkit-animation: flicker .7s infinite;
}
```

现在我们可以自由调用同一个动画(例如“闪烁”)，但是我们会给每个动画一个不同的时间来保持有趣。重复我们在蝉原理文章中使用的[质数思想，我将设置火焰元素分别在 0.7、1.1 和 1.3 秒内循环(是的，我知道它们不是质数)。](https://www.sitepoint.com/the-cicada-principle-and-why-it-matters-to-web-designers/)

最后，我不会将它们精确地叠放在一起，而是将每个火焰从它的兄弟姐妹稍微偏移(10-20px)。这主要是为了让我们可以看到边缘的融合和互动。

这是 CSS:

```
#fl1 {
animation: flicker 1.3s infinite;
top:-10px; /* offset */
left:10px /* offset */
}
#fl2 {
animation: flicker .7s infinite;
top:10px; /* offset */
left:20px /* offset */
}
#fl3 {
animation: flicker 1.1s infinite;
}
```

[这是最后一次演示](http://dabblet.com/gist/1848758)。

[![A screenshot — although the static image doesn't do it justice.](img/db37596ae4fd775caee6c7aad7282896.png)](http://dabblet.com/gist/1848758)

截图——尽管静态图像有点违背了目的。

我们的火焰以一种柔和的、不那么“GIF-loopy”的方式跳跃、舞蹈和编织。有时候你认为你已经确定了模式，但是它似乎已经改变了。Dabblet 允许你修补，所以你可以随意调整时间。显然，循环越慢，精确模式重复之间的时间越长。

有几点需要注意:

*   正如我前面提到的，dabble et 示例使用了"[-无前缀的](http://leaverou.github.com/prefixfree/)"来让我们编写干净的、无前缀的 CSS3，但是您需要添加"-moz，" "-webkit "和"-ms "前缀来让代码在 dabble et 之外工作。
*   我想让这个例子相对简单一些，但是我们可以通过设置背景高度为 100%，然后设置火焰高度的动画来增加一点趣味。这将使我们的火焰跳跃和俯冲更加有力。
*   显然，旧的浏览器不支持 CSS3 动画，但是[Modernizr.com](http://www.modernizr.com/)允许你针对这些浏览器进行回退。对于旧的浏览器，静态渲染火焰似乎是一个完全可以接受的选择。事实上，很难想象一个 IE8 用户会意识到其他用户正在看动画。
*   您有时可能会在动画中看到轻微的“抖动”或随机闪烁。这似乎是一个浏览器缓冲区溢出错误，但迄今为止我还没有找到消除它的方法，尽管较慢的计算机似乎使它变得更糟。

我们还看到一些证据表明，在*一些*较大的 CSS3 动画中，我们的处理器周期会出现峰值。这似乎与特定类型的动画有关，但我还没有隔离出关键的问题领域。可以说，如果你听到你的 CPU 风扇超速运转，你可能也遇到了同样的问题。

### 总结…

我认为这个编织动画计时的想法有一些有趣的潜力，所以在接下来的几周里，请关注这个空间进行更多的实验。微妙的阳光、水和云的效果浮现在脑海中。

你觉得怎么样？

如果你有任何自己的想法，制作一个 Dabblet 并在这里分享。

**更新:2012 年 3 月 1 日**

阅读 CSS 动画规范，我发现有一个更好的方法来控制精灵切换。@keyframe 语法有一个很少使用的属性叫做“step ”,它允许你控制浏览器生成多少个“中间”动画状态。它在未固定的状态下看起来是这样的:

```
animation: animation_name 1.1s steps(3) infinite;
```

这段代码将在我们在@keyframe 代码中手动设置的步骤之间生成三个阶段。当使用精灵时，我们不希望在改变精灵之间显示任何东西，所以我们只设置我们的步骤为 1。

```
animation: flicker 1.1s steps(1) infinite;
```

这使我们能够大大简化我们的火焰计时循环。这里有一个更新的 Dabblet，显示它正在工作。`* 

## *`分享这篇文章`*