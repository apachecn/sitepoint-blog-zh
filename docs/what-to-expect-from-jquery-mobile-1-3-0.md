# 对 jQuery Mobile 1.3.0 有什么期待

> 原文：<https://www.sitepoint.com/what-to-expect-from-jquery-mobile-1-3-0/>

我写过一篇文章，标题是[jQuery Mobile 1 . 2 . 0 有什么新特性？](https://www.sitepoint.com/whats-new-in-jquery-mobile-1-2-0/ "What’s New in jQuery Mobile 1.2.0?")展示了 jQuery Mobile 团队在其流行的移动开发框架 1.2.0 版本中的新特性和变化。几天前，该团队发布了 1.3.0 版本的测试版。幸运的是，它专注于响应式网页设计，特别是两种不同的响应式表格模式、面板和双手柄范围滑块。在本文中，我将向您展示其中一些新特性的概述。


### 桌子

在 1.3.0 版本的 [jQuery Mobile](https://www.sitepoint.com/javascript/jquery/ "Articles about jQuery Mobile") 中，有两个重要的新增:回流表和列切换表模式。让我们仔细看看这两者。

#### 回流表模式

假设您有一个包含许多列的表，并且您希望在一个小屏幕上合理地显示这些数据。使用基本的响应式设计技术，你会缩小字体，由于字体很小，导致数据表很难理解。在这种模式下，表被分成块，每一行变成一个新表，列变成行。因此，新的小表将具有与以前的列计数一样多的行，并且它将只显示两列:一列用于数据标签(即旧的列标题)，另一列用于值。

要应用回流模式，您需要做的就是将`data-role="table"`属性添加到`<table>`标签，因为`data-mode="reflow"`属性是默认行为。要查看回流模式的运行情况，请看 jQuery 移动网站上的[这个例子](http://jquerymobile.com/demos/1.3.0-beta.1/docs/tables/table-reflow.html "Reflow table mode example")。

#### 列切换表格模式

与回流表模式相比，此模式有不同的方法。事实上，这一次您有机会根据自己的需要隐藏一列或多列，这要感谢在表格顶部注入的一个按钮。当然，这并不是全部的解决方案，因为如果所有的列都显示出来，您将会遇到前面讨论过的同样的宽度有限的问题。要解决这个问题，您可以对一组列进行编程，以便在达到某个宽度阈值时隐藏它们自己。这是通过使用基于优先级管理列的 CSS 媒体查询来实现的。您可以将一个值从 1(最高)到 6(最低)的`data-priority`属性应用到表格的标题(`<th>`)来指定可以隐藏或显示的列。如果不指定优先级，该列是持久的，因此不能隐藏。这意味着它在所有宽度上都可见，并且在列选择器菜单中不可用。除了指定列的优先级属性，您还必须将`data-role="table"`和`data-mode="columntoggle"`属性添加到`<table>`标签中。

要查看列切换表模式的运行情况，请看 jQuery Mobile framework 网站上的[这个例子](http://jquerymobile.com/demos/1.3.0-beta.1/docs/tables/table-column-toggle.html "column toggle table mode example")。

### 嵌板

如果你曾经在 Android 上看过 Google+应用程序，你可能会注意到，一旦你点击屏幕左上角的 Google 图标，就会出现一个漂亮的面板。从这个版本开始，你可以很容易地为你的网站或应用程序实现这样一个面板(使用 Cordova，你可能已经在[中了解到用 HTML5 和 Javascript](https://www.sitepoint.com/series/build-a-location-based-mobile-app-with-html5-and-javascript/ "Build a Location-Based Mobile App With HTML5 and Javascript") 构建一个基于位置的移动应用程序)。这个新的小部件给了你很多选择:你可以把它放在页面的左边(默认)或者右边，并且你有多达三种方式来显示它。默认方法是“显示”，但是您也可以随意使用“推送”和“覆盖”动画。该面板可以通过滑动、点击页面或按键盘上的 Esc 键来关闭。要创建一个面板，只需将`data-role="panel"`属性添加到想要转换成面板的容器(通常是一个`<div>`)中。

通常，您的代码如下所示:

```
<div data-role="page">
  <header data-role="header">
    <h1>Your title</h1>
  </header>
  <div data-role="content">
    <p>Open the panel: <a href="#panel">Panel</a></p>
  </div>
  <footer data-role="footer">
    <h3>Copyright Aurelio De Rosa</h3>
  </footer>

  <div data-role="panel" id="panel">
      <p>The content of the panel</p>
  </div>
</div>
```

查看[页面](http://jquerymobile.com/demos/1.3.0-beta.1/docs/panels/index.html "Panel widget demo")，可以看到面板小部件的演示。

### 范围滑块

新的范围滑块是 JQM 版本 1.3.0 中一个非常好的特性。它允许您创建一个有两个手柄的滑块，您可以移动滑块以适应给定属性的范围。很有用。例如，如果您有一个预订网站，并且您想让您的用户能够选择价格范围来过滤搜索结果，他们可以使用范围滑块来指定确切的范围。要拥有这个小部件，只需将`data-role="rangeslider"`属性应用于容器，通常是一个由两个`元素组成的`<div>`。`

 `下面是创建范围滑块的代码示例。

```
<div data-role="rangeslider">
  <label for="range-1">Slider 1:</label>
  <input type="range" name="range-1" id="range-1" min="0" max="10" value="0" />
  <label for="range-2">Slider 2:</label>
  <input type="range" name="range-2" id="range-2" min="0" max="10" value="10" />
</div>
```

如果你想看现场演示，请看本页。

### 自动完成列表视图

列表视图增加了一个闪亮的新功能:自动完成。基本版本使用本地数据。要获得自动完成功能，只需将`data-filter-reveal="true"`属性添加到列表中。当搜索字段为空时，它会隐藏列表项，当用户书写时，匹配的元素会显示出来。然而，jQuery Mobile 团队警告不要在大数据集上使用这个特性。此外，如果您想使用远程数据源，您可以使用`listviewbeforefilter`事件在用户输入时动态填充 listview。

为了了解这项功能是如何工作的，[观看本页所示的演示](http://jquerymobile.com/demos/1.3.0-beta.1/docs/demos/listviews/listview-filter-reveal.html "auto-complete listview demo")。

### 一些小改动

#### 对话框关闭按钮选项

从现在开始，如果对话框有一个标题，jQuery Mobile 会自动在标题的左侧添加一个“关闭”按钮。如果你想把它放在一个更像 Windows 风格的位置(右边)，你可以把`data-close-btn="right"`添加到对话框容器中。如果您根本不想要关闭按钮，您可以添加`data-close-btn="none"`属性。

#### 弹出可拒绝选项

在 1.2.0 版本之前，如果您显示了一个弹出窗口，可以通过单击或点击弹出窗口外部来隐藏它。如果您不喜欢这种行为，现在可以通过向弹出容器添加一个`data-dismissible="false"`属性来停止它。很明显，在这种情况下，你必须提供一个替代的方法来消除弹出窗口，否则它会一直存在，给你的用户带来很大的困扰。

#### 新图标

jQuery Mobile 团队增加了两个新图标。他们创建了一个“编辑”图标，用铅笔表示，可以通过添加`data-icon="edit"`来设置，还创建了一个“三栏”图标，可以通过添加`data-icon="bars"`来设置，可以用于新的面板小部件。

#### 清除按钮选项

在这个版本的框架中，你可以将“清除”按钮(通常用于`<input type="search">`元素)添加到任何输入类型中，甚至是像`text`和`number`这样的类型。为了实现这个目标，您必须向元素添加`data-clear-btn="true"`属性。您也可以使用`data-clear-btn-text="my new text"`属性更改“清除”按钮的显示文本。

### 结论

我已经展示了 jQuery Mobile 团队创建的一些新特性，您将在框架的下一个版本中看到这些特性。可能最有趣的新特性是面板部件和表格的响应模式，这将让你创建更好的移动界面和用户体验。然而，请记住，这些并不是唯一的改进，您可以通过阅读官方网站上发布的发布更新来深入研究它们。` 

## `分享这篇文章`