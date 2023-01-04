# Spectre:一个轻量级的 CSS 框架

> 原文：<https://www.sitepoint.com/spectre-a-lightweight-css-framework/>

框架大大减少了项目的开发时间。其中有几个像 [Bootstrap](http://getbootstrap.com/) 非常流行，提供了很多特性，但是你的项目可能不需要所有这些。今天，我们将关注一个叫做[幽灵](https://picturepan2.github.io/spectre)的新框架。它重量轻，现代，反应灵敏，移动友好。当它被缩小和压缩的时候，它重约 6.8kb。除了基本的网格系统，它还有许多其他有用的各种组件，如标签、模态和卡片等。

本教程将提供该框架的简要概述，随后是一些帮助您快速入门的指南。

## 装置

可以直接下载缩小后的 [Spectre.css](https://github.com/picturepan2/spectre/tree/master/dist) 文件，也可以使用 npm 和 Bower 安装。完成后，您可以像常规样式表一样将该文件包含在项目中。

```
<link rel="stylesheet" href="link/spectre.min.css" />
```

您还可以通过编辑`/src`目录中的 Less 文件或者从`spectre.less`文件中删除不需要的组件来创建您自己定制的框架版本。然后，您可以使用 Gulp 从命令行构建您的 CSS 文件。

## 网格系统

Spectre 拥有基于 flexbox 的 12 列响应式网格系统。每列的宽度由其类名决定。每个类都以`col-`开头，然后是一个数字，代表这个特定元素应该有多少列宽。例如，`col-12`有 12 列，宽度为 100%，而`col-3`有 3 列，宽度为`col-12`的四分之一，宽度为 25%。默认情况下，不同的列之间会有一些间隙。您可以通过将类`col-gapless`添加到它们的容器中来缩小这个差距。就像 Bootstrap 一样，它也提供了像`col-md-[1-12]`、`col-sm-[1-12]`和`col-xs-[1-12]`这样的类来帮助你在视窗大小改变时控制元素的宽度。

它还提供了诸如`hide-xs`、`hide-sm`和`hide-md`之类的类来隐藏特定视口大小的元素。

当视窗宽度小于 480 像素时，所有列将显示为一行。`col-xs-*`类将适用于宽度大于 480px 的所有元素。同样，`col-sm-*`适用于 600 像素以上的视口宽度，`col-md-*`适用于 800 像素以上的视口宽度。

下面的代码片段创建了一个宽度为 33.333% ( `col-4`)的列、两个宽度为 25% ( `col-3`)的列和一个宽度为 16.66% ( `col-2`)的列。

```
<div class="container">   
  <div class="columns">     
    <div class="column col-4">
      <div class="col-content">col-4</div>
    </div>       
    <div class="column col-3">
      <div class="col-content">col-3</div>
    </div>       
    <div class="column col-3">
      <div class="col-content">col-3</div>
    </div>     
    <div class="column col-2">
      <div class="col-content">col-2</div>
    </div>
  </div>
</div>
```

在下面的演示中，我创建了一个基本的网格系统和一个嵌套系统。从演示中可以看出，创建复杂的布局并不难。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [Spectre 框架网格系统](http://codepen.io/SitePoint/pen/wWLaGL/)。

## 基本要素

Spectre 包括一些印刷元素的默认样式，如标题、段落和引号。该框架还针对亚洲字体进行了优化。它有几个类似于`highlight`或`lead`的类来突出文本的某些部分。下面的演示一次展示了所有这些功能:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[Spectre-排版](http://codepen.io/SitePoint/pen/EyBjyv/)。

您还可以将类`table`添加到任何`<table>`元素中。然后，框架会将基本样式应用到您的表格，如填充和边框样式，以使您的表格具有更整洁的默认外观。它还会适当地设置表格标题行的样式。您可以使用类`table-striped`通过添加类`table-hover`使表格条纹化并启用悬停样式。

这个演示说明了使用 Spectre 创建漂亮的响应表是多么容易:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen[Spectre-Tables](http://codepen.io/SitePoint/pen/GqbJqz/)。

按钮也有一些可用的类。要使用默认的按钮样式，您需要添加`btn`类。您可以使用`btn-sm`、`btn-lg`和`btn-block`类来控制按钮的大小。类`btn-block`将创建全角按钮。要将多个按钮组合在一起，可以在它们的容器上使用`btn-group`类。

Spectre 还为常见的表单元素(如标签、输入字段和文本区)提供了样式规则，使它们看起来干净而时尚。要创建水平表单，您需要将`form-horizontal`类添加到`<form>`元素中。然后你可以使用`col-[1-12]`中的一个类来控制子元素的宽度。请注意，只有当视窗宽度至少为 840 像素时，表单才会是水平的。让表单元素变大或变小只是添加类`input-sm/input-lg`或`select-sm/select-lg`的问题。

通过将类`input-group`添加到输入容器中，还可以将一些文本或按钮附加到输入元素中。您必须将类`input-group-addon`添加到附带的文本元素中，并将`input-group-btn`添加到按钮元素中。

本演示展示了一个基本表单:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[Spectre-Forms](http://codepen.io/SitePoint/pen/OXZVRY/)。

## 航行

即使是最基本的网站，导航也是不可或缺的一部分。记住这一点，Spectre 提供了三个导航组件——导航栏、垂直菜单和面包屑。导航栏可以包含徽标、导航链接、按钮等元素以及搜索框等其他元素。默认情况下，导航条会有一个非常简单的样式，所以你不需要花费很多精力来定制它。下面是创建基本导航栏的标记:

```
<header class="navbar">
  <section class="navbar-section">
    <a href="#" class="navbar-brand">SitePoint</a>
  </section>
  <section class="navbar-section">
    <a href="#" class="btn btn-link">HTML5</a>
    <a href="#" class="btn btn-link">CSS3</a>
    <a href="#" class="btn btn-link">JavaScript</a>
    <a href="#" class="btn btn-primary">Sign Up</a>
  </section>
</header>
```

除了导航栏之外，您还可以创建垂直菜单。这要求您将类`menu`添加到相应的容器元素中。您可以将类`menu-item`添加到菜单中的所有子元素中。框架使用这个类来添加自己的样式，与其余组件保持一致。使用`divider`或`menu-header`可以分隔不同的菜单项。

您还可以使用`breadcrumb`类实现面包屑菜单。所有子元素都需要有类名`breadcrumb-item`。

我们在本节中讨论的所有三个菜单都显示在下面的演示中:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen[Spectre-Menus](http://codepen.io/SitePoint/pen/bZPdgb/)。

## 情态动词和卡片

到目前为止，我们还没有讨论的两个重要组件是模态和卡片。它们与它们的 Bootstrap 对应物相似。

### 模型

您可以向容器元素添加一个`modal`类来创建模态。在该容器中，您可以添加一个带有类`modal-container`的实际模态容器元素和一个带有类`modal-overlay`的模态覆盖元素。在你的容器中，你可以用`modal-header`、`modal-content`和`modal-footer`类添加实际的元素。您可以使用`modal-sm`类来控制模态的大小。下面的代码将为您创建一个基本的模式:

```
<div class="modal modal-sm active">
  <div class="modal-overlay"></div>
  <div class="modal-container">
    <div class="modal-header">
      <button class="btn btn-clear float-right" type="button"></button>
      <div class="modal-title">Modal title</div>
    </div>
    <div class="modal-body">
      <div class="content">
        <p>This is some text inside the Modal.</p>
      </div>
    </div>
    <div class="modal-footer">
      <button class="btn btn-primary" type="button">Close</button>
    </div>
  </div>
</div>
```

在下面的演示中，我添加了使模态出现和消失的功能。这只是添加和删除`active`类的问题。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[Spectre-Modals](http://codepen.io/SitePoint/pen/PzAqWv/)。

### 卡片

卡片是灵活的通用内容容器。你可以用它们来存放各种各样的东西。它们可以通过使用类`card`来创建。卡本身可以根据它们的目的拥有带有类别`card-header`、`card-content`、`card-footer`或`card-image`的子卡。以下是显示典型卡片标记的基本片段:

```
<div class="card">
  <div class="card-image">
    <img class="img-responsive" src="img-path/bp.jpg">
  </div>
  <div class="card-header">
    <h2 class="card-title">Heading</h2>
    <p class="card-meta">Some Meta Data</p>
  </div>
  <div class="card-body">
    <p>Something related to the image or the heading goes here!</p>
  </div>
</div>
```

你可以把卡片包装成像`col-6`和`col-md-8`这样的类。来控制它们的宽度。如果需要的话，你也可以在卡片内放置按钮或标签等元素。下面的代码笔演示展示了两张不同位置的图像卡:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[Spectre-Cards](http://codepen.io/SitePoint/pen/bZPdRG/)。

## 更多组件

其他组件如“祝酒词”类似于 Bootstrap 中的警告。toast 组件可以包含文本和其他图标。您还可以选择向它们添加关闭按钮。敬酒可以用于不同的目的。有一些类可以满足这些目的。为了庆祝成功，你可以使用类`toast-success`。这将使烤面包变绿。类似地，也有错误消息类(`toast-danger`)和一般信息类(`toast-primary`)。

Spectre 还有其他常见组件，如徽章、标签、分页和工具提示。你可以访问[官网](https://picturepan2.github.io/spectre/#components)了解所有这些组件。

此外，这个框架还有一些实用类，比如`text-left`、`text-lowercase`、`float-left`、`centered`，可以帮助你做一些小的布局调整。实用程序类`float-right`可以在上面的模态演示中看到，它被应用到模态标题中的小`x`按钮上。

一个更有用的类是`loading`，它可以被添加到按钮或 div 来显示加载动画。

## 最后的想法

Spectre 的一个好处是你不必覆盖很多默认样式。这个框架只是为元素提供了最小的样式，这本身就很不错了。考虑到它提供的组件数量，大小也非常合理。Spectre 唯一的缺点是，它不提供任何 JavaScript 来为其所有组件添加交互。这意味着你将不得不编写自己的代码来显示或隐藏模态框和其他类似的交互，但这并不难实现。

该框架绝对值得一试。你对 Spectre 有什么看法？你计划在你的项目中使用它吗？请在评论中告诉我们。