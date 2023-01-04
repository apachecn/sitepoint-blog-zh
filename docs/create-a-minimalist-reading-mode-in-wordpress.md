# 如何(以及为什么应该)在 WordPress 中建立阅读模式

> 原文：<https://www.sitepoint.com/create-a-minimalist-reading-mode-in-wordpress/>

我定义的“阅读模式”是一个可选的极简视图，网站访问者可以使用它来查看你的帖子内容。我所做的是极简主义——除了内容什么都没有:没有边栏，也没有广告。它具有触摸感应和响应能力，因此移动用户使用它不会有任何问题。我还添加了字体大小增加和减少的功能。

那么，是什么带来了这种阅读模式的灵感呢？

我最近有一个客户花了一大笔钱把他所有的广告放在网站的侧边栏和标题上，结果却要求完全不同的东西——一个简化的阅读模式。我认为这是一个奇怪的要求，考虑到所有花费在广告块开发上的钱，但是对内容的对比和关注已经对网站产生了重大的积极影响。

### 为什么我需要阅读模式？

首先，人们实际上正在我的客户的网站上利用这种阅读模式，用户在网站上停留的时间比那些没有利用这种模式的人长得多，这表明利用这种功能的人比不使用阅读模式的普通用户倾向于消费更多的内容。

第二，广告收入没有下降，而是增加了。由于人们在网站上停留的时间更长，他们建立了更多的信任，显然比以前点击或回复了更多的广告。

此外，流量跟踪显示，阅读模式的用户最终会更频繁地回来。阅读模式为他们增加了重要的价值——他们更喜欢在这个网站上消费内容，然后回来获取更多内容。

### 现在有了一个插件

听起来好得难以置信？我会做得更好——我做了一个免费的阅读模式插件，只能在设计节上找到。最终，你会在 WordPress.org 的插件库中看到这个。

只需[下载这个文件](https://www.sitepoint.com/wp-content/uploads/2013/03/Justyns_Reading_Mode.zip)，使用 WordPress 管理中的【插件】- >“添加新项”上传，并激活它。你会在所有帖子的顶部看到“阅读模式下的视图”，但不会在你的页面上看到。

### 道具应该放在哪里

在进入如何为你的 WordPress 站点定制这个插件的具体细节之前，这是围绕 Jack Moore 的惊人工作建立的，他开发了 [ColorBox jQuery lightbox](http://www.jacklmoore.com/colorbox/) 。所以，去看看[的文档](http://www.jacklmoore.com/colorbox/)，因为我的插件就是建立在这个基础上的，并且还能让你访问他所有有用的工具。

有大量的文档和对 ColorBox 的支持，还有一个用户社区。这非常适合阅读模式的后端，因为它将支持我的基本需求之外的许多功能。

### 自定义阅读模式按钮

阅读模式按钮设计由 CSS 控制。在 plugin 文件夹中，导航到 CSS 子文件夹并打开 colorbox.css 文件。按钮的类是 rm-button，颜色可以完全定制。从第 90 行开始:

[source code language = " CSS " first line = " 90 "

/*阅读模式按钮*/
。RM-button {
border-top:1px solid # 000000；
背景:# 4d4d4d
填充:5px 10px
-WebKit-border-radius:8px；
-moz-border-radius:8px；
边框-半径:8px
颜色:白色；
字体大小:22px 字体系列:Helvetica，Arial，无衬线字体；
正文-装饰:无；
文本对齐:居中；
垂直对齐:中间；
宽度:220px
最大宽度:90%；
}
。RM-button:hover {
border-top-color:# bdbdbd；
背景:# bdbdbd
颜色:# 171717；
}
。RM-button:active {
border-top-color:# 000000；
背景:# 000000；
}
。rm-button a:link，a:visited {
text-decoration:none；
字体大小:22px 字体系列:Helvetica，Arial，无衬线字体；
正文-装饰:无；
垂直对齐:中间；
颜色:# fff
}
[/sourcecode]

请注意，在第 104 和 105 行，您可以调整按钮的大小，并调整它的工作方式。

### 改变阅读模式的宽度

阅读模式窗口本身更难使用。您需要使用 jquery.colorbox.js JavaScript 文件。(注意:有一个缩小版的 ColorBox jQuery，但是编辑起来要困难得多。)

在插件的“js”子文件夹中的 jquery.colorbox.js 文件中，您可以在第 13 行找到默认窗口的大小:

[source code language = " JavaScript " first line = " 10 " highlight = " 4 "]

默认值= {
过渡:
速度:300，
宽度:“85%”，
初始宽度:“600”，
内宽:假，
最大宽度:假，
高度:假，
初始高度:450，
内高:假，
最大高度:假，
比例照片:真，
滚动:真，
内联:假，
html

[/sourcecode]

您可以使用像素或百分比，只要确保保留双引号。

### 自定义文本大小调整按钮

为了让插件尽可能简单，让阅读模式尽可能实用，我使用了基于文本的按钮来控制字体的大小。您可以通过编辑 image-sizer.js 文件的第 7 行来添加图像:

[source code language = " JavaScript " wrap lines = " true "]

jQuery(文档)。ready(function($) {
//将此设置为包装文章内容的元素的 CSS 选择器。
//例如。入口或。条目内容
var 选择器= '。RM-content '；

//您的“以阅读模式查看”链接的 HTML。
var html = '<p class = " RM-button "><a href = " # " class = " Reading-Mode " rel = " no follow ">阅读模式下的视图</a></p><p class = " RM-sizes "><a href = " JavaScript:void(0)；"onclick = " resize text(1)" id = " plus text ">放大文本</a>|<a href = " JavaScript:void(0)；"onclick = " resize text(-1)" id = " MINUS text ">使文本变小</a><p>'；

$(选择器)
。前置(html)
。查找('。【T2 读书模式】)。colorbox({
innerHeight: "80% "，
innerWidth: "40% "，
inline: true，
href:selector
})；
})；

[/sourcecode]

代替文本，将您想要使用的任何图像放入带有 JS 文件的文件夹(或子文件夹)中，并像您使用的那样进行链接:

```
<img src="#" />
```

让我知道你对这种阅读模式的看法，以及有哪些其他的功能会让它对你的读者更有价值！

## 分享这篇文章