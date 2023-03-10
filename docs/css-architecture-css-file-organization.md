# CSS 架构:CSS 文件组织

> 原文：<https://www.sitepoint.com/css-architecture-css-file-organization/>

下面是我们的书《CSS 大师》的摘录，作者是蒂芙尼·布朗。世界各地的商店都有出售，或者你可以在这里买到电子书。

### CSS 文件组织

一个好的 CSS 架构的一部分是文件组织。单一文件适合单独开发人员或非常小的项目。对于大型项目——具有多种布局和内容类型的网站，或者在同一个设计保护伞下的多个品牌——更明智的做法是使用模块化方法，将 CSS 拆分到多个文件中。

将你的 CSS 文件分开可以更容易的将任务分配给团队。一个开发人员可以处理与排版相关的样式，而另一个可以专注于开发网格组件。团队可以合理地分工，提高整体生产力。

那么，一个好的文件结构应该是什么样的呢？这是一个类似于我在最近的项目中使用的结构:

*   reset.css:重置和规范化样式；最少的颜色、边框或字体相关声明
*   字体，粗细，行高，大小，以及标题和正文的样式
*   layouts.css:管理页面布局和片段的样式，包括网格
*   forms.css:表单控件和标签的样式
*   lists.css:特定于列表的样式
*   tables.css:特定于表格的样式
*   carousel.css:轮播组件所需的样式
*   accordion.css:手风琴组件的样式

如果您使用的是预处理器，比如 Sass 或更少，您可能还想包含一个包含颜色变量等的 _config.scss 或 _config.less 文件。

在这种结构中，每个 CSS 文件都有一个特定而狭窄的范围。最终有多少文件取决于网站设计需要多少视觉模式或组件。

CSS 框架如 [Foundation](http://foundation.zurb.com) 和 [Bootstrap](http://getbootstrap.com/) 使用这种方法。对于进度条、范围输入、关闭按钮和工具提示，两者都变得非常精细。这允许开发人员只包含项目所需的组件。

#### 注意:模式库

像这样跨文件分割 CSS 的一个密切相关的概念是 **模式库** 。安娜·德本汉姆(Anna Debenham)的[“模式库入门”是关于这个主题的一本很好的入门书](http://alistapart.com/blog/post/getting-started-with-pattern-libraries/)

### 多少文件？

即使我们使用几个 CSS 文件进行开发，我们也不会以这种形式将它们全部提供给浏览器。我们需要的 HTTP 请求的数量会使我们的站点花费更长的时间来加载。相反，我们将 *将* 较小的 CSS 文件连接成几个较大的文件用于生产。

**串联**在这个上下文中，意味着将多个文件组合成一个 CSS 有效载荷。它消除了对`@import`语句或多个`link`元素的需要。目前的浏览器对一次可以下载多少文件有限制。我们可以使用串联来减少网络请求的数量，在更短的时间内将您的内容发送给用户。

通常，您的 web 开发框架会将连接作为其资产管理特性的一部分来处理，比如 Ruby on Rails。一些内容管理系统也是如此，无论是作为核心功能还是附加功能。预处理程序——在第 9 章中介绍，也使连接变得容易。如果预处理器和开发框架都不是您工作流的一部分，那么您的操作系统可能有一个串联实用程序可供您使用。

对于 Mac OS X 或 Linux，使用`cat`实用程序连接文件:

```
cat file1.css file2.css > combined-output-file.css
```

使用 Windows？试试`type`实用程序:

```
type file1.css file2.css > combined-output-file.css
```

您还可以使用 Bash、PHP、Python 或您选择的其他脚本语言编写自己的串联脚本。

#### 注意:CSS 优化

串联是 CSS 优化的一个方面。同样重要的是缩小你的文件，删除多余的字符和空白。缩小工具将在第 3 章介绍。

那么应该使用多少个文件呢？这就是棘手的地方。当前的最佳实践是确定您的 **关键路径**CSS——您的页面需要呈现的最小 CSS 数量——并使用`style`元素将其嵌入到您的页面中。应该使用 JavaScript 加载额外的 CSS 文件。Addy Osmani 的演讲[“CSS Performance Tooling”](https://www.youtube.com/watch?v=FEs2jgZBaQA)很好地概述了这种技术以及一些可以用来实现它的工具。另请参见灯丝组的[负载。](https://github.com/filamentgroup/loadCSS)

如果您的内容将使用 SPDY 或 HTTP/2 协议提供，则可能不需要连接。用 HTTP/1.1，浏览器顺序下载资产；当前一个请求结束时，下一个请求开始。在这种模式下，减少网络请求的数量可以提高网站的性能；但是，SPDY 和 HTTP/2 可以一次下载多个资源。因此，减少请求数量并没有真正的好处。但是，发送比呈现页面所需更多的字节是有代价的。陈伟霆的[“HTTP/2 的考虑和权衡”](https://insouciant.org/tech/http-slash-2-considerations-and-tradeoffs/)对此有更详细的解释。最好的方法是确定您的服务器是否提供 HTTP/2 服务，如果是，检查是否有更多的用户将受益于实际拆分您的资产，只加载页面需要的内容，或者继续以旧的方式工作。如果您有兴趣了解更多关于性能优化方法的信息，SitePoint book[*Lean Websites*](https://www.sitepoint.com/premium/books/lean-websites)是一个有用的资源。

## 分享这篇文章