# 谷歌的 Flash 索引灾难

> 原文：<https://www.sitepoint.com/googles-flash-indexing-disaster/>

7 月 1 日，谷歌[宣布](http://googlewebmastercentral.blogspot.com/2008/06/improved-flash-indexing.html)使用 Adobe 提供的[技术](http://www.adobe.com/devnet/flashplayer/articles/swf_searchability.html)增强了谷歌搜索引擎对 Flash 电影中嵌入的文本进行索引。随之而来的是谷歌的糟糕建议，网页开发者的事后批评，最后是几个直截了当的回答。

谷歌最初的声明是如此令人难以置信的模糊，以至于它几乎毫无用处。开发者们知道谷歌正在对他们的 Flash 内容做一些与众不同的事情，但仅此而已。

当谷歌的 Dion Almaer 提出搜索引擎一直都是黑匣子，应该由我们通过测试来发现发生了什么变化时，几乎所有人都在叫嚣这是不公平的。

谷歌的可信度立即受到质疑，因为它包含了明显糟糕的建议:

> “如果您希望谷歌忽略您的信息量较少的内容，如“版权”或“加载”信息，请考虑替换图像中的文本，这将使我们实际上看不到它。”

声明一下，用一个庞大的图片来替换快速加载的、可访问的文本内容，仅仅是为了在搜索引擎上隐藏它，从来都不是一个好主意。

谷歌在声明中列出的警告同样令人困惑:

> “Googlebot 不执行某些类型的 JavaScript。因此，如果你的网页通过 JavaScript 加载 Flash 文件，谷歌可能不知道这个 Flash 文件，在这种情况下，它不会被索引。”

哪些类型的 JavaScript？发布 Flash 内容的最佳实践是使用 [SWFObject JavaScript 库](http://code.google.com/p/swfobject/)来克服旧浏览器中的错误，那么谷歌是不是说它将只索引使用破损/过时的纯 HTML 技术创作的 Flash 内容？

> “我们目前不会附加由您的 Flash 文件加载的外部资源的内容。如果您的 Flash 文件加载了 HTML 文件、XML 文件、另一个 SWF 文件等。，谷歌将单独索引该资源，但它不会被视为你的 Flash 文件内容的一部分。”

任何有经验的 Flash 开发人员都知道，如果要在 Flash 内容中包含大量的文本，最好的办法是将它保存在 XML 文件中，然后动态加载，这样就不必在更改内容时重新构建 Flash 电影。

显然，Google 不仅看不到以这种方式创作的 Flash 内容，而且还会跟踪 XML 文件，并将其索引为您站点上的一个单独的页面。没错，谷歌将有助于引导人们搜索你的内容到包含它的原始 XML 文件，而不是你光滑的 Flash 前端。

所有这些东西都毫无意义，以至于许多开发者质疑谷歌是否真的能够索引任何重要的 Flash 内容。然而，没过几天，搜索引擎 War 博客就证实了谷歌[确实在索引 Flash 内容](http://www.search-engine-war.co.uk/2008/07/flash-time-to-c.html)。

最后，在几天的开发者抗议后，谷歌承认它留下了太多没有回答的问题，四天后，它发布了一个[重大更新](http://googlewebmastercentral.blogspot.com/2008/06/improved-flash-indexing.html)，如果你的网站上有任何 Flash 内容，它非常值得一读。

下面是我们现在所知的一个快速总结:

*   7 月 1 日的版本没有对使用 [SWFObject 库](http://code.google.com/p/swfobject/)的动态发布方法插入的 Flash 内容进行索引，该方法完全使用 JavaScript 将 Flash 内容写入页面。推荐的静态发布方法(页面中包含两个嵌套的`<object>`标签)*被*索引。Google 现在正在部署一个支持动态发布方法的更新。

*   从 XML 文件中动态加载的文本内容还没有被索引，但是 Google 正在努力解决这个问题。

*   谷歌将尽最大努力检测重复内容，以提供 Flash 内容的 HTML 替代，并将在搜索结果中只显示两个版本中的一个。没有处罚适用于网站的搜索排名由于重复的内容。

这里仍然有未知数，但谷歌搜索引擎将永远如此。虽然花了几天时间，谷歌正在回答它能回答的问题，并通过增强功能来回应开发者的担忧。

用不了多久，基于 Flash 的网站中的大部分文本都会进入谷歌搜索索引。然而，谷歌在一段时间内探测 Flash 内容的深度仍不确定。提供非 Flash 替代内容仍然是保证你最重要的内容在谷歌索引中占有一席之地的有效手段。它也给了不支持 Flash 的浏览器(如 iPhone)的用户一些可看的东西。

尽管谷歌最初的消息相当不成熟，但后续的消息打消了我的大部分担忧。你的呢？

## 分享这篇文章