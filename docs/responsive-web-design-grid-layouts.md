# 响应式网页设计:自定义网格布局

> 原文：<https://www.sitepoint.com/responsive-web-design-grid-layouts/>

在响应式设计系列的最后几个部分中，我已经介绍了[流体布局](https://www.sitepoint.com/responsive-web-design-fluid-layouts/ "Fluid Layouts")的特征，并且我已经描述了[如何响应性地使用字体](https://www.sitepoint.com/understanding-responsive-web-design-how-to-manage-fonts/ "How to use fonts responsively")。在本文中，我想介绍网格布局，这是一种广泛流行的设计实践。虽然网格系统已经在印刷媒体中得到了广泛的应用，但是 web 和移动开发者的兴趣却是最近才出现的。

网格是一种二维结构，由交叉的垂直和水平分割组成，用于结构化内容。网格是一个框架，设计者可以在这个框架上把文本和图像组织成一个合理的、易于理解的界面。一个构建良好且实现得当的网格系统可以提高网站或移动应用程序中内容的可伸缩性和可读性。

网格布局的优点很多；一个网格结构…

*   给内容的呈现带来秩序、创意和和谐；
*   允许用户预测在哪里可以找到他们需要的信息；
*   更容易添加新内容，而不会显得杂乱无章或边缘化。

要开发一个构建良好的网格布局，第一步是创建画布结构本身。画布的大小决定了网格的框架:它被划分为设计者想要的列数(3，5，9，12)，这是处理界面或布局的起点。

网格的大小不必用固定的参数来定义。对于这种类型的布局，一个常见且正确的设计实践是让内容定义网格所占据的空间，而不是由*而不是*来定义，反之亦然。在这种情况下，“内容”不仅仅指文本；它也指界面中所有有价值的东西，包括图像、视频、文本、广告、链接等等。你应该围绕内容的大小和性质来设计你的网格，而不是试图把各种各样的内容大小和类型塞进一个预先确定的网格中。在您自定义的网格结构中，布局通过垂直和/或水平的指引线进行划分，以提供一致的空间组织。一旦您定义了页边距和列间距，您的网格将成为您的多面移动内容的清晰、合理且有用的组织架构。

### 在线工具

在更新、更健壮的框架普及使用基于网格的布局之前，基于 HTML 和 CSS 的 Web 设计框架已经非常流行了。互联网上充满了有用的模型和框架以及 CSS 规则，帮助设计者为几乎任何目的创建简单、快速、高效的网格布局。有些布局是灵活的，有些则相当死板(毕竟它们是为了约束内容而设计的)。其中一些最多有三至五列，而另一些有 12 至 16 列。我提出了各种各样的网格框架来强调它们并不都是一样的，而且，有些网格天生就比其他网格更好。然而，为了充分理解基于网格方法的好处，您应该为您必须开发的每个项目考虑一个独特的模式和目的。你不应该害怕尝试各种各样的东西；记住，简单往往是获胜的设计。

在网上，有许多有用的网格资源可以帮助你为你的项目建立一个合适的布局或者界面。

*   网格系统 960 :一个让你使用 960 像素的网格创建网站的工具。选择这个数字是因为它允许容易地划分成各种列和行。
*   Gridr Buildrrr :为定制项目提供精确控制边框、边距和框内容的工具。
*   网格设计(Design by Grid):这是一份杂志，发表关于基于网格的设计的文章和教程。

### 网格布局标记

如果您想尝试用自己的手写代码开发一个网格结构，请确定界面宽度和各列之间的间距大小。请记住，为了获得平衡的效果，各种水平间距大小相等是很重要的。如前所述，*首先研究你的内容*，思考它应该如何定位和安排，并在整个网格开发过程中保持这些想法。

在包装器中添加应用或移动网站的主要元素(页眉、主要内容、侧栏和页脚)，如下所示:

```
<div id="wrapper">
  <div id="header">Header</div>
  <div id="content">Content</div>
  <div id="sidebar">Sidebar</div>
  <div id="footer">Footer</div>
</div>
```

此时，您的内容和侧边栏将被分成几个部分，所有部分都将被分配一个名为 Grid1、Grid2、Grid3 等的类(这取决于您决定构建的网格数量)。在每个网格单元中，可以包含任意多的子部分。

```
<div class="griglia1">
  <div>Subsection 1</div>
  <div>Subsection 2</div>
  <div>Subsection 3</div>
</div>
```

至于 CSS，几乎所有的东西都基于对 *float* CSS 属性的正确应用。为了正确定位网格设计中最突出的区域(也就是说，网格的主容器，它又包含子区域)，您使用了所谓的“反向浮动”技术。对于网格和它们之间的水平间距，您必须首先创建几个独立的柱部分，其次，您必须为每个部分分配一个浮动和一个固定的宽度。我已经使用下面的 CSS 应用了这种技术:

```
div.grid1
{
  float: left;
  width: 290px;
}
```

随后，您将在多列的节中应用相同的浮动规则，为宽度和右边距指定特定的大小(在本例中为 10 像素)来分隔它们，如下所示:

```
div.grid2 div
{
  float: left;
  width: 250px;
  margin-right: 10px;
}
```

为了增加额外的模块化级别，使一个部分能够占用两列或更多列的空间，让我们使用所谓的联合列:您只需为一个元素分配一个 ext2、ext3、ext4 或 ext5 类型的类，就可以拥有一个占用 2、3、4 或 5 列的部分，而不是单一宽度。

### 结论

本文旨在指出另一种设计和开发响应性布局或界面的可能技术。如果有一条关于网格的原则需要记住，那就是:不要试图把你的内容塞进一个不合适的网格。如果流行的解决方案不能满足您的需求，请为您的内容量身定制您自己的网格。使用几个列、行和 CSS 声明就可以快速轻松地完成。

想了解更多关于响应式网页设计的知识吗？看看 SitePoint 的新书 [Jump Start 响应式网页设计](https://www.sitepoint.com/books/responsive1/)！

## 分享这篇文章