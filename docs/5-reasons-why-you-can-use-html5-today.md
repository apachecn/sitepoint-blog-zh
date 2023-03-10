# 今天你可以使用 HTML5 的 5 个理由

> 原文：<https://www.sitepoint.com/5-reasons-why-you-can-use-html5-today/>

在我们最近的民意调查中，几乎一半的受访者表示使用 HTML5 还为时过早，而 3%的人声称他们永远不会使用它。虽然风险存在，但现在有几个很好的理由投资 HTML5。

## 1.HTML5 规范永远不会完整

也许这有点言过其实，但让我进一步解释一下……开发人员理解软件规范，很少有人会愿意根据一份草稿文档来削减代码；然而，HTML5 规范不同于典型的软件规范。W3C 不是创新者:它没有为浏览器供应商制定规则。供应商引领潮流，他们的技术想法最终被接受、拒绝或调整，成为 W3C 规范的一部分。HTML5 规范不太可能完全完整。唯一可能发生的时刻是当供应商和 W3C 在沙地上划了一条线，并转向 HTML6(或任何他们命名的下一个标准)。如果你等待最终的 HTML5 规范，你将会错过。

## 2.HTML5 就是 HTML

与[失败的 XHTML 2.0 规范](https://www.sitepoint.com/rip-xhtml-2/)不同，HTML5 是 HTML4/XHTML 1.0 的进化。你已经知道大部分了。有 28 个附加标签和一些新技术，但你不会使用一种全新的标记语言。开发工具有点难找，但是验证器开始出现了——[validator . nu](http://validator.nu/)就是其中的佼佼者。

## 3.传统浏览器支持很少成为问题

如今火狐、Chrome、Safari、Opera 都可以使用 HTML5。这些浏览器的用户倾向于快速升级，所以没有必要担心旧版本会实现奇怪的解析规则。那就只剩下 Internet Explorer 了。它不能识别新的标签，但是如果您在页面中添加一个 JavaScript shiv 来“创建”相关的 HTML5 元素，可以强制解析它们，例如:

```
document.createElement("header");document.createElement("footer");document.createElement("nav");// etc, etc…
```

在你的页面中包含以下代码`head`，IE6、7 和 8 将神奇地变成 HTML5 感知的:

```
<!--[if lt IE 9]><script src="http://html5shiv.googlecode.com/svn/trunk/html5.js"></script><![endif]-->
```

这不会帮助浏览器理解诸如`audio`、`video`和 SVG 之类的技术。在这些情况下，你需要使用 Flash 这样的后备，但我怀疑我们会在未来很多年里支持传统浏览器。

**Tip:** HTML5 Inline Elements

大多数浏览器，包括 IE 和 Firefox，默认假设新的 HTML5 元素是内联的。因为您几乎肯定希望像`header`这样的元素成为块级元素，所以记得在 CSS 中使用`display: block;`。

现在我怀疑你们中的一些人在想:哇！等等——可访问性呢？我的 IE 用户禁用了 JavaScript 怎么办？一个公平的观点，但实际上，它影响了多少人？IE9 将支持 HTML5，IE 的市场份额每月都在萎缩，我怀疑许多 IE 用户即使想禁用 JavaScript 也无法做到。此外，如果你已经放弃了 IE6，那将会影响更大比例的用户。即使非 JavaScript IE 用户是你的人口统计的重要部分，最糟糕的情况是新的 HTML5 元素将被取消样式。页面可能看起来很糟糕，但内容和功能应该仍然有效。

## 4.HTML5 正在发生

尽管 HTML5 已经像 Web 2.0 或 Ajax 一样成为一个松散的营销术语，但所有主要的浏览器供应商都支持该标准。他们可能有自己的原因和议程，但 HTML5 今天在这里，浏览器支持将继续改善和快速发展。

## 5.不要被落下！

HTML5 对不同的人意味着不同的东西。对于大多数 web 开发人员来说，它是 HTML 的渐进发展，因此不如 SVG、canvas、标准化音频/视频、地理定位等外围技术令人兴奋。你的许多客户，可能还有你的老板会有不同的看法。对他们来说，HTML5 是他们在商业新闻中看到的白热化技术；它在 iPad 里，所以一定很酷。所以，现在就开发几个 HTML5 页面吧:你将成为一名大师，成为同龄人羡慕的对象，并将在游戏中保持领先！

**tip:** Psst … wanna learn HTML5?

SitePoint 将于 7 月 26 日与约翰·奥尔索普合作推出新的 HTML5 直播课程。那里见！

## 分享这篇文章