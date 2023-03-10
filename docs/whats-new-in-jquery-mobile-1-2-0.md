# jQuery Mobile 1.2.0 有什么新特性？

> 原文：<https://www.sitepoint.com/whats-new-in-jquery-mobile-1-2-0/>

最近发布了备受期待的 jQuery Mobile 新版本。让我们来看看在这个 1.2.0 版本中增加和改变了什么。在整个代码示例中，您还将学习如何处理这些新特性，并利用它们来改进您的移动应用程序。

jQuery Mobile 是一个轻量级的、有用的、易于学习的移动框架，由<q>一个统一的用户界面系统定义，可以在所有流行的移动设备平台上工作。</q>我必须承认，当我开始研究和使用它的时候，我就爱上了它。我将重点介绍最新版本的新功能，但如果您不知道 jQuery Mobile 是什么或者您需要一个起点，我建议您阅读 [Hello jQuery Mobile](https://www.sitepoint.com/hello-jquery-mobile/ "Hello jQuery Mobile") 和[在 jQuery Mobile 上构建人员目录](https://www.sitepoint.com/building-a-staff-directory-on-jquery-mobile/ "Building a Staff Directory on jQuery Mobile")。

请记住，最新版本也解决了前一个版本的许多错误。如果您需要变更的完整列表，请参考 1.2.0 的[官方变更日志。请注意，我将使用“虚拟页面”这一术语来强调 jQuery 移动页面和物理页面之间的区别，物理页面是一种类似 index.html 或 page.php 的文件。](http://jquerymobile.com/blog/2012/10/02/announcing-jquery-mobile-1-2-0-final/#changelog "jQuery mobile official changelog")

### 兼容性

首先要强调的是 jQuery Mobile 1.2.0 增加了设备兼容性。实际上，这些操作系统或浏览器中的大部分已经在 1.1 版本中运行了，但是 jQuery Mobile 现在拥有了无与伦比的“A 级”兼容性。“A”级意味着对基于 Ajax 的动画页面转换的全面、增强的体验。如果你需要更多关于这些等级的信息，你可以阅读[移动分级浏览器支持](http://jquerymobile.com/gbs/ "Mobile Graded Browser Support")。
完全兼容的手机操作系统完整列表包括:iOS 6、Android 4.1、Tizen、Firefox for Android、Kindle Fire HD。现在，你可能对 [Tizen](https://www.tizen.org/) 感到困惑。这是一个由 Linux 基金会创建的开源操作系统。为了更准确，我将引用他们的“关于”一页:

> Tizen 是一个开源的、基于标准的软件平台，由领先的移动运营商、设备制造商和芯片供应商支持多个设备类别，包括智能手机、平板电脑、上网本、车载信息娱乐设备、智能电视等。Tizen 提供了一个创新的操作系统、应用程序和用户体验，消费者可以从一个设备带到另一个设备。

您可能知道，jQuery Mobile 依赖于 jQuery 库。jQuery Mobile 的新版本增加了对最新 jQuery 版本 1.8 分支的支持，而在软件现代化的另一端，jQuery 团队放弃了对 1.6 分支的支持。然而，如果您仍然使用旧版本，您的代码应该像以前一样工作，但是请注意，如果发现只影响旧版本 1.6 的错误，它们将不会被修复。

### 弹出

弹出窗口是 jQuery Mobile 最新版本中引入的新特性之一。它们允许您将任何内容(如菜单、小部件或图像)放入浮动在当前页面上方的容器中。创建一个非常容易。事实上，你所要做的就是遵循一个经典的 jQuery 移动虚拟页面的结构，用`data-role="popup"`代替`data-role="page"`。要调用一个弹出窗口小部件，就像一个对话框，你必须放置一个指向弹出窗口 ID 的链接并添加`data-rel="popup"`。下面是一个如何调用弹出窗口的例子。

```
<a href="#helloPopup" data-rel="popup">Basic Popup</a>
```

这就是它如何类似于一个基本的弹出窗口。

```
<div data-role="popup" id="helloPopup">
  <header data-role="header">
    <h1>Popup</h1>
  </header>
  <div data-role="content">
    <p>Hello! I'm a brand new popup widget.</p>
    <a href="#" title="Go back" data-role="button" data-rel="back">Close</a>
  </div>
</div>
```

像几乎所有其他功能一样，popup 小部件有许多有用的选项，如`theme`设置弹出内容的配色方案、`corners`确定弹出窗口是否有圆角，以及可能最有趣的`positionTo,`，它允许您确定在哪里呈现弹出窗口。可能的值有:

*   `origin`，默认，表示基于调用弹出的元素居中；
`*   `window`，在当前窗口居中；*   jQuery 选择器，位于给定元素的中心。`

 `弹出窗口和对话框的主要区别之一是，虽然对话框必须是外部页面和物理文件，但弹出窗口放在同一个文件中，并且在调用它的同一个虚拟页面中。

我必须承认，当我开始使用 jQuery Mobile 时，这个对话框约束让我浪费了很多时间。事实上，那时我没有使用弹出窗口小部件，我觉得拥有一个物理文件是对磁盘空间的浪费。引用文件:

> 通过向页面锚链接添加 data-rel="dialog "属性，任何页面都可以呈现为模式对话框。

虽然文档示例总是显示一个物理文件的链接，但是它们并没有指定它**必须是一个物理文件。然而，由于新的弹出窗口小部件，您将永远不必建立额外的外部文件或运行在这个问题上了。**

### 列表

在这个新的 jQuery Mobile 版本中，列表的呈现、修改和操作更加容易。让我们仔细看看。

#### Listview 自动分类器

jQuery Mobile 团队引入了一个新的列表属性，它绝对会让您激动不已。属性是`data-autodividers`，它通过添加字母列表分隔符极大地提高了列表的可读性。好消息是，这个属性可以和`data-filter`一起进一步改进你的应用。因此，如果您在列表中同时使用这两种技术，并且用户在输入框中输入一个字符串来过滤数据，那么分隔线就会相应地改变。当然，它们仍然保持相互独立。要开始使用这个新属性，您的列表视图应该如下所示。

```
<ul data-role="listview" data-autodividers="true" data-filter="true">
  <li><a href="#">Audero.it</a></li>
  <li><a href="#">Aurelio De Rosa</a></li>
  <li><a href="#">BuildMobile.com</a></li>
</ul>
```

要查看完整的示例，请参考 jQuery Mobile 网站上的演示页面。

#### 可折叠列表

从这个版本开始，您将拥有可折叠的列表。点击父列表时，会显示列表中的列表。作为列表，你可以使用所有你已经知道的属性，比如`data-inset`、`data-filter`和前面解释的`data-autodividers`。现在，您将能够显示单个可折叠元素或创建一个手风琴。如果您不熟悉手风琴，它们是可折叠元素的集合，您可以通过用具有属性`data-role="collapsible-set"`的标签包装几个折叠元素来创建。

#### 只读列表

在 jQuery Mobile 中，由于框架的可读性增强，列表非常容易阅读。尽管列表主要用于列出链接，但也用于显示只读列表项。在这个 1.2.0 版本中，美学风格修改现在使用平面背景颜色呈现只读列表项，以给用户一种不可点击的视觉理解。通过查看[只读列表演示](http://jquerymobile.com/demos/1.2.0/docs/lists/lists-readonly-inset.html "Read-only list demo")，您可以看到这种变化的一个例子。

### 页面加载小部件的不推荐使用的方法和属性

在此版本中，页面加载小部件的一些方法和属性已被弃用。在 jQuery Mobile 中，您曾经使用`loadingMessage`、`loadingMessageTextVisible`和`loadingMessageTheme`等`$.mobile`属性来配置它。现在，应该分别使用`$.mobile.loader.prototype.options`对象的属性`text`、`textVisible`和`theme`来设置它们。所以，下面的配置:

```
$.mobile.loadingMessage = "Please wait...";
$.mobile.loadingMessageTextVisible = true;
$.mobile.loadingMessageTheme = "e";
```

应该这样改变:

```
$.mobile.loader.prototype.options.text = "Please wait...";
$.mobile.loader.prototype.options.textVisible = true;
$.mobile.loader.prototype.options.theme = "e";
```

在显示或隐藏页面加载小部件的方法中实现了其他更改。事实上，在 1.2.0 版本之前，开发人员使用的是`$.mobile.showPageLoadingMsg()`和`$.mobile.hidePageLoadingMsg()`方法。它们现在已经被否决了，所以如果可能的话，你应该使用`$.mobile.loading("show")`和`$.mobile.loading("hide")`来代替。请注意，使用这个新方法，您还可以选择覆盖全局配置，以便在调用这些方法时传递一个配置对象。下面是一个使用本地配置显示小部件的调用示例。

```
// Show the widget using theme swatch "e",
// a custom message and setting the text visible
$.mobile.loading(
   "show",
  {
    theme: "e",
    text: "foo",
    textVisible: true
  }
);
```

### 结论

在本文中，您已经看到了 jQuery Mobile 团队创建的新版本的特性，包括弹出窗口、较小的列表样式改进，以及几个不推荐使用的方法和属性。得益于扩展的设备兼容性，您可以在更多的操作系统和市场中推广您自己的应用程序。` 

## `分享这篇文章`