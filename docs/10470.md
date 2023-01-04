# 基于标准的程序员的罪恶秘密

> 原文：<https://www.sitepoint.com/the-guilty-secrets-of-a-standards-based-programmer/>

在过去的几周里，我花了大部分时间为 [Opera](http://www.opera.com/) 开发浏览器扩展，使用 11a 中可用的[扩展 API。在这段时间里，我一直在为 Firefox](http://labs.opera.com/news/2010/10/21/) 的 [CodeBurner 做更新，这是我们为网络开发者提供的流行参考工具。](https://getfirebug.com/)

在这两种情况下，基于标准的技术的工作是强有力的。Opera 的扩展 <abbr title="Application Programming Interface">API</abbr> 使用了来自 <abbr title="HyperText Markup Language Version 5">HTML5</abbr> 的许多特性，比如[跨文档消息](https://www.w3.org/TR/html5/comms.html)和[网络存储](https://www.w3.org/TR/webstorage/)。Firefox 的扩展系统是基于 XUL 的，这当然是一个 XML 词汇表。

然而，真正*定义了*基于标准的技术的价值的一件事却在所有这些中找不到——**互操作性**完全不是问题，当你只需要处理一个浏览器的时候。

你知道吗？这是一种解脱！

不要误解我——我喜欢从事易访问性和互操作性是主要考虑因素的项目。我理解这种挑战，即无论使用何种设备访问，都要开发出能够正常工作的功能。

但与此同时，我有时希望 web 开发能够更简单、更可预测。只需要为一个浏览器、一组特性或一个支持特性列表进行编程；只需运行一组测试，然后就知道它是否有效！

当然，我不可能不知道我在这里所说的讽刺。因为如果所有的供应商和所有的客户都按照相同的开放标准运行，那么我们的工作*就会一直*那么简单。但是我们知道事实并非如此。而且可能永远也不会——如果说 <abbr title="HyperText Markup Language Version 5">HTML5</abbr> 教会了我们什么的话，那就是有时候你必须*忽略*标准，然后才能提升它们！

但不管怎样，我认为享受这种喘息的机会，承认那些我们都有的关于工作的罪恶秘密是没问题的。承认如果我们可以用表格来布局，我们的网站会更容易建立；如果我们只支持 Firefox，那么 JavaScript 将会更容易编写。

当我们真的足够幸运地处于这种情况下——比如在编写浏览器扩展时——对这一切的直接性感到高兴是可以的！

*缩略图鸣谢:[互联网 _ 乳品](http://www.flickr.com/photos/16339684@N00/2681435919/)*

**note:**Want more?

如果你想阅读更多詹姆斯的作品，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章