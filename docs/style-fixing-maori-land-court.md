# 有风格的立场:修复毛利土地法院

> 原文：<https://www.sitepoint.com/style-fixing-maori-land-court/>

“嘿，乔，你能从“欢迎”页面添加一个到“决策”页面的链接吗？”

“没问题，只要有足够的时间，任何事情都可以做，”我回答。

“我们下周需要它。”

我相信你们在某个阶段都有过这样的对话。我敢肯定，你们大多数人都不得不“修复”另一个开发人员或设计人员的早期作品。这次讨论是重新设计的一次冒险的开始。让我解释一下。

##### 进退两难

这是我面临的困境——也是我们在 Web 开发生涯中的某个时刻都会面临的困境:

*   对旧页面进行更改，或者
*   从头开始，重新构建整个系统

毛利土地法院网站由一家设计公司于 2001 年开发。像过去的大多数网站一样，在你真正进入网站之前，它会有启动页面和欢迎屏幕。另一种常见的做法是，通过获取设计者的图像并将其切割成表格来创建网页，表格中是一堆大小精确的单元格和间隔图像。

然后是鼠标悬停；最初的规范希望欢迎页面上的文本是英文的，但是当用户将鼠标悬停在链接上时，文本变成了毛利语。我对页面所做的任何更改都必须保留这个功能，并且尽可能保持当前页面的外观和感觉。

我最讨厌的网页之一是使用图片而不是文本(我相信尼尔森也同意)。不幸的是，最初的设计者就是这样开发网站的。毫无疑问，周围有设计师不希望他们的排版被替换的字体破坏，但我认为大多数用户并不真正关心(毕竟，网站是为用户服务的)。

##### 决定

我们的标准狂热者不会有一个艰难的决定，但是我真的有时间花一整天来重建一个页面吗？我确信我可以在表格中再插入一个单元格，为文本创建图像，并在半天内将它们组合在一起。相反，我决定尝试一下无表格版本，只是为了挑战自己，看看它是否可行。

在做出这个决定时，我知道我还必须遵守新西兰电子政府网站指南，该指南建议在布局上避免使用表格。我们的大部分观众来自农村，他们不得不面对 9600 b/s 的拨号速度，以及穿过电栅栏的共享电话线。

##### 库存

我有一个 15kB 的 HTML 页面，一个 1kB 的样式表和 46 张图片(54.7kB)，总共 70.8 kb。[原始页面](http://www.justice.govt.nz/maorilandcourt/oldwelcome.htm)由一张 whakairo(雕刻)的图片组成，包含几个切片，导航文本在它的左边。每个导航项目都与一个翻译图像、一个“毛利土地法院”及其毛利语翻译“Te Kooti Whenua Maori”的动画 GIF 相关联，最后还有一个小图标。

##### 该方法

我会一步一步解释这个过程。

***画面***

我进入我的工具包，拿出我信赖的 Photoshop，开始重新组装 whakairo 的切片(事后想起，我应该使用屏幕转储)。幸运的是，背景是一个独立的图像，不是切片的一部分，所以它很容易使用，就像动画文本 GIF 一样。

对于每个菜单项，原始页面上的图标旋转了不同的角度。我决定最好只用一张图片作为图标。最初的设计者可能不同意，但正如我所说的，网站是为用户服务的。

***HTML***

文档的基本结构很简单:它有 8 个链接和一些装饰图片，所以这就是我如何构建 HTML 的；一个用于图片，一个用于导航链接，一个用于访问键。

***迷失在*的翻译中**

我把每一个导航链接分成了自己的`<div>`，每一个都有自己的`id`。在每个`<div>`中，我用英语和毛利语文本创建了链接，每个链接都有自己的`<span>`。我的计划是使用`:hover`伪类来打开和关闭每个跨度。

```
<div id="search"> 

  <a href="search.htm"> 

    <img src="images/buttong.gif" width="26" height="25" alt="Search"> 

    <span class="en">Search</span>&nbsp;<span class="mi">Rapu</span> 

  </a> 

</div> 

a span.en, a:hover span.mi { 

  display: inline; 

} 

a:hover span.en, a span.mi { 

  display: none; 

}
```

然后使用 CSS 对每个`<div>`进行绝对定位。

***问题***

该页面在 Firefox 中运行良好。我的计划进行得非常顺利——直到我在 Internet Explorer 中尝试。我确信我的代码是有效的，所以我快速访问了 [SitePoint 论坛](https://www.sitepoint.com/forums/showthread.php?t=157415)，几分钟内就找到了问题的解决方案。显然，IE 中有一个已知的 bug，要求属性必须在`:hover`上改变，否则跨度不会显示。所以 CSS 变成了:

```
a:hover { 

  background-color: transparent; /*hack for IE */ 

} 

a span.en, a:hover span.mi { 

  display: inline; 

} 

a:hover span.en, a span.mi { 

  display: none; 

}
```

***老浏览器***

令我惊讶的是，在 IE4 和 NN4 中，这个页面看起来和预期的一样。翻译鼠标悬停也有效。我遇到的唯一问题是，因为图标包含在链接中，NN4 在它周围加了一个边框。我认为我和我的用户可以接受这一点。

***悬案***

在 Opera 7.2.1 中将指针从链接文本移开时，英文文本不会重新出现。这只会发生在文本上，不会在用户从图标上移开鼠标时发生。我仍然不确定为什么这个 bug 会存在，但是我怀疑我是唯一一个用 Opera 访问我们网站的用户。我尝试过在 a 元素上使用`:link`伪类，但是这在不同的浏览器中会导致一些奇怪的显示问题。

##### 结果呢

完成的页面在大小和服务器请求方面有了巨大的减少。HTML 从 15.1kB 缩减到 3.4kB，CSS 从 1kB 增加到 2kB，现在只有 4 张图片，总大小为 37.9kB，比 46 张图片减少了 54.7kB。包括图片和 CSS 在内的总页面大小为 41.3kB，减少了 29.5kB

##### 结论

虽然代码可能不完美，但还不错。该页面为我们网站的大多数访问者提供了预期的功能，并且变得更易于文本读者访问。

这个项目只花了我估计时间的一半。要求变更的经理对结果很满意。我的下一个挑战是如何使网站的其余部分达到标准！

## 分享这篇文章