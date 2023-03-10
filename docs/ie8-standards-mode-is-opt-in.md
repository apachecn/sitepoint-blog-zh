# IE8 标准模式是选择加入的

> 原文：<https://www.sitepoint.com/ie8-standards-mode-is-opt-in/>

<ins>[微软已经扭转了这个决定；默认情况下，IE8 将选择符合标准的模式。](https://www.sitepoint.com/microsoft-backflips-on-browser-version-targeting)</ins>

1.  “怪癖模式”保持不变，并且(将保持)与当前内容兼容。
2.  “标准模式”与 IE7 保持一致，并且与当前内容兼容。
3.  如果您(页面开发人员)真的想要 IE8 所能提供的最佳标准支持，您可以通过插入一个简单的`meta`元素来实现。

Aaron Gustafson 在最新一期的 List Apart 中也记录了 meta 标签[(经过仔细的论证和支持)，看起来是这样的:](http://alistapart.com/articles/beyonddoctype/)

`<meta http-equiv="X-UA-Compatible" content="IE=8" />`

虽然我承认，为了保持与昨天(和五年前)的网站的向后兼容性，选择在 IE8 中以标准模式呈现页面似乎是唯一明智的选择，但这种方法有一点让我很恼火:

将对特定浏览器的引用硬编码为页面标记的必要部分让人感觉很脏。

指定字符编码或页面使用的语言是一回事，但与它兼容的代理呢？从根本上和哲学上讲，一个页面完全与浏览器无关是有意义的。事实上，在一个页面中提到一个浏览器可能会被解释为一种极客形式的广告，而其他浏览器则因为更好地遵守 W3C 规范而付出代价。其他浏览器会效仿吗，以至于 web 开发人员发现有必要编写这样的东西吗？

`<meta http-equiv="X-UA-Compatible" content="IE=8;FF=3;Opera=9;Konqueror=3;Safari=3..." />`

不过，我想这比使用条件注释要好。

## 分享这篇文章