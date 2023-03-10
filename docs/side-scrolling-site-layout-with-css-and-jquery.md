# 横向思考:用 CSS 和 jQuery 构建一个侧滚式站点布局

> 原文：<https://www.sitepoint.com/side-scrolling-site-layout-with-css-and-jquery/>

自从我看到泰勒·芬克的[Sursly.com](http://sursly.com/)后，我就一直在到处寻找一个借口来尝试像他那样的平滑滚动的水平布局。这绝不是一种新趋势，但它是我们中很少有人有机会使用的一种趋势，而且它仍然很不寻常，当你看到它时，你真的会注意到它。

横向风格非常适合带有一点“附庸风雅”感觉的设计:小册子风格的网站、画廊、文件夹，甚至可能是利用有趣的信息图形或图表的网站。成功的横向布局通常使用动画和图形来突出他们的横向移动——大量的这些网站使用彩色图像和漂亮的大背景来展示这一点。不寻常也意味着不熟悉，所以一个设计良好的水平布局也将很好地利用导航元素，如菜单、箭头和按钮来克服任何可用性障碍。

至于进入这些网站的内容——事实证明这其实很简单。让我们看看如何使用一点简单的 CSS 和 jQuery 来构建我们自己的 groovy、scrolly、sideways 网站。

## 计划好了

首先，我们需要弄清楚这个站点发生了什么。想象一下，我们正在为一个销售时尚鞋子的虚拟实体商店构建一个简单的 brochureware 站点。这里传达的信息很少:

*   一小段介绍性的文字
*   时事通讯注册
*   方向和联系信息
*   联系方式

时尚是一个非常时髦的行业，所以不寻常的水平布局非常适合这类网站。如果你能把四个项目中的每一个想象成一个面板，那么你可以把它们排成一行。一次只能看到一个面板，就好像它们是独立的页面一样，这意味着我们的布局的整体宽度需要比浏览器窗口(我们的视口)宽得多。在下图中，我们的视口由第一个面板周围的红色方块表示，我们的`body`元素的总宽度由蓝色方块表示:

![Panels, viewport, and the body element](img/49825751b841eb5c8d6f72bb287685e0.png)

这应该很容易编码！

## 添加标记和样式

为了标记这一点，我们可以在一个`body`中使用四个小的`div`元素。此标记可能类似于以下示例:

```
<body> <div id="home" class="panel"> ... </div> <div id="newsletter" class="panel"> ... </div> <div id="directions" class="panel"> ... </div> <div id="contact" class="panel"> ... </div> </div> </body> 
```

为了实现每个面板都是独立“页面”的效果，也为了给我们的动画留出一些空间，我们需要每个面板占据比我们的视口大得多的空间——让我们为实际内容留出 960 像素，为额外空间留出 1040 像素，使每个面板总共占据 2000 像素的宽度。这里有一个小小的 CSS 来帮助我们:

```
body { width: 8000px; } .panel { width: 930px; float: left; padding-left: 30px; padding-right: 1040px; } 
```

你可以在[演示 1](https://s3.sitepoint.com/examples/sidescroll/demo1.html) 中看到这是什么样子，其中也包括一些内容和阴影。除非你有一个免费的宽屏显示器，否则当你从一边滚动到另一边时，你应该一次只能看到一页。太棒了。

## 添加导航

到目前为止，这一切都很好，但是有没有更简单的方法来移动页面呢？让我们构建一个包含四个链接的菜单:

```
<ul id="banner"> <li> <a href="#home">Home</a> </li> <li> <a href="#newsletter">Newsletter</a> </li> <li> <a href="#directions">Directions &amp; Opening Hours</a> </li> <li> <a href="#contact">Contact us</a> </li> </ul> 
```

当然，链接的目标与`div`的 id 相匹配，所以我们知道点击每一个都会让我们直接跳到页面上的相关锚。然而，只要我们点击一个，我们的菜单就消失了——它回到了`body`元素的左上方，我们再也看不到它了。如果我们使用一个固定的位置将菜单附加到页面的顶部，我们甚至可以确保当我们四处缩放时，它不会移动。一个固定位置的项目被从布局的整体流程中移除——我们需要在这些面板的顶部增加一些空间来留出空间。现在，让我们让菜单看起来更像一个菜单:

```
.panel { ... margin-top: 45px; } ul#banner { position: fixed; line-height: 45px; margin: 0 30px; padding: 0; } ul#banner li { display: inline; } 
```

我们通过设置 45 像素的行高来为菜单留出空间，并在面板上设置匹配的顶部边距，以确保它们保持清晰。你可以在[演示 2](https://s3.sitepoint.com/examples/sidescroll/demo2.html) 中看到结果，你可以点击所有四个链接并在面板之间弹跳。

## 平滑滚动

现在您已经有了内容和菜单，您可以使用一点 jQuery 来平滑地围绕您漂亮的长画布制作动画。我们将监听菜单链接上的点击，然后用我们自己的行为替换默认行为:我们将使用 jQuery 的动画方法平滑地滚动到正确的元素，而不是直接跳到页面的那个部分。看起来是这样的:

```
$(document).ready(function() { $("#banner a").bind("click",function(event){ event.preventDefault(); var target = $(this).attr("href"); $("html, body").stop().animate({ scrollLeft: $(target).offset().left, scrollTop: $(target).offset().top }, 1200); }); }); 
```

这是如何分解的:首先，我们防止默认行为发生。接下来，我们设置一个变量来保存链接的`href`值。第三，我们用`html`和`body`元素执行一些动作:

*   确保任何当前播放的动画立即停止——如果有人点击几个菜单链接，我们不必等待所有滚动动作完成。
*   接下来，为了动画化我们的滚动行为——我们通过计算我们的`target`相对于文档的左侧和顶部有多远来计算出我们需要滚动到哪里，并使用`scrollLeft`和`scrollTop`来这样做。
*   我们为这个效果设置了 1200 毫秒(1.2 秒)的持续时间，这样就有足够的时间来欣赏呼啸而过的风景。

这就是你所需要的！[演示 3](https://s3.sitepoint.com/examples/sidescroll/demo3.html) 向您展示这一切是如何组合在一起的。

## 但是漂亮的东西呢？

现在你已经有了框架，你可以开始给你的侧滚设计添加一些色彩和活力了。我当然不是泰勒·芬克，但即使是我也可以对我们之前看到的灰色、四四方方的演示进行改进。在[演示 4 中，](https://s3.sitepoint.com/examples/sidescroll/demo4.html)你会看到我已经添加了一个大的背景图片，它改变了站点在不同面板之间的感觉。我还在菜单的背景颜色上添加了一些不透明度，并重新安排了一些标记，以允许菜单中有一个标题。最后，我稍微重新安排了一下，这样当 JavaScript 关闭时，布局恢复为常规的垂直滚动布局。

*   使用每个面板上的“上一页”和“下一页”按钮，引导读者沿着一条路径前进
*   在内容区域之间的大而宽的间隙中添加一些更具冒险性的背景图片
*   为什么仅限于水平滚动？使用这种技术在一个大的正方形画布上缩放！
*   根据当前的视口大小，使用 JavaScript 在面板之间添加更多或更少的空间
*   把这变成一组媒体询问——小屏幕当然可以跳过这个节目

无论你如何选择使用它，有一件事是肯定的——虽然这已经成为网页设计的一个趋势，但它仍然是不寻常的，你的网站一定会脱颖而出。我知道我会在我的下一个迷你网站中考虑它！

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [Learn CSS3](https://learnable.com/courses/learn-css3-203?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。对 CSS3 有疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?53-CSS-amp-Page-Layout?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章