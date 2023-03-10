# 使用 MixItUp 3 JS 库进行动画过滤和排序

> 原文：<https://www.sitepoint.com/animated-filtering-sorting-mixitup/>

对网站的各个部分进行过滤和排序是组织内容的好方法。作品集、相册和博客只是你想要分类的几个例子。为了实现这一点，许多 JavaScript 库可以提供帮助。其中最受欢迎的两个是[混合库](https://mixitup.kunkalabs.com/)和[同位素库](http://isotope.metafizzy.co/)。

在本文中，我将介绍 MixItUp 3(撰写本文时的最新版本)的基本特性，并向您展示在您的项目中利用它所需的所有步骤。为了更好地展示这个库是如何工作的，我还[创建了一个演示](http://codepen.io/SitePoint/full/MJQVEa/)，我将在整篇文章中引用它。

> **注意:**本文于 2017 年 2 月更新，涵盖了 MixItUp 库的 v3

## MixItUp 3 是什么？

如上所述，MixItUp 3 是一个无依赖性的 JavaScript 库，允许您使用基于 CSS 的动画来过滤和排序元素。由 Patrick Kunka 创建，它有许多不同的定制选项和详细的文档。您可以轻松地将其集成到现有布局中。所有需要做的就是瞄准想要的元素，库会为你做繁重的工作。

对于非商业项目，可以免费使用。但是，商业项目需要许可证。更多信息可在页面[获得。](https://www.kunkalabs.com/mixitup/licenses/)

这个库可以在所有现代浏览器中运行(包括 IE10+和一个后备功能，但是在 IE8+中没有动画)。

现在我们已经看到了这个库是做什么的，让我们来看看使用它所需要的步骤。

## MixItUp 3 入门

要开始使用 MixItUp 3，首先必须将它包含在您的项目中。你可以通过访问它的 GitHub 页面来下载它，或者使用像 T2 NPM T3 这样的包管理器。

对于我们的演示，我们将使用第一个选项。所需的脚本将放在结束的`</body>`标记之前，如下所示:

```
 ...
  <script src="/path/to/mixitup.min.js"></script>
</body> 
```

让我们继续我们内容的结构。

## 构建容器

首先，我们将想要排序和过滤的元素包装在一个容器中。每个被包装的元素都应该有一个公共的类名。在我们的例子中，类名是`mix-target`。容器将使用这个类来识别哪些是目标元素。此外，我们为这个容器分配一个惟一的 ID(`mix-wrapper`)。稍后，我们将让它初始化 MixItUp 的一个实例。

下面的 HTML 演示了我刚才描述的内容:

```
<ul class="courses" id="mix-wrapper">
   <li class="mix-target">
      <a href="#">Economics<span>(U)</span></a>
   </li>
  <li class="mix-target">
     <a href="#">Pharmacology<span>(G)</span></a>
  </li>

  <!-- more list items here -->
</ul> 
```

至此，我们已经建立了元素的基本结构。我们现在可以看到过滤和排序是如何工作的了。

## 过滤

我们首先确定内容中的过滤器类别。在这个例子中，我们将使用一些与教育相关的类别:`undergraduate`、`graduate`和`phd`。然后，我们将这些作为类添加到目标元素中。

下面是前两项的标记:

```
 <li class="mix-target undergraduate">
    <a href="#">Economics<span>(U)</span></a>
  </li>
  <li class="mix-target graduate">
    <a href="#">Pharmacology<span>(G)</span></a>
  </li> 
```

接下来，我们定义将显示过滤器项目的点击处理程序。我们将使用`<button>`元素来做到这一点。我们给它们添加了`filter-btn`类和`data-filter`属性。自定义属性的值应该与已经应用于目标元素的类名相匹配。关键字`all`和`none`也是可能的值。

我们的两个过滤器按钮如下所示:

```
<button class="filter-btn" data-filter=".undergraduate">Undergraduate</button>
<button class="filter-btn" data-filter=".graduate">Graduate</button> 
```

默认情况下，当 MixItUp 第一次加载时，它会显示所有的目标元素。这意味着，带有自定义属性`data-filter="all"`的`<button>`被触发。此外，这个库将`programs-filter-btn-active`类应用于这个按钮。这允许我们对所选的过滤/排序选项进行样式化，以表明它已被选中。

下面是我们在演示中如何设计它的样式:

```
.programs button.programs-filter-btn-active,
.programs button.programs-sort-btn-active {
   box-shadow: 2px 2px 2px rgba(0, 0, 0, 0.5) inset, 0px 0px 1px transparent;
   background: #3a9fbf;
   color: white;
} 
```

到目前为止，一次只能激活一个过滤器控件。但是，假设我们想要基于多个过滤器控件进行过滤。例如，在我们的例子中，我们应该能够同时看到本科生和研究生课程。幸运的是，我们可以通过利用切换控件来做到这一点。你可以在[这个 Codepen 演示](http://codepen.io/SitePoint/pen/egVMrd)中看到我们使用切换处理程序的例子。

现在让我们继续排序。

## 整理

我们要做的第一件事是确定我们想要排序的属性。在我们的项目中，这些是`order`和`year`属性。下一步，我们使用它们的名称将定制属性(`data-order`和`data-year`)应用于目标元素。这些自定义属性的值取决于我们想要实现的排序顺序。

两个项目的 HTML 如下所示:

```
<li class="mix-target undergraduate" data-order="5" data-year="4">
   <a href="#">Economics<span>(U)</span></a>
</li>
<li class="mix-target graduate" data-order="14" data-year="2">
   <a href="#">Pharmacology<span>(G)</span></a>
</li> 
```

此时，我们将`<button>`元素定义为点击处理程序，并为每个元素添加`sort-btn`类和`data-sort`属性。此属性的值应该使用以下约定:

```
<button data-sort="order:asc"> 
```

因此，我们有了用于排序的属性，然后，作为值，我们有一个订单类型，后跟一个冒号以及“asc”或“desc”指示符。

该属性也接受关键字`default`和`random`作为值。另一个有趣的事情是，我们可以同时使用多个属性进行排序。

我们示例中使用的两个排序按钮如下所示:

```
<button class="sort-btn" data-sort="order:asc">Ascending</button>
<button class="sort-btn" data-sort="year:desc order:desc">Descending <span class="multi">(Multi)</span></button> 
```

当 MixItUp 第一次加载时，属性为`data-sort="default:asc"`的按钮被触发。也就是说，元素是根据它们的 DOM 外观进行排序的。类似于默认的过滤器按钮，这个按钮接收一个活动类(`programs-sort-btn-active`)。

现在我们已经介绍了这个库的基本特性，是时候看看如何初始化它了。

## 定制选项

为了在我们的演示中启用 MixItUp 3 的功能，我们使用了 [mixitup](https://www.kunkalabs.com/mixitup/docs/mixitup-factory/) 工厂函数，并向它传递两个参数:

1.  代表我们的容器的选择器
2.  和一个覆盖 MixItUp 默认行为的配置对象。

代码如下:

```
mixitup('#mix-wrapper', {
  load: {
    sort: 'order:asc' /* default:asc */
  },
  animation: {
    effects: 'fade rotateZ(-180deg)', /* fade scale */
    duration: 700 /* 600 */
  },
  classNames: {
    block: 'programs', /* mixitup */
    elementFilter: 'filter-btn', /* control */
    elementSort: 'sort-btn' /* control */
  },
  selectors: {
    target: '.mix-target' /* .mix */
  }
}); 
```

让我们仔细看看我们的定制:

1.  首先，当库加载时，我们改变默认的`active`排序按钮。
2.  接下来，我们为目标元素识别不同的动画效果。
3.  最后，我们向目标元素、过滤器和排序按钮添加定制类。因此，例如，当一个过滤器控件被激活时，添加到它的是`programs-filter-btn-active`类，而不是通用的默认`mixitup-control-active`类。

*注:原始默认值在注释中注明。*

值得一提的是，`mixitup`工厂函数返回 MixItUp 的一个实例，我们可以通过使用它的 [API 方法](https://www.kunkalabs.com/mixitup/docs/api-methods/)来操纵它。

您可以在完整的演示中查看此示例代码的运行[，您可以在下面进行尝试:](http://codepen.io/SitePoint/pen/MJQVEa/)

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [MixItUp 3 JavaScript 库演示](http://codepen.io/SitePoint/pen/MJQVEa/)。