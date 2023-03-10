# 使用 UIkit 构建响应式产品组合

> 原文：<https://www.sitepoint.com/building-responsive-portfolio-uikit/>

有许多工具可以帮助你创建一个美观实用的投资组合，并提供过滤和排序选项。一些例子有[石工](http://masonry.desandro.com/)、[同位素](http://isotope.metafizzy.co/)、[混合料](https://mixitup.kunkalabs.com/)等等。

其中一些工具不能用于商业项目，除非您购买了商业许可证。虽然有其他商业用途的选择，但这些会带来额外的复杂性和代码不一致性。例如，如果你使用 Bootstrap 作为你的前端框架，使用 Masonry 作为你的 portfolio builder 插件，这将需要一些额外的努力来无缝地工作。

如果 Bootstrap 中内置了项目组合构建功能，那不是很好吗？嗯… Bootstrap 不提供这种功能，但是 [UIkit](http://getuikit.com/) 提供！

## UIkit 的动态网格

UIkit 功能丰富，拥有你在其他流行框架中找不到的组件。一个这样的组件被称为动态网格，可以与另一个框架的组件结合使用来创建一个完整的功能组合。这正是我们在本教程剩余部分要做的。

## 设置我们的项目

要创建投资组合，我们需要以下资源:

*   UIkit 的 CSS，包括[粘性组件](http://getuikit.com/docs/sticky.html)的 CSS
*   jQuery
*   UIkit 的主要 JavaScript
*   UIkit 的动态网格和粘性组件的 JavaScript

这里需要注意的是[常规网格组件](http://getuikit.com/docs/grid.html)和动态网格有相同的文件名:`grid.js`或`grid.min.js`。但是它们被分别放在不同的文件夹中，“核心”和“组件”。

## 创建单个投资组合项目

在我们搭建好舞台之后，让我们继续创建一个容器来存放和放置项目组合的项目。

```
<div class="uk-container uk-container-center uk-margin-top">
  <div class="uk-grid-width-small-1-1 uk-grid-width-medium-1-4" 
              data-uk-grid="{gutter: 20}">
    <!-- content... -->
  </div>
</div>
```

我们将一个`div`元素与`uk-container`和`uk-container-center`类一起使用。我们还使用`uk-margin-top`在内容上方添加一些空间。在容器内部，我们创建了一个网格，在列之间有一些间距，这是我们使用`data-uk-grid="{gutter: 20}"`属性指定的。

`uk-grid-width-small-1-1`和`uk-grid-width-medium-1-4`类告诉浏览器在不同的设备上以不同的方式呈现项目。在智能手机上，这些项目将被显示为一列，而在平板电脑和大屏幕设备上，这些项目将被放置在四列中。

现在，让我们为投资组合的项目制作一个原型。下一个代码块创建一个底部带有标题的缩略图:

```
<div>
  <figure class="uk-thumbnail uk-overlay uk-overlay-hover">
    <img class="uk-overlay-fade" 
         src="http://placehold.it/1200x800/1e90ff/fff&amp;text=A+4">
      <div class="uk-overlay-panel uk-overlay-scale uk-flex 
                  uk-flex-center uk-flex-middle uk-text-center">
        <button class="uk-button" data-uk-modal="{target:'#project-details'}" 
                type="button">VIEW MORE</button>
      </div>
    <figcaption class="uk-thumbnail-caption">My Project</figcaption>
  </figure>
</div>
```

为了创建缩略图，我们使用一个具有类`uk-thumbnail`的`figure`元素，并通过向`figcaption`元素添加类`uk-thumbnail-caption`来添加标题。

接下来，我们添加一个带有类`uk-button`的“查看更多”`button`，它将在悬停时显示。

## 向每个项目添加模式覆盖

我们需要 UIkit 的[覆盖](http://getuikit.com/docs/overlay.html)组件中的一些类。

*   首先我们将`uk-overlay`和`uk-overlay-hover`类添加到`figure`元素中。
*   然后，我们将`uk-overlay-fade`类添加到`img`元素中，这最初使图像看起来淡出，并在悬停时淡入。
*   最后，我们用一个带有`uk-overlay-panel`类的`div`元素包装按钮，它充当覆盖图内容的容器——在我们的例子中，是一个按钮。

类强制按钮在悬停时放大。其余的类(`uk-flex uk-flex-center uk-flex-middle uk-text-center`)用于对齐和居中按钮和按钮上的文本。

您可能已经注意到了`button`元素中的`data-uk-modal="{target:'#project-details'}"`属性。这是用来引用模态的，我们马上就要创建。

```
<div id="project-details" class="uk-modal">
  <div class="uk-modal-dialog">
    <a class="uk-modal-close uk-close"></a>
    <div class="uk-modal-header"><h2>My Project Title</h2></div>
    <div class="uk-overflow-container">
      <img src="http://placehold.it/1200x800/1e90ff/fff&amp;text=My+Project+Image">
      <p>Lorem Ipsum...</p>
    </div>
  </div>
</div>
```

为了创建一个工作模式对话框——当按钮被单击时，它将显示特定项目的详细信息——我们执行以下操作:

*   添加一个具有`uk-modal`类和`id="project-details"`属性的`div`元素。我们通过添加一个带有`uk-modal-dialog`类的`div`元素来创建实际的对话框。
*   我们添加一个带有`uk-modal-close uk-close`类的`a`元素来创建一个关闭按钮。
*   为了添加一个头，我们使用另一个带有类`uk-modal-header`的`div`元素。
*   最后，为了在一个可滚动的容器中显示模态的内容，我们添加了一个带有类`uk-overflow-container`的`div`元素。

您可以多次复制和粘贴原型代码，以创建投资组合的其余项目。您可以省略模式对话框的代码，因为它不是必需的。第一个项目的基础知识足以展示所需的功能，没有必要为其他项目重复它。

## 添加过滤和排序

接下来，我们想创建一个带有过滤和排序控件的导航栏。将以下代码放在投资组合项目的标记上方:

```
<nav data-uk-sticky id="items" class="uk-navbar">
  <label class="uk-navbar-content uk-hidden-small">Filter:</label>
    <ul class="uk-navbar-nav uk-hidden-small">
      <li data-uk-filter="" class="uk-active"><a href="#">ALL</a></li>
      <li data-uk-filter="blue"><a href="#">BLUE</a></li>
      <li data-uk-filter="green"><a href="#">GREEN</a></li>
    </ul>
</nav>
```

下面是上面代码中发生的情况:

*   我们使用一个带有类`uk-navbar`的`nav`元素。
*   我们添加了`data-uk-sticky`属性，使导航栏在我们向下滚动内容时保持在顶部。
*   为了添加文本(" Filter:")，我们使用带有`uk-navbar-content uk-hidden-small`类的`label`元素。第二类在智能手机上查看时隐藏文本。
*   我们使用带有`uk-navbar-nav uk-hidden-small`类的`ul`元素来创建过滤控件。
*   对于每个控件，我们使用一个带有对应过滤器名称的`data-uk-filter`属性的列表项。为了显示没有应用过滤器的所有投资组合项目，我们将属性`data-uk-filter`的值留空。此外，为了使“全部”控件被默认选择，我们使用了`uk-active`类。

对于排序控件，我们几乎做了同样的事情，只是有一些小的不同。将这个标记放在`nav`元素中，在过滤控件之后:

```
<label class="uk-navbar-content uk-hidden-small">Sort:</label>
<ul class="uk-navbar-nav uk-hidden-small">
  <li data-uk-sort="numbers">
    <a href="#">NUMBERS <i class="uk-icon-sort-numeric-asc"></i></a>
  </li>
  <li data-uk-sort="numbers:desc">
    <a href="#">NUMBERS <i class="uk-icon-sort-numeric-desc"></i></a>
  </li>
  <li data-uk-sort="letters">
    <a href="#">LETTERS <i class="uk-icon-sort-alpha-asc"></i></a>
  </li>
  <li data-uk-sort="letters:desc">
    <a href="#">LETTERS <i class="uk-icon-sort-alpha-desc"></i></a>
  </li>
</ul>
```

为了添加分类，我们使用了`data-uk-sort`属性。为了进行降序排序，我们只需在类别名称后添加一个`:desc`后缀。为了更加美观和易用，我们还在控件中添加了一些图标。如您所见，您可以创建任意多的类别。

现在，为了让一切顺利，我们还需要做两件事。

*   将动态网格的设置改为:`data-uk-grid="{gutter: 20, controls: '#items', duration: 500}"`。这通过使用来自`nav`元素的`id="items"`将文件夹的项目与控件连接起来，并将动画持续时间设置为 500 毫秒
*   将控件与投资组合的项目连接起来。为此，我们为每个项目包装`div`添加了以下属性:`data-uk-filter`(与过滤控件连接)、`data-numbers`和`data-letters`(与排序控件连接)。

代码如下所示:

```
<!-- item-wrapping div -->
<div data-uk-filter="blue" data-numbers="4" data-letters="A">
  <figure class="uk-thumbnail uk-overlay uk-overlay-hover">
    <img class="uk-overlay-fade" 
         src="http://placehold.it/1200x800/1e90ff/fff&amp;text=A+4">
    <!-- etc... -->
  </figure>
</div>
```

到目前为止一切顺利。但是，为了使投资组合真正具有响应性，我们将增加一个因素。当在智能手机上查看文件夹时，我们希望导航栏上只显示一个菜单图标，而不是控件。当用户点击这个图标时，我们希望控件显示在下拉列表中。为此，我们将在控件后的`nav`元素中放置以下标记:

```
<div data-uk-dropdown="{mode:'click'}">
  <a href="#" class="uk-navbar-toggle uk-visible-small"></a>
  <div class="uk-dropdown uk-dropdown-navbar">
    <!-- where the navbar will go -->
  </div>
</div>
```

总结一下上面的代码:

*   dropdown 是用带有`data-uk-dropdown`属性的`div`元素创建的，其模式被设置为“click”。这意味着下拉菜单将在点击时打开，而不是在悬停时打开。
*   我们在里面放了一个`a`元素，带有一个`uk-navbar-toggle uk-visible-small`类。这就创建了前面提到的菜单图标，并确保它只在智能手机上可见。
*   我们添加了另一个带有`uk-dropdown uk-dropdown-navbar`类的`div`元素。

最后要添加的是控件。为此，我们将以下标记放入我们创建的最后一个`div`中:

```
<ul class="uk-nav uk-nav-navbar">
  <li class="uk-nav-header">Filter:</li>
  <li data-uk-filter="" class="uk-active"><a href="#">ALL</a></li>
  <li data-uk-filter="blue"><a href="#">BLUE</a></li>
  <li data-uk-filter="green"><a href="#">GREEN</a></li>
  <li class="uk-nav-divider"></li>
</ul>
<ul class="uk-nav uk-nav-navbar">
  <li class="uk-nav-header">Sort:</li>
  <li data-uk-sort="numbers">
    <a href="#">NUMBERS <i class="uk-icon-sort-numeric-asc"></i></a>
  </li>
  <li data-uk-sort="numbers:desc">
    <a href="#">NUMBERS <i class="uk-icon-sort-numeric-desc"></i>
  </a></li>
  <li data-uk-sort="letters">
    <a href="#">LETTERS <i class="uk-icon-sort-alpha-asc"></i></a>
  </li>
  <li data-uk-sort="letters:desc">
    <a href="#">LETTERS <i class="uk-icon-sort-alpha-desc"></i></a>
  </li>
</ul>
```

## 结论和演示

这是最终的结果:

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [响应式作品集带 UIkit](http://codepen.io/SitePoint/pen/doyXqB/) 。



## 分享这篇文章