# 遐想:一个基于 WordPress 启动主题的基础

> 原文：<https://www.sitepoint.com/reverie-foundation-based-wordpress-starter-theme/>

[遐想](http://themefortress.com/reverie/)是一个流行的 WordPress starter 主题，在我之前的文章《[基础和 WordPress](https://www.sitepoint.com/foundation-and-wordpress/) 入门》中提到过。

Reverie 主要基于 ZURB 的基础框架，增加了一些额外的好处。你不需要自己去尝试把基础框架和 WordPress 融合在一起。如果您想直接投入开发，这是一个很好的起步和运行方式。

当你使用一个已存在的主题时，即使它是一个框架，你也要对它进行定制，理解如何去做是很重要的，这样你就可以简化这个过程。如果不花时间去理解你所使用的框架或入门主题，最终会比你从头开始构建一切花费更多的时间。

## 你的第一个重大决定

每当您自定义任何基础或起始主题时，您都希望评估对子主题的需求。如果你想保持主题的更新，你肯定想走使用子主题的路线。这样做的主要原因是，当你更新主题时，你可能会丢失你所做的所有更改。如果你是为客户建立网站，这很容易成为一场灾难。

另一方面，如果你不想让客户自己更新主题，破坏你所有的努力工作，你可以用一个简单的代码片段禁用更新。这将使该用户无法从后端更新主题。对主题和插件的更新可能会破坏整个网站，所以你可以通过在 functions.php 文件中添加以下内容来禁用它们。

```
function remove_core_updates() {
  global $wp_version;return(object) array('last_checked'=> time(),'version_checked'=> $wp_version,);
}
add_filter('pre_site_transient_update_core','remove_core_updates');
add_filter('pre_site_transient_update_plugins','remove_core_updates');
add_filter('pre_site_transient_update_themes','remove_core_updates');
```

## 第一次安装后

你可以从截图中看到，当你第一次安装 review 主题时，它附带了 Customize 功能，使你能够从前端预览基本的变化，如背景颜色，背景图像和小部件。许多流行的主题正在以详细的方式定制这个区域，下至字体颜色、字体家族等等。

## 为幻想设置一个儿童主题

子主题的工作原理是，文件保存在一个单独的文件夹中，因此当您更新主主题时，单独文件夹中的文件不会受到影响。这意味着你所有的努力工作和定制设计不会在你每次更新主题时被删除。

在很大程度上，为 Reverie 设置一个子主题和其他主题是一样的。在主题目录中，您需要创建一个名为 review-child 的文件夹。在里面，你需要放置一个 style.css 文件和一个 functions.php 文件。这对于任何子主题都是典型的。如果您想要添加自己的自定义功能，并且想要添加自己的自定义样式，这些文件是必需的。

在子主题的样式表中，您需要做的就是按照子主题 CSS 文件的基本设置进行操作。

```
/*
Theme Name: Reverie Child Theme
Description: Child theme for Reverie 
Author: James George
Template: reverie
*/

@import url("../reverie/style.css");

/* Start child theme customization below *
```

现在，您已经准备好将您的自定义功能和样式添加到新的 Reverie 子主题中。

![Reverie Install](img/3dcf15329c8c41dd4bbc8c1de8de2671.png)

这是典型的 Reverie 安装后的样子。如果我们想要自定义外观，我们需要将自定义 CSS 添加到子主题文件夹中的 style.css 文件中。

最好的方法是打开 main-index.php 文件和 single-page.php 文件，看看它们的结构。记下你想关注的任何特殊课程是很重要的。在很大程度上，一切都应该很简单。

另一种方法(也是我选择的方法)是使用 Firefox 的 Firebug Inspector 和 Chrome 的 Inspect 工具。你选择哪一个并不重要，它们都做着同样的事情。我个人比较喜欢火狐的 Firebug。我可以检查一个元素，它会告诉我 div ID 和任何具有设计特定元素的属性的类。

![Reverie - Highlight](img/2db3f8af53846a350f0a0003785cf2f4.png)

这也是一个很好的方法来查看事物是如何构建的。当你将鼠标悬停在 Firebug 窗口顶部的分解中的一个项目上时，它会高亮显示该元素。您可以看到 div 是如何嵌套的，这可以帮助您缩小您试图解决的任何问题。

![Reverie - Inspection](img/9da5a592fc06de7cd75e897143b54155.png)

在上图中，你可以看到你正在查看的代码区域以浅蓝色突出显示。如果有紫色区域，则表示存在任何填充。黄色代表应用的任何边距值。这是一个很好的方法来微调任何间距问题，并诊断为什么你的设计不是你想要的结构。

在上面的例子中，如果我想设计作者姓名的样式，改变它的颜色，我可以将鼠标悬停在它上面，单击右键，然后选择 inspect element。然后，在下面的代码中，您可以看到 div 和类名，这实际上是 byline author 的 span 类。

由于作者姓名是一个链接，我可以在末尾添加一个`a`，选择活动元素:

```
.byline.author a{color: #F90;}
```

![CSS in Firebug](img/332a3106018f776aa5e2681c3f2f6921.png)

这将作者链接更改为橙色。这很简单，但是这些是设计你的主题的基础。此外，从一页 PHP、div 和类开始——并试图在头脑中把它们全部整理出来要容易得多。

在本系列的下一篇文章中，我们将着眼于向 review 主题添加功能。这里有很大的灵活性，没有限制。理解幻想主题的整体结构以及如何操作它的外观将有助于你理解如何成功地构建它。

## 分享这篇文章