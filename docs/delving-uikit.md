# 前端替代方案:深入研究 UIkit

> 原文：<https://www.sitepoint.com/delving-uikit/>

如今，网络上有许多“用户界面框架”。

其中最流行的包括 Twitter 的[Bootstrap](http://getbootstrap.com/)、 [Foundation](http://foundation.zurb.com/) 、 [Pure](http://purecss.io/) 、 [HTML5 样板文件](http://html5boilerplate.com/)和 [Skeleton](http://www.getskeleton.com/) 等等。

它们为构建您的 web 项目奠定了良好的基础，您不必担心自己重新发明基本网格和样式，也不必处理许多标准 HTML 元素样式的浏览器差异。

[![UIkit](img/108e9b236149462ab649b52b91afa65d.png)](http://www.getuikit.com/)

即使不是全部，也有大部分是基于“移动优先”的。也就是说，他们使用响应性 CSS 来确保你的网站从手机到桌面浏览器看起来都很棒。

有些人可能会认为这是一种懒惰的工作方式，当然也不乏知识渊博的网络专家仍然主张从头开始建立你的网站。

但是这些框架的真正价值是双重的:理想情况下，它们为您的用户带来一致的体验，同时节省您自己做这件事所需的开发时间。方便。

## 那么，UIkit 是什么？

UIkit 是一个 CSS 框架——就像 Bootstrap 和 Foundation——为你的网站提供一个“干净的石板”样式。你所要做的就是放入所需的 CSS，用一些特定的类标记你的 HTML，然后你就可以在几分钟内拥有一个可爱的、简单的、好看的网站！

不用再担心各种现代浏览器之间的不一致。另外，如果你根本不想做任何造型，那就没必要做。UIkit 完全盒装，有一个基本的，但美丽的风格，将适合大多数网站。而且，如果您确实想改变一些东西以更好地满足您的需求，这真的很容易构建。

## So, Why UIkit?

这些年来，我自己也使用过许多这样的框架，但主要集中在 Bootstrap 上，因为它在 2011 年作为开源项目首次发布。

感觉棒极了！

只要我使用正确的类和标记，甚至包括它自己的图标(Glyphicons ),它让我构建的所有东西都变得更快更容易。

当 Bootstrap 3 发布的时候，对我来说是一个转折点，因为我觉得浏览所有我使用它的网站，重新设计 HTML 以适应引入的类名和布局的变化是一件很麻烦的事情。

所以我寻找新的东西。

输入 [UIkit](http://getuikit.com/ "Website: UI Kit") 。这不是我第一次尝试，但它给我留下了持久的印象，不仅因为它的易用性、相当语义化的类名和过多的选项，还因为它“开箱即用”的风格干净、简洁和现代。我喜欢。

对于网站的特定部分，比如表单和表格，UIkit 用它的[组件](http://getuikit.com/docs/components.html)来处理，其中有足够多的组件可以满足大多数情况下的需要(随着项目的不断发展，还会添加更多的组件)。

例如，布局的标准网格被称为组件，按钮、警告甚至模态也被称为组件。

## 它是做什么的？

![UIKit's Contact template](img/850bf934643e5787af50f6ecda7a8681.png)

UIKit 的联系我们模板

UIkit 自我描述为'*一个轻量级和模块化的前端框架，用于开发快速和强大的 web 界面*，并基于移动优先的风格工作，然后一直工作到桌面。

它确实如主页上所说的那样。它避开了前端设计师

下载了存档文件，解压，然后用它建立了我的第一个网站，我惊讶于它是如此简单！

这些类不是应用一般的类来指定一个元素应该占据多少列(例如 Bootstrap 2 的`‘<span4>’`类，它占据了页面的三分之一——4/12 = 1/3 ),而是指定*一个元素应该占据父*的多少比例。

你可以在这里看到例子。但是一般来说，如果你想让一个元素占据父元素的一半，你可以给它一个类`uk-width-1-2`。是所有基于宽度的测量的助手类，所以你只需要把你需要的修改器放在最后，瞧！

考虑下面的[例子](https://jsfiddle.net/ES9kj/):

[https://jsfiddle.net/ES9kj/embedded/result,html,css](https://jsfiddle.net/ES9kj/embedded/result,html,css)

<br>

```
<div class="uk-container">
    <div class="uk-grid">
        <div class="uk-width-1-3"></div>
        <div class="uk-width-1-3"></div>
        <div class="uk-width-1-3"></div>
    </div>
</div> 
```

我们刚刚创建了一个包含三个元素的容器，占据了三分之一的空间。

这些也不一定要和`<div>`一起使用，任何块级元素都可以，比如`<section>`或者`<article>`。

该库使用`box-sizing`来确保宽度保持一致，无论对它们应用什么填充，所以你可以定制这些元素的填充和边框来满足你的需求，而不用担心它会弄乱显示。

然而，需要注意的是，因为这个库在正确布局页面方面工作得非常好，所以建议您不要更改这些主要的库元素(就像您不会更改其他库一样)，而是在其中一个`uk-width-*`元素中添加一个元素，并将其样式化。

在上面的[示例](https://jsfiddle.net/ES9kj/)中，我使用了来自 panel 组件的元素，它基本上被用作创建面板的可视化布局工具。UIkit 添加了三种默认面板样式，但是您可以通过创建指定`background-color`和`color`的类来轻松添加自己的样式。

其他布局组件包括“文章”、“评论”和一个“实用工具”组件，用于设计其他组件未涵盖的内容，如边距、对齐和标题修饰符。那里有很多可以玩的东西。

除了布局组件之外，还有那些列在“通用”下面的组件，如果你使用过其他 CSS 框架，这些组件你可能会很熟悉。像警报、徽章、按钮和进度条等东西都包括在内。查看 UIKit 网站上的[示例](http://www.getuikit%20.com/docs/button.html)以了解它们的运行情况。

## 我如何使用它？

与大多数框架相同；唯一的要求是一个 CSS 文件(和 Javascript 文件，如果你想使用这些组件的话)。你还需要来自 [Font Awesome](http://fontawesome.io) 的字体，因为框架将这些字体作为任何图标的标准。以下 HTML 将帮助您入门:

```
<!DOCTYPE html>
<html>
<head>
    <title>My UIkit  Document</title>
    <link rel="stylesheet" href="css/uikit .min.css">
</head>
<body>
    <!-- Your document starts here -->
    <script src="js/jquery.min.js"></script>
    <script src="js/uikit .min.js"></script>
</body>
</html> 
```

就是这样！开始用 UIkit 类编写你的 HTML 代码，你马上就能拥有一个网站了！

## JavaScript 也是？

UIkit 附带了许多现成的 JavaScript 资源。它们中的许多都是这类大多数框架的标准，但是并没有因此而降低效率。我特别喜欢的 JavaScript 组件之一是“Off Canvas”，这是一种非常简单的方法，可以将导航系统添加到您的网站，特别是对于移动用户来说，它可以包含您想要的任何内容，但在单击某个操作之前不会出现在页面上。引入它的动画也非常好。[这里有一个小演示](https://jsfiddle.net/cWFT7/)它做什么，下面是代码样本:

```
<div class="uk-container">
    <div class="uk-grid">
        <div class="uk-width-1-1">
            <div class="uk-panel uk-panel-box">Added panels to highlight the grid sections. <button class="uk-button uk-button-primary" data-uk-offcanvas="{target:'#offcanvas'}">Click for off canvas</button></div>
        </div>
    </div>
</div>
<!-- This is the off-canvas sidebar -->
<div id="offcanvas" class="uk-offcanvas">
    <div class="uk-offcanvas-bar">
        <div class="uk-panel">
            <p>Some off canvas text.</p>
        </div>
    </div>
</div> 
```

如您所见，要让按钮调用画布外元素，您只需在按钮上使用一个数据属性，这就是所有 JavaScript 组件的工作方式。这是赋予元素额外功能的一种非常简单明了的方式。作为另一个例子，如果你想给任何元素添加一个工具提示，你需要做的就是给它一个标题并添加一个数据属性`data-uk-tooltip`，它就在那里了！

UIkit 的一组特性中最近增加了一个新特性，一些不错的 JavaScript 插件也加入了进来。标准的日期和时间选择器真的很好，通知附加组件干净、简单、有效。同样，查看[文档](http://getuikit.com/docs/addons.html)，获取您需要的所有示例和信息。

## 主题

有几个主题可供 UIkit 使用。它们都很干净简单，可以很容易地从 UIkit 网站的[定制器](http://getuikit.com/docs/customizer.html)部分下载。

只需在下拉列表中选择你想要 CSS 的主题，然后选择是只下载 CSS，还是少下载一些。我个人更喜欢默认的风格，即扁平和方形，但对于那些喜欢渐变和圆角的人来说，还有几个不同的选项。

看起来开发者也在寻找创建或者编译一个其他人已经创建的主题列表。你可以在[这个 GitHub repo](https://github.com/uikit/uikit-showcase/tree/master/images) 里看到那些截图。但是 UIkit 的美妙之处在于它的可扩展性。

![citybaseapartments](img/e623613300b137c3c1faa55de2cc1155.png)

城市公寓建立在 UIkit 上

我的最新项目是为一家在线公寓预订公司重新设计网站，我使用 UIkit 作为核心 CSS 和 JavaScript 框架。

然而，正如你从那个链接中看到的，这个网站看起来一点也不像任何一个主题。

我总是发现基于 Bootstrap 的网站的一个问题是，它们看起来总是像是用 Bootstrap 构建的，除非在一个网站上做了大量的工作，否则你可以很快看出它是基于什么框架构建的。

## 结论

![website: https://www.de4emusketier.nl/](img/1c5ef8093bee62878c3b6940cca36ca3.png)

另一个基于 UIkit 的前端([sticht ing de 4e shover ier](https://www.de4emusketier.nl/ "Stichting de 4e Musketier. ")

像 UIkit 这样的工具可以做一些奇妙的事情。它已经成为我最近所有 web 开发项目的首选工具，因为它没有花时间去学习这些类是如何工作的，以及在它的实现中使用哪些语义。

网站的默认风格看起来很棒，不需要任何构建。但是如果你需要的话，这很容易做到。甚至 JavaScript 组件也使用相同的样式标准，所以很容易修改。

所有的 JavaScript 都可以通过 HTML 标签上的数据属性轻松调用，不需要任何额外的 JavaScript。但是如果您愿意，也可以选择从自己的 JavaScript 中手动调用一些东西。

有很多例子，如果你不知道如何做某事，有足够多的社区使用 UIkit，帮助就在不远处。

你也可以为这个项目做贡献，因为它是在 [GitHub](https://github.com/uikit/uikit) 上托管的，并使它变得更好！

所以为什么不试一试，看看你的想法呢？我不认为你们会失望，尽管我知道你们中的很多人倾向于坚持自己所知道的(我也一样！)这一次也许值得尝试一些新的东西。

自从自己玩了之后就再也没有回头过。

## 分享这篇文章