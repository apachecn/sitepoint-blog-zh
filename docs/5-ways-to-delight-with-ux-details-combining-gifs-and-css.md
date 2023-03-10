# 结合 gif 和 CSS 的 5 种 UX 细节

> 原文：<https://www.sitepoint.com/5-ways-to-delight-with-ux-details-combining-gifs-and-css/>

![GIF - Muybridge](img/5a8e91a5fe969103922510151422b2b6.png)

用户体验不仅是关于可用性，也是关于让你的产品或网站使用起来愉快，甚至令人愉快。在你的网站上创造愉快体验的一种方法是创建有意义的过渡、交互或视觉元素，以匹配你的网站的外观和感觉，而实现这一点的一种方法是结合 GIF 和 CSS。

通过下面的一些例子，你可以考虑如何以现代时尚的方式使用 GIF 和 CSS 不是为了分散注意力，甚至是留下印象，而是为了创造更好的体验。GIF 不再仅仅是给猫看的，也不再是给同事发垃圾邮件了。

这里有 5 个在网页设计中使用 gif 和 CSS 的例子，可能会帮助你取悦你设计和开发的网站的访问者:

## 1.翻转时制作 GIF 播放

无论是显示你的特色的图标，还是你想向你的网站访问者传达意思的图标——一个愉快且相当简单的方法就是使用 gif 和 CSS。你可以有一个静态的 GIF 起始版本作为背景，然后当有人滑过该区域时，[用动画 GIF 版本](http://animaticons.co/how-do-i-make-an-icon-play-on-rollover/)替换掉它。

```
<div class="play-GIF-on-hover"></div>
```

```
.play-GIF-on-hover {
    width: 200px;
    height: 200px;
    background-image: url('path-to-your-folder/this-is-the-static-version.GIF');
    background-size: 100%;
    background-position: center center;
}
.play-GIF-on-hover:hover {
    background-image: url('path-to-your-folder/this-is-the-animated-version.GIF');
}
```

## 2.将它们用作 GIF 预加载程序

如果你以前从未为预加载覆盖图创建过 [CSS，你将需要首先创建 HTML 和组成覆盖屏幕的 CSS。然后，您可以将自己定制的预加载 GIF 文件放在它的上面。如果你还没有预装的 GIF 文件，Codrops 有一套非常好的文件，你可以](https://ihatetomatoes.net/create-custom-preloading-screen/)[点击这里](https://tympanus.net/codrops/2014/04/25/freebie-flat-style-squared-preloaders/)(例子如下。)

![Preloaders](img/fa2e78b2ffe9fd1e27f3d985f7c228ee.png)

如果你需要更多关于如何制作立体 gif 的信息，请查看“ **[设计令人敬畏的动画 gif 的 7 个技巧](http://blog.invisionapp.com/7-tips-for-designing-awesome-GIFs/)**

## 3.创建动画菜单

![Giphy Navigation](img/732eac919935a735cb8075aba6357d9a.png)

Giphy 最近更换了他们的菜单，但这个以前的版本是辉煌的(和话题！Giphy 的菜单会播放一张 GIF 图片，让你对一号门、二号门、三号门等门后的内容有所了解。这里的诀窍是保持事情相当微妙，不要太“在你面前”，以免你觉得有点廉价。

## 4.作为背景像[头像](http://headscape.co.uk/)

同样，微妙是这里的关键。他们没有在 GIF 中展示一系列真实的动作，而是展示了一些真实的动作。这使得观众不会注意到，然后发现背景中有什么东西在动，也许仔细观察会给他们惊喜。

从技术上来说，这是一个被称为 GIF“cinemagraph”的很好的例子，重量相当可观，为 1MB。然而，如果你想对动画更感兴趣，这可能是一个循环播放的[视频背景](http://vodkabears.github.io/vide/)(最多 30-40 秒)的更好工作。

![Gif Animation](img/7d601f95180bd297504a696270d530de.png)

这是背景在上下文中的样子——作为一种广告牌/英雄部分，当然使用了——背景:用 CSS 覆盖以保持其尺寸适合空间。

![Headscape](img/b758366385ec04f1dd7fcb0d352d061b.png)

虽然 GIF 通常不会像标准视频那样压缩移动图像，但它有一个很大的优势——可变的帧速率。这意味着您可以将单个帧设置为显示 2 分钟，然后突然激活并重置。用不同的时间重叠几个半透明的 gif 可以产生有趣的随机结果。

## 5.按钮上的悬停状态

现在，因为移动网站上没有悬停状态，这限制了这里的应用。你可能想在一个网站上使用这种方法，在这个网站上你可以加入一个更微妙的 GIF 和一个轻轻移动的轻循环模式，以增加互动的感觉而不显得俗气。

仅仅因为有些东西不能在手机上使用，并不意味着你完全忘记了在桌面上逐步增强的方法，而没有使它成为导航或整体功能的关键。

![Button](img/0084259850e0e5978223d8426a804264.png)

代码片段，原来这里的[是](https://timbdesign.com/delight-website-visitors-user-experience)。

### gif 不仅仅是装饰

与任何设计细节一样，重要的是不要仅仅为了我们自己的创造性放纵而忙于添加装饰。每当我利用 gif 和 CSS 添加一个特性时，我都想确保我这样做是为了改善交流。上面的悬停状态表示某种意义，帮助访问者知道他们确实悬停在一个按钮上——甚至可能让他们好奇当他们采取下一步时会发生什么。

我们添加到设计中的每一个细节都应该有助于传达意义，并引导访问者沿着我们希望他们走的道路前进——或者至少让他们更容易、更愉快地到达他们想去的地方。

### 一些最后的想法

随着 gif 在某种程度上的复兴，我认为它们在所有新的 SVG 动画和 CSS 后处理器中有一席之地，如果没有其他的易用性和你可以达到的细节水平的话——甚至像 gif 这样的短视频。把它们放在你的军火库里，创造出像上面那些很酷的小细节。

### 奖励:轻松制作 gif 的工具

*如果你需要一种简单的方法来制作 gif，让你的 UX 令人愉快，试试 [CloudApp](https://www.sitepoint.com/cloudapp-optimize-bug-tracking-process/) 吧。*

*我们创建了一份备忘单，帮助您快速了解如何使用 CloudApp 的功能和键盘快捷键。把它放在你的电脑桌面上或者打印出来，你马上就可以高效地截图了。在这里抢:*

<form action="http://go.sitepoint.com/t/y/s/stisi/" method="post" id="subForm">

<label for="fieldEmail">邮箱</label>
<input id="fieldEmail" name="cm-stisi-stisi" type="email" required="">

<button type="submit">收到我的小抄</button>

</form>

## 分享这篇文章