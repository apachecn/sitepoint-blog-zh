# 基础 6:新的菜单组件

> 原文：<https://www.sitepoint.com/foundation-6-menu-component/>

如果你以前用过 [Foundation](http://foundation.zurb.com/) ，你可能会发现菜单/导航组件使用起来有点乏味。如果你想构建一个图标栏或者水平的侧边栏导航菜单，你需要完全按照它们的样子来使用它们(如果你想要其他的东西，可以定制它们来满足你的需要)。

随着《基础 6》的发布，这一切都变了。在他们迄今为止最大的一次改革中，Zurb 已经成功地简化了过程，这样你就可以和你的开发一起进行了。

在之前的帖子中，我讨论了 Foundation 6 中的许多[新特性。在这篇文章中，我将介绍导航菜单。你会看到 Zurb 团队现在提供了一个精简版，以及几个非常有用的 JavaScript 插件，让菜单系统比以前更好。](https://www.sitepoint.com/whats-new-in-foundation-6/)

![Foundation 6 Menus](img/6e6539ae4712e81c84bf8e63913c3c5d.png)

## 导航菜单简化

[导航组件](http://foundation.zurb.com/sites/docs/navigation.html)在 Foundation 6 中进行了重大改造。在 Foundation 5 中，有大量的菜单可供选择，每个菜单都需要自己特定的标记、设置和样式才能正常工作。

在最新版本中，Zurb 将这些不同的菜单重新整合成一个具有多种配置选项的大菜单。**因此，以下菜单现在消失了**:

*   [内联列表](http://foundation.zurb.com/sites/docs/v/5.5.3/components/inline_lists.html)–用于创建您习惯的标准水平菜单
*   [侧边导航](http://foundation.zurb.com/sites/docs/v/5.5.3/components/sidenav.html)–用于勾勒垂直菜单，通常用于侧边栏或其他垂直位置
*   [图标栏](http://foundation.zurb.com/sites/docs/v/5.5.3/components/icon-bar.html)–用于展示菜单项和图标(有时只是图标)。
*   [子导航](http://foundation.zurb.com/sites/docs/v/5.5.3/components/subnav.html)–用于创建子导航元素，例如链接到单个页面的不同部分。

这些已经被一个叫做菜单的单一组件[所取代。](http://foundation.zurb.com/sites/docs/menu.html)

菜单很简单。你用你的`menu`类的`ul`元素中的`a`和`li`项定义你的标记:

```
<ul class="menu">
  <li><a href="#">Option 1</a></li>
  <li><a href="#">Option 2</a></li>
  <li><a href="#">Option 3</a></li>
</ul>
```

这个新的菜单组件提供了与 Foundation 5 中以前的菜单相同的基本功能，但它们是选项，因此您可以选择您的菜单是垂直菜单、水平菜单还是图标栏等。

## 简单菜单

默认情况下，`menu`类添加了填充、微妙的颜色和其他布局方面来帮助您开始。如果你喜欢自己设计所有的样式，你可以在你的菜单中添加 [`.simple`](http://foundation.zurb.com/sites/docs/menu.html#simple-style) 类，它会去掉大部分的样式。

```
<!-- A basic menu with most styling stripped out -->
<ul class="menu simple">
  <li><a href="#">Vanilla</a></li>
  <li><a href="#">Strawberry</a></li>
  <li><a href="#">Chocolate</a></li>
<ul>
```

## 水平和垂直菜单

默认情况下，菜单将是水平的，这将与 Foundation 5 中较旧的“内联菜单”非常相似。如果你想要一个垂直菜单，只需在菜单中添加 [`vertical`](http://foundation.zurb.com/sites/docs/menu.html#vertical-menu) 类即可。例如，我们可以使用这个特性来创建最新新闻条目的列表。

```
<!-- Basic vertical menu example for showcasing blog items -->
<ul class="menu vertical">
  <li><a href="#">Dynamic Interactivity, 21st December 2015</a></li>
  <li><a href="#">Web Fundamentals, 12th December 2015</a></li>
  <li><a href="#">Intro to CSS, 02nd December 2015</a></li>
</ul>
```

## 子菜单和嵌套

如果你要有子菜单，你最有可能使用[一个菜单插件](http://foundation.zurb.com/sites/docs/navigation.html#menu-plugins)，比如下拉/钻取组件，但是你可以通过添加 [`nested`](http://foundation.zurb.com/sites/docs/accordion-menu.html) 类，轻松地将子菜单向左偏移，用于垂直菜单:

```
<!-- using the nested class to create
     a sub-menu inside a vertical menu -->
<ul class="menu vertical">
 <li><a href="#">Course One</a>
   <ul class="menu vertical nested">
     <li><a href="#">Course One - One</a></li>
     <li><a href="#">Course One - Two</a></li>
   </ul>
 </li>
 <li><a href="#">Course Two</a></li>
</ul>
```

## 图标菜单

图标菜单在以应用程序为中心的设计中非常好用，并且可以用最少的样式轻松实现。

对于一个标准菜单，你可以将你的图标图像添加到一个`<span>`标签中，并将它包装在你的`<a>`标签中。你可以使用一个图像作为你的图标，也可以选择一个[基金会图标字体](http://zurb.com/playground/foundation-icons)元素。

如果你选择图标字体，你需要手动将它包含在你的项目中，而不是使用 span，你可以使用一个带有适当类的`<i>`元素。

```
<!-- Simple icon menu using Foundation's icon font -->
<ul class="menu">
  <li><a href="#"><i class="fi-save"></i> <span>Save</span></a></li>
  <li><a href="#"><i class="fi-x"></i> <span>Delete</span></a></li>
  <li><a href="#"><i class="fi-arrow-left"></i> <span>Back</span></a></li>
  <li><a href="#"><i class="fi-arrow-right"></i> <span>Forward</span></a></li>
</ul>
```

我们所需要做的就是将正确的类名添加到`<i>`元素中，然后我们就可以开始了！

默认情况下，标准布局是一个水平菜单，每个选项的左侧都有图标。如果你想要菜单元素上面的图标，我们可以添加`.icon-top`类。这看起来更像你在移动平台上看到的，上面有一个小图标，下面有文字。

## 下拉菜单、明细菜单和折叠菜单

标准菜单很棒，但有时您需要更健壮的东西来动态处理子元素和交互性。Foundation 6 扩展了先前版本的[下拉菜单](http://foundation.zurb.com/sites/docs/dropdown-menu.html)、[深化菜单](http://foundation.zurb.com/sites/docs/drilldown-menu.html)和[折叠菜单](http://foundation.zurb.com/sites/docs/accordion-menu.html)。这些菜单都可以通过键盘导航来访问，并使用语义标记来简化开发。

因为这些菜单使用 JavaScript 库，所以您需要通过执行以下操作来设置它:

*   如果您的构建包含它，您可以调用`$(document).foundation()`来设置它，或者
*   包括您的特定菜单并创建它。例如，要设置一个向下钻取菜单，您需要定义`var elem = new Foundation.Drilldown(element)`(在包含所需的依赖项之后)。

[下拉菜单](http://foundation.zurb.com/sites/docs/dropdown-menu.html)你应该很熟悉。这是标准的菜单设计，当与父元素交互时(通过悬停或键盘)显示子元素。向菜单添加`data-dropdown-menu`属性将动态触发所有这些。

[向下钻取菜单](http://foundation.zurb.com/sites/docs/drilldown-menu.html)的独特之处在于，它不是在当前菜单的上方或下方显示一个新菜单，而是在当前菜单上滑动一个新菜单，让您“向下钻取” *X* 个级别，直到找到您想要的链接。这在屏幕空间有限的情况下非常有用，比如在手机上。

JavaScript 处理后退按钮的生成和定位。您所需要做的就是将`data-drilldown`属性包含到菜单中的所有菜单元素中。

[折叠菜单](http://foundation.zurb.com/sites/docs/accordion-menu.html)就像它听起来的那样，一个折叠菜单，你可以通过点击一个父项目来切换(显示/隐藏)子菜单。将`data-accordion-menu`属性包含到顶层`menu`中以使其工作。

所有的菜单插件都有自己的功能、事件和选项，您可以进行配置。例如，如果您想在关闭折叠菜单中的子菜单时触发某些功能，您可以挂钩到`up.zf.accordion menu`事件。例如:

```
// do something when the accordion closes
$('.my-accordion-menu').on('up.zf.accordion menu', function() {
  alert('Menu Closed');
});
```

总的来说，这些函数、事件和选项在 Foundation 6 中都得到了简化，所以如果您使用 Foundation 5 实现了这些，您可能想检查一下它们是如何变化的。

## 响应导航

此功能是 Foundation 6 的新增功能。以前，如果您想在不同的设备配置文件上显示不同的菜单样式，您可以定义多个菜单并显示/隐藏它们。有了这个最新版本，您可以创建一个响应菜单，它将根据所使用的断点/设备配置文件而动态变化。

我们首先像往常一样定义菜单:

```
<ul class="menu">
  <li><a href="#">Option 1</a></li>
  <li><a href="#">Option 2</a></li>
  <li><a href="#">Option 3</a></li>
</ul>
```

为了使菜单有响应性，使用`data-responsive-menu`属性并提供以下内容:

*   默认菜单类型(例如可以是`drilldown`、`dropdown`或`accordion`菜单)
*   以屏幕轮廓尺寸为前缀的附加菜单类型。比如`small-dropdown`、`medium-drilldown`、`large-dropdown`等。

提供这个信息将让我们的菜单根据我们传递的值进行转换。如果您希望在较大的设备上有一个标准的下拉菜单，但在较小的设备上又回到垂直的折叠/向下钻取菜单，这是很有用的。

如果您定制了断点类，您可以选择定义更多的菜单组合(例如，在超大屏幕上显示下拉菜单，但在大屏幕或更低屏幕上显示下拉菜单)。

## 栏顶横木

我们不要忘记[顶栏](http://foundation.zurb.com/sites/docs/top-bar.html)组件。像大多数其他部件一样，这个已经被彻底检修过了。

使用顶栏组件，您的主菜单可以有左右两个部分。你可能想把你的主菜单项放在左边，然后把你的搜索和操作按钮放在右边。基本格式如下:

```
<div class="top-bar" id="top-bar">
  <div class="top-bar-left">
    <ul class="dropdown vertical medium-horizontal menu">
      <li><a href="#">Monday</a></li>
      <li><a href="#">Tuesday</a></li>
      <li><a href="#">Wednesday</a></li>
      <li><a href="#">Thursday</a></li>
      <li><a href="#">Friday</a></li>
    </ul>
  </div>
  <div class="top-bar-right">
    <ul class="dropdown vertical medium-horizontal menu">
      <li><a href="#">January</a></li>
      <li><a href="#">February</a></li>
      <li><a href="#">March</a></li>
      <li><input type="search" placeholder="Search"></li>
    </ul>
  </div>
</div>
```

你可以在这个菜单中放入任何你想要的东西，但是你需要格式化它，直到它为你工作为止(例如，让内部菜单“下拉”和“垂直”是有意义的，这样它们在移动设备上也能很好地工作)。

如果你也想有一个菜单切换(这是 Foundation 5 中组件的一部分)，你可以使用[响应切换](http://foundation.zurb.com/sites/docs/responsive-navigation.html#responsive-toggle)功能。

这个的基本运行是，它将在小设备上显示一个菜单切换，显示/隐藏你的菜单。文档中有更多的信息，但是它的默认设置在大多数情况下都能正常工作。

```
<div class="title-bar" data-responsive-toggle="top-bar"
     data-hide-for="medium">
  <button class="menu-icon" type="button" data-toggle></button>
  <div class="title-bar-title">Main Menu</div>
</div>
```

这里重要的是`data-responsive-toggle`属性，它需要与你想要控制的菜单的`id`相匹配(这就是为什么这个显示为`top-bar`)。这将显示一个按钮，该按钮仅在查看小轮廓时切换菜单。

## 菜单示例

下面是一个 CodePen 示例，展示了使用新菜单组件的一些不同方式。如果你想知道如何调整菜单的默认设置和选项，那么[菜单文档](http://foundation.zurb.com/sites/docs/menu.html)是最好的来源。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的 Pen [Foundation 6 菜单示例](http://codepen.io/SitePoint/pen/bEYPmg/)。