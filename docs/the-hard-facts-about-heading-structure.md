# 关于标题结构的确凿事实

> 原文：<https://www.sitepoint.com/the-hard-facts-about-heading-structure/>

一定要阅读[我在这个问题上的最新文章](https://www.sitepoint.com/heading-headaches-balancing-semantics-and-seo/)，因为事情已经发生了变化。

以下内容转载自《科技时报》第 157 期。

作为一名所谓的网络技术专家，我在工作中不得不习惯的一件事是，我的假设不时被证明是错误的。像 HTML 这样的语言有时看起来似乎很简单，即使有了多年的经验，仍然有重要的经验留给你去学习。

不久前，我学到了一个关于 HTML 标题结构的惨痛教训，我想很多网页设计师也会对此感到惊讶。简而言之，HTML 文档中的标题结构独立于文档结构。

让我们从我们都同意的事情开始。如果你写的 HTML 文档只是一篇文章或其他文本，只有一个标题和一些副标题，你应该使用`<h1>`作为标题，使用`<h2>`作为副标题。如果你的副标题本身包含副标题，那么应该用`<h3>`标记出来，以此类推。

```
<h1>Article title</h1>
<p>Lorem ipsum dolor sit amet...</p>
<h2>Subheading</h2>
<p>Lorem ipsum dolor sit amet...</p>
<h3>Sub-subheading</h3>
<p>Lorem ipsum dolor sit amet...</p>
<h2>Subheading</h2>
<p>Lorem ipsum dolor sit amet...</p>
```

目前看来还算合理，对吧？

现在，如果这是一个典型的网页，它也会有一些导航元素，也许还有一些次要内容，比如相关的论坛讨论。页面的每个部分都会有某种标题，但是你会使用什么标题标签呢？

在某种程度上，你的答案可能取决于你如何思考。如果你的第一反应是这些附加内容没有文章重要，你可以这样写:

```
<div class="navigation">
  <h2>Navigation</h2>
  <ul>...</ul>
</div>
<div class="article">
  <h1>Article title</h1>
  <p>Lorem ipsum dolor sit amet...</p>
  ?
</div>
<div class="discussion">
  <h3>Related Discussion</h3>
  <ul>...</ul>
</div>
```

这不是一个不合理的假设，W3C 的 HTML 验证器肯定会非常满意，但事实上它严重损害了你网站的可访问性。

W3C 的[网页内容可访问性指南 1.0 (WCAG)](https://www.w3.org/TR/WCAG/) 在对[章节标题](https://www.w3.org/TR/WCAG10-HTML-TECHS/#document-headers)的讨论中澄清了这一点:

> 在 HTML 中，H2 元素应该遵循 H1 元素，H3 元素应该遵循 H2 元素，等等。内容开发人员不应“跳过”级别(例如，直接从 H1 到 H3)。

所以事实上，因为导航、文章和相关讨论都在页面的*结构*中处于同一层次(无论它们的相对重要性如何)，所以它们都应该以`<h1>`标题开始。

举个更进一步的例子，页面可能会有某种带有你的站点名称的顶级标题或标志。很明显，这应该用一个`<h1>`标签来标记，但是这对文档的其余部分有什么影响呢？

这就是我最近学到的惨痛教训。我认为`<div>`标签是一种将文档分成“子文档”的工具。按照这种思路，您可能会尝试这样做:

```
<h1>My Site</h1>
<div class="navigation">
  <h1>Navigation</h1>
  ?
</div>
<div class="article">
  <h1>Article Title</h1>
  ?
</div>
<div class="discussion">
  <h1>Related Discussion</h1>
  ?
</div>
```

因为上面代码中的每个标题都是包含标题的`<div>`标签中的顶级标题*，所以对每个标题使用`<h1>`似乎是明智的。*

如果您使用某种内容管理系统来单独编写文章内容，然后动态地将其插入到页面中，这可能特别有吸引力。当孤立地处理文章时，文章的标题是顶层标题，所以使用`<h1>`似乎是有意义的。然而，当你把这篇文章放在一个完整的网页中时，情况就不一样了。

虽然它们可以为您的文档添加结构，以便在应用 CSS 样式时使用，`<div>`标签和其他块级元素与您文档的标题结构无关。[来自 WCAG 1.0](https://www.w3.org/TR/WCAG10-HTML-TECHS/#document-headers) 又来了:

> 部分应该与 HTML 标题元素(H1-H6)介绍。其他标记可以补充这些元素以改进表示(例如，HR 元素创建水平分割线)，但是视觉表示不足以识别文档部分。

因为像屏幕阅读器这样的辅助技术允许用户通过标题结构浏览文档，所以标题需要形成一个独立于文档标记的合理结构。所以在我们上面的例子中，页面的每个部分都应该以`<h2>`开头:

```
<h1>My Site</h1>
<div class="navigation">
  <h2>Navigation</h2>
  ?
</div>
<div class="article">
  <h2>Article Title</h2>
  ?
</div>
<div class="discussion">
  <h2>Related Discussion</h2>
  ?
</div>
```

为了在您自己的站点上测试标题结构，您可以使用 W3C 的[语义数据提取器](https://www.w3.org/2003/12/semantic-extractor.html)根据标题结构查看文档的轮廓。

你会注意到 SitePoint [的首页在这次测试中表现不太好](https://www.w3.org/2005/08/online_xslt/xslt?xmlfile=http%3A%2F%2Fcgi.w3.org%2Fcgi-bin%2Ftidy-if%3FdocAddr%3Dhttp%253A%252F%252Fwww.sitepoint.com&xslfile=http%3A%2F%2Fwww.w3.org%2F2002%2F08%2Fextract-semantic.xsl)，所以如果你的网站在这方面表现不佳也不要太难过。这只是表明，我不是 SitePoint 唯一一个经历了惨痛教训的人。你可以打赌，这将是固定的在下一次重新设计 sitepoint.com！

一定要阅读[我在这个问题上的最新文章](https://www.sitepoint.com/heading-headaches-balancing-semantics-and-seo/)，因为事情已经发生了变化。

## 分享这篇文章