# 使用 WAI-ARIA 提高您网站的可访问性

> 原文：<https://www.sitepoint.com/wai-aria/>

![htmlcss2thumb](img/d1b531ecd770df998ea32dbc2cf0f23c.png)

以下是我们的书的摘录，由 Alexis Goldstein、Louis Lazaris 和 Estelle Weyl 撰写的《真实世界的 CSS3，第二版。世界各地的商店都有出售，或者你可以在这里买到电子书。

在第 2 章和第 3 章中，我们讨论了大量的内容，解释了如何使用 HTML5 的新语义元素使我们的页面变得更有语义性和更易访问。然而，仅仅改进语义通常不足以使复杂的 web 应用程序完全可访问。

为了让我们的用户尽可能方便地访问我们页面的内容和功能，我们需要 WAI-ARIA 提供的推动力，扩展 HTML5 已经做的事情。我们将避免在这里深入讨论 WAI-ARIA——这是一个可以占据许多章节的话题——但是我们认为在这里提到它是很重要的，这样你就知道你的选择。

WAI-ARIA 代表网络无障碍倡议-无障碍丰富的互联网应用。W3C 网站上的[对 WAI-ARIA 的概述将其解释为:](https://www.w3.org/WAI/intro/aria.php)

> [……]一种让残障人士更容易访问 Web 内容和 Web 应用程序的方法。它特别有助于使用 Ajax、HTML、JavaScript 和相关技术开发的动态内容和高级 JavaScript 用户界面控件。

依赖屏幕阅读器技术的用户或不会使用鼠标的用户通常无法使用某些网站和 web 应用程序功能，例如滑块、进度条和选项卡式界面。有了 WAI-ARIA，即使内容和功能被困在复杂的应用程序架构中，您也能够处理页面中的这些缺点。因此，依赖辅助技术的用户可以访问通常无法访问的网站部分。

## WAI-ARIA 如何补充语义学

WAI-ARIA 给元素分配 **角色** ，并赋予那些角色 **属性****状态** 。这里有一个简单的例子:

```
<li role="menuitemcheckbox" aria-checked="true">Sort by Date</li>
```

应用程序可能将列表项用作链接元素来对内容进行排序；然而，如果没有 role 和`aria-checked`属性，屏幕阅读器将无法确定这个元素的用途。语义本身(在本例中是一个列表项)没有告诉它任何东西。通过添加这些属性，辅助设备能够更好地理解该功能的用途。

对于语义元素，例如`header`、`h1`和`nav`，WAI-ARIA 属性在大多数情况下是不必要的，因为这些元素已经表达了它们是什么。相反，它们应该用于那些其功能和目的不能直接从元素本身辨别出来的元素(或者那些在一个或多个主流浏览器中很少或没有可访问性支持的元素)。

## WAI-ARIA 的现状

WAI-ARIA 规范仍在改进，HTML5 也是如此，所以这些技术还没有提供我们想要的所有好处。虽然我们已经描述了 WAI-ARIA 扩展页面元素语义的方式，但是可能有必要在元素上包含 WAI-ARIA 角色，这些元素已经在名称中表达了它们的意思，因为辅助技术还不支持所有新的 HTML5 语义。换句话说，WAI-ARIA 可以作为一种权宜之计，在屏幕阅读器跟上时代的同时，为 HTML5 页面提供可访问性。

让我们来看一个网站导航，例如:

```
<nav role="navigation"> <ul>… </ul>
</nav>
```

看起来我们在这里重叠了:`nav` 元素意味着其中包含的链接列表构成了一个导航控件，但是我们仍然向它添加了 WAI-ARIA 角色`navigation`。在许多情况下，这种重叠往往是必要的。在`nav`元素的例子中，Internet Explorer 是唯一一个默认情况下没有正确显示“导航”角色的浏览器，所以现在，添加这个属性是必要的。

这是否意味着一旦 HTML5 语义和可访问性得到完全支持，WAI-ARIA 将变得多余？不会。WAI-ARIA 中有一些角色没有相应的 HTML5 元素；例如，[计时器](https://www.w3.org/TR/wai-aria/roles#timer)的作用。虽然您可能使用 HTML5 `time`元素表示一个计时器，然后用 JavaScript 更新它，但是您无法向屏幕阅读器表明这是一个计时器，而不仅仅是一个静态时间的指示。

对于要访问 WAI-ARIA 角色的屏幕阅读器，浏览器必须通过可访问性 API 公开它们。这允许屏幕阅读器以类似于访问本机桌面控件的方式与元素进行交互。

浏览器对 ARIA 特性的支持一直在增长，目前非常好。所有最新版本的浏览器都至少部分支持 WAI-ARIA。在 Paciello 集团的网站上可以找到一个相当最新的指南，关于在某些操作系统的浏览器中支持 WAI-ARIA 等辅助功能。

最后，值得注意的是，并不是所有能够从 WAI-ARIA 角色中受益的用户都在利用他们。在 2014 年 1 月，WebAIM (Web Accessibility In Mind)进行了他们的[第五次屏幕阅读器用户调查，](http://webaim.org/projects/screenreadersurvey5/)调查显示，大约 28%的参与者很少或从不通过 ARIA 地标导航网页。好消息是，使用 ARIA 地标的用户数量正在增加。2010 年，[在一项类似的调查](http://webaim.org/projects/screenreadersurvey3/#landmarks)中，超过 50%的人没有使用或不知道咏叹调角色的存在。

简而言之，对 WAI-ARIA 有很好的支持，包含这些属性不会损害 HTML5 文档。它们通过了 HTML5 中的标记验证，尽管还没有看到全部的好处，但它们只会随着时间的推移而增加。

## 延伸阅读

如上所述，对所有 WAI-ARIA 角色的完整介绍超出了本书的范围，但是如果你有兴趣了解更多，我们首先推荐[官方规范](https://www.w3.org/TR/wai-aria/)。W3C 还编写了一个更短的[初级读本](https://www.w3.org/TR/wai-aria-primer/)和一个[创作实践指南](https://www.w3.org/TR/wai-aria-practices/)。

你也可以在 SitePoint 上查看斯蒂芬·马克斯[对 WAI-ARIA](https://www.sitepoint.com/introduction-wai-aria/) 的介绍。最后，你可能会发现回顾一下 [HTML5 Accessibility](http://www.html5accessibility.com/) 是一个由可访问性专家 Steve Faulkner 维护的网站，它以图表的形式从可访问性的角度总结了不同浏览器如何处理 HTML5 的语义元素。

## 分享这篇文章