# 使用 Google 的社交图 API 增强用户档案

> 原文：<https://www.sitepoint.com/google-social-graph-api-ruby-rails/>

我最近注册了杰里米·基思的播客应用程序。由于其“ [Mad Lib](http://en.wikipedia.org/wiki/Mad_Libs) ”风格的设计，注册表单受到了很多关注，但让我感兴趣的是，在我创建我的帐户后，我在我的个人资料页面的右下角看到了以下内容:

**图一。赫芬顿的别处简介面板**

![Huffduffer’s Elsewhere profile panel](img/af858a839b9f995a6dbad4e0168ce7ea.png)

赫芬顿从我提供的一个网址(louissimoneau.com)上找到了我的几乎所有资料，来自我用过的所有社交网络或网络应用。图中没有显示出来，但是这些链接指向我在这些网站上的个人资料。这怎么可能呢？

通过挖掘杰里米的一些博客帖子，我发现他正在使用谷歌社交图 API 来收集这些数据。我的网站指向我的 Twitter、Last.fm 和 FriendFeed 个人资料，而这些资料(主要是 FriendFeed)又链接到我所有的其他个人资料。谷歌能够跟踪这些链接，并通过巧妙使用 XFN 微格式(稍后会详细介绍)知道它们引用了我的其他实例。

Jeremy 的实现虽然很酷，但是有点生硬。有些人可能反对在他们的个人资料中列出他们的每一个网络身份，由于谷歌的蜘蛛依赖于缓存数据，一些信息可能已经过时。例如，几年前我删除了 interblag.tumblr.com，但后来又被另一个人拿走了。

我认为尝试开发这种功能的稍微复杂一点的版本可能是一个很酷的想法，同时学习一点关于社交图 API 如何发挥其魔力的知识。我将使用 Ruby on Rails 来实现这一点，但是由于代码非常简单，您应该能够很容易地将其应用到您喜欢的任何平台上。

## 微格式速成班

在我们进一步讨论之前，最好先退一步，试着弄清楚这个功能是如何实现的。谷歌怎么知道那些其他网站也是“我”？答案是微格式。如果你是一个真正的 HTML 迷，或者对语义代码情有独钟，你应该已经了解微格式，但是对于我们其他人来说，这里有一个快速的补充课程。

微格式就是:迷你格式，恰好位于 HTML 的大格式中。例如，HTML 缺乏一种方法来表明一个链接指向一个你见过的人的网站——也就是说，没有“met”属性可以让你这样做:

```
<a href="http://somesite.com" met="true" />
```

这个代码当然是无效的。幸运的是，HTML 有一些属性可以用于这个目的。`class`是最明显的，但是对于链接和锚我们也有`rel`。例如:

```
<a href="http://somesite.com" rel="met" />
```

与前面的代码不同，这段代码完全有效。这本身可能没什么价值:充其量你可以用 CSS 给指向你见过的人的链接赋予不同的风格。但是，如果世界各地的网络开发者 *同意* 用一种方式来表示这些信息，突然之间，可能性的范围就爆炸了:一个网络蜘蛛可以抓取见过面的人之间的链接，并构建这些关系的地图。

目前有各种微格式标准，从地址(hCard)到友谊(XFN)到标签(rel-tag ),还有许多其他标准正在起草阶段。

谷歌正在使用微格式标准 *XFN* (可扩展的朋友网络)和 *FOAF* (朋友的朋友)来抓取链接网络。这些链接指向以 URIs 为代表的人，而谷歌正试图弄清楚这些人之间的关系。FOAF 是一种稍微复杂一点的格式，它涉及到使用 XML 创建一个单独的文件来详述你所有的友谊；这对我们的实现是不必要的，所以我们只关注 XFN。

XFN 最基本的用法是在链接中使用`rel=”me”`来表示被链接的网站与我们所在的网站属于同一个人。

当然，这里有一个问题:这种微格式数据只有在有人费心把它放在那里时才会出现。只有一小部分网站所有者知道什么是微格式，更不用说在他们的网站上使用它们了。好消息是，一些大型网站和应用程序会自动将 XFN 添加到链接中。例如，Last.fm 和 FriendFeed 在你添加链接到其他网站时都使用这个标准。所以你可能在没有意识到的情况下就有了社交图谱。

WordPress 博客也使得用这种方式标记链接变得容易。向你的 WordPress 博客添加新链接时，向下滚动页面，你会看到以下内容:

**图二。WordPress XFN 支持**

![WordPress XFN support](img/d0ec1ea996594db0449806efd926c667.png)

因此，即使没有广泛的微格式知识，事实证明，对于 Google Social Graph API 到 spider 来说，有大量标记的 XFN 链接。

## 分享这篇文章