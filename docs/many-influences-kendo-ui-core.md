# 剑道 UI 核心的诸多影响

> 原文：<https://www.sitepoint.com/many-influences-kendo-ui-core/>

每一个好主意通常都是现有想法和新想法的结合。这是我们让事情变得更好的方法。

[拱门](http://en.wikipedia.org/wiki/Arch)建筑结构沿用至今。它可以追溯到美索不达米亚，但最早是由罗马人在多种建筑中大量使用。拱门是不可思议的东西，因为它们能够跨越很长的距离，同时通过将力分散到底部，然后向外推到地面来支撑疯狂的重物。

![An Arch](img/669c0548649f4dba001c6e67716a9afa.png) *“得克萨斯州西利市布凌格溪上的 BNSF 铁路三拱桥，1112111435BW”帕特里克·费勒*

拱能够减少建筑所需的材料，增加稳定性，并通过提供在结构顶部和底部移动的空间来最大化可用空间。

我们今天的许多建筑都包含拱门。我们采用了我们所知道的有效方法，并继续将其应用到我们不断发展的设计中。软件与物理架构有如此多的共性。就像机械和土木工程师继续建立在过去的影响上一样，软件总是基于我们所学到的东西和那些对我们影响和启发最大的想法而发展。

## 剑道 UI 核心

![Kendo Logo](img/868d18ce38631a86641087eb7393bc6c.png)

剑道 UI 核心是一个用于 web 和移动的 HTML5 框架，它建立在过去和现在的框架的强大影响之上。我目睹了这些影响在剑道 UI 中扎根，它从一个想法成长为当今最完整的开源 JavaScript 库。了解这些影响对于理解我们如何走到今天非常重要。

## jQuery

![jQuery Logo](img/8e3ab92f1c6fdd9e33f2fad8f93e574b.png)

[jQuery](http://jquery.com/) 是软件的“拱”。无论从哪个角度来看，它都是出色的、易于使用的和多产的。它经受住了毫无根据的表现和[反框架的叫嚣](http://youmightnotneedjquery.com/)，今天仍然是互联网上 69%的顶级 10K 网站的支柱。

当 HTML5 框架的想法在 Telerik 诞生时，我们有机会重新开始。Telerik 十多年来一直在构建 UI 组件，但这次不同。这是第一个不仅仅是一组 UI 小部件的产品。浏览器正在成为首选的虚拟机，我们构建的任何东西都需要满足前端开发人员不断增长的需求。

我们还想把 HTML5 带给各地的开发者，而不仅仅是那些为最新的“常青树”浏览器开发的人。我们希望在完成所有这些工作的同时，提供最具性能的 JavaScript 框架。这就是为什么我们从一开始就决定在 jQuery 之上构建剑道 UI。

jQuery 不是剑道 UI 的可选部分。它是框架的核心，也是剑道 UI 运行的轨道。Kendo UI 实际上使用了 jQuery 库的每一部分，从 DOM 操作到实用方法再到事件委托。回想起来，这是我们做过的最好的决定之一。

jQuery 不仅被证明是一个经得起时间考验的坚如磐石的框架，它还允许我们模块化剑道 UI，以满足开发人员的需求。如果你只需要一个日期选择器，你可以放一个[剑道 UI 日期选择器](http://demos.telerik.com/kendo-ui/datepicker/index)进去，你唯一的承诺就是`$('#element).kendoDatePicker()`。如果你想构建一个完整的[单页面应用程序](http://demos.telerik.com/kendo-ui/websushi/)，使用剑道 UI 完成双向数据绑定，你也可以这样做。jQuery 使这一切从根本上成为可能。

## jQuery UI

jQuery UI 项目可谓是开创性的。正是这个库使得用 jQuery 构建接口变得如此简单。我认识几个从事或曾经从事 jQuery UI 项目的人，我可以向你保证，他们是 JavaScript 领域的精英。

我们从 jQuery UI 中学到了很多。我们完全受到了 jQuery UI 的启发。我们意识到 jQuery UI 可能适合某些人，而有些人可能需要一个框架来提供一些额外的相当复杂的小部件。从 jQuery UI 中学习使我们能够看到什么对 jQuery UI 团队有效，他们面临什么障碍，以及什么[事件塑造了 API](http://blog.jqueryui.com/2011/03/api-redesigns-the-past-present-and-future/) 。

## jQuery Mobile

剑道 UI 也深受 jQuery Mobile 的影响，但不是以您可能会想到的方式。虽然剑道 UI 核心包括剑道 UI Mobile 框架，但 jQuery Mobile 实际上是剑道 UI 声明性 API 背后的影响。

由于我们在内部使用 Kendo UI 来构建其他工具，我们很快发现界面的强制性初始化可能非常难以扩展和维护。Angular 让 HTML 重新流行起来，但是声明式 UI 的魅力远远超出了创建定制 HTML 标签的能力。

您可能熟悉使用命令式初始化创建日历:

```
<div id="dob"></div>
```

```
<script>
  $('#dob').kendoCalendar();
</script>
```

这非常有效，多年来我们一直是这样用 jQuery 初始化 UI 组件的。问题是，我们已经取消了 HTML 在描述用户界面方面的任何作用，我们用“ [div soup](http://www.html5rocks.com/en/tutorials/webcomponents/customelements/) 代替了它如果我们看看初始化同一个日期选择器的其他方法，情况会变得更糟:

```
$('.cal').kendoCalendar();
```

页面上刚初始化了多少日历？答案；全部都是。

最糟糕的例子可能如下:

```
$('<div>').kendoCalendar().appendTo('body');
```

乍一看，能够动态创建 UI 组件并将其添加到页面上看起来非常性感。事实上，这个组件在运行时之前并不存在于 HTML 的任何地方。这使得以后很难发现和改变。即使你是那个写代码的人，你也有可能在六个月后都不记得是你写的。

jQuery Mobile 是第一个使用数据属性来处理这个问题的库。数据属性非常聪明，因为它们是有效的 HTML，你不必担心会踩到实际合法属性的脚趾。使用类似 jQuery Mobile 的基于角色的选择器，可以让 HTML 再次定义 UI 元素和它们的配置。

```
<div data-role="calendar"></div>
```

```
<script>
  kendo.bind('body');
</script>
```

## 迷人的

![Knockout Logo](img/4ecc59c7002fa222e60412db3dbb50e5.png)

HTML 和 JavaScript 对象之间的双向绑定最近非常流行，但它并不总是这样。当《T2》的淘汰赛《T3》上映时，史蒂夫·桑德森远远领先于他的时代。这种以前在 Flex 和 Silverlight 中可用的双向绑定被引入到 JavaScript 中。随着这些相同的框架开始变得晦涩难懂，我们很自然地需要用 JavaScript 来取代它们强大的应用程序功能。

Knockout 是第一个引入 JavaScript 和 HTML 之间双向绑定概念的公司。此外，它是使用`data-bind`属性完成的，这使得它成为合法的 HTML。

我们希望能够在剑道 UI 小部件和 JavaScript 之间添加这种无缝的双向集成。我们想要一个 MVVM 模式。我们喜欢 Knockout 所做的事情，但我们担心人们似乎喜欢在绑定语法中加入逻辑运算符。这导致淘汰项目变得非常非常笨拙。

我们想利用 Knockout 的优点——比如简单的 HTML 绑定——并让它与剑道 UI 插件无缝结合。我们决定推出自己的 MVVM 框架，模仿 Knockout 的最佳部分。

这不是一项容易的任务。此外，我们有很多人对我们没有像其他人一样完全在 Knockout 之上构建剑道 UI 感到非常沮丧。尽管如此，最终的结果是一个邪恶的快速 MVVM 实现，对于那些熟悉 Knockout 的人来说，它很轻而且非常熟悉，对于那些不熟悉 MVVM 的人来说，它也很容易学习。我们还避免了依赖我们无法控制的第三方库。如果你不小心，依赖性会直接转化为技术债务。

## 引导程序

![Bootstrap Logo](img/57354588b8441e602026f7d5171506b4.png)

在去年的过程中，响应式网页设计作为一种合法的移动策略变得越来越明显，我们希望我们所有的剑道 UI 小部件都是响应式的。我们当时正处于十字路口；为剑道界面创建一个布局系统，或者使用一个现有的系统。

一个完整的框架和一个臃肿的框架之间的界限非常细微，很多时候难以区分。每当我们考虑添加特性或功能时，我们必须首先确定是否有人已经做得很好了。如果是的话，而且我们可以在不依赖的情况下集成它，那么增加已经存在的 web 工具的[寒武纪大爆发](http://en.wikipedia.org/wiki/Cambrian_explosion)就没有意义了。这就是为什么我们选择添加[对 Bootstrap](http://demos.telerik.com/kendo-ui/bootstrap/) 的一流支持，作为我们对响应式应用的首选布局框架。

我们一直是 Bootstrap 的忠实粉丝。当框架达到第 2 版时，我们决定在剑道 UI 中添加一个“引导”主题，这样小部件就能适应引导的外观和感觉。当我们发布所有小部件的响应版本(自适应网格/调度程序)时，我们提高了自举承诺并加强了集成。没有比使用 Bootstrap 更简单的方法来启动和运行一个响应性应用程序了，对我们来说，让 Kendo UI 与一个您已经知道的布局框架一起工作比要求您学习另一个框架更有意义。

## 毅力

![Backbone Logo](img/6b28f2b7d7bc5bce457b102401ce4c2c.png)

许多人认为 Backbone 是 JavaScript 框架之父。最初的客户端框架，有一些东西 Backbone 做得非常好——特别是路由和视图。剑道 UI Mobile 一直都有一个路由器、视图和视图管理。我们决定分离框架的这一部分，并通过核心将其作为新的 Kendo UI SPA 框架的一部分公开。

今天的剑道 UI SPA 很大程度上受到了 Backbone 和 Backbone 牵线木偶项目的影响。碰巧的是，[吴镇男·贝利](https://twitter.com/derickbailey)(《木偶》的作者)当时正在为剑道 UI 工作，他能够围绕我们的实现提供许多有价值的见解。我们今天所拥有的是一个非常简单易用的 SPA 框架，对于主干网开发者来说感觉相当熟悉，对于那些发现主干网有点令人畏惧的人来说也很容易学习。

例如，您可以这样声明主干视图:

```
var HomeView = backbone.View.extend({
  tagName: "li",
  className: "row",

  events: {
    "click .badge":         "open",
    "click .button.save"    "save"
  },

  initialize: function () {
    // some initialization code
  }

  render: function () {
    // draw the view to the container
  }
});

var homeView = new HomeView();
```

剑道 UI 视图被抽象得更抽象一些，允许您将模型附加到视图上。模型是一个可观察的对象，它将被绑定到视图。这意味着视图模板中的所有绑定都被接受，包括剑道 UI 小部件及其创建。

```
var template = '<div data-role="kendo.mobile.ui.NavBar">' +
                '<div data-role="viewTitle" data-bind="html: title">' +
                '</div>';

var headerModel = kendo.observable({
  title: 'Home'
});

var headerView = new kendo.View(template, {
  tagName: 'header',
  model: headerModel,
  init: function () {
    // some initialization code
  }
});

headerView.render('#app');
```

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔[剑道 UI SPA](http://codepen.io/SitePoint/pen/AwFhb/) 。