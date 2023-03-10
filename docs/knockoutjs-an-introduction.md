# KnockoutJS:简介

> 原文：<https://www.sitepoint.com/knockoutjs-an-introduction/>

GMail 等复杂网络应用的兴起催生了一大批 javascript 框架。基本上，这些框架允许您在 web 应用程序中创建客户端应用程序。像“MVC”和其他设计模式这样的术语，曾经只适用于 web 的服务器端，现在只适用于代码的客户端。这几天比较流行的框架有 [、http://backbonejs.org](http://backbonejs.org)、 [、http://emberjs.com](http://emberjs.com)、http://knockoutjs.com。本文将对后者进行旋风般的审视。

(注意:我用了很大的意志力才没有把这篇文章命名为“妈妈说击倒你！”或者类似的东西。事实上，我的意志力需要麻醉剂的帮助。)

Knockout 采用[模型-视图-视图-模型(MVVM)](http://en.wikipedia.org/wiki/Model_View_ViewModel) 方法构建应用程序架构。ASP.NET 是一种架构模式，它(部分)起源于微软和他们的 Windows Presentation Foundation/Silverlight/使 Web 表单可用的失败尝试。老实说，由于这些根源，我避免了淘汰，我猜许多开发人员也是如此。直到我继承了一个使用 Knockout 的应用程序，我才被迫真正了解它。

然而，我又一次惊喜地发现，我毫无根据的开发者偏见是非常错误的。

## 至少不是 MVC

查看[淘汰赛主页](http://knockoutjs.com/index.html)，它列出的关键概念是:

*   声明性绑定
*   自动用户界面刷新
*   依赖性跟踪
*   模板

我不打算逐一介绍，因为您对他们的网站有相同的访问权限。简而言之，我会说出我认为它的意思。在我看来，Knockout 的优势在于它的数据绑定。如果您从未使用过将 UI 绑定到模型的框架(在这种情况下是视图模型),那么核心概念是对该模型的任何更改都会立即反映在视图/UI 中。在我看来，Adobe Flex 是最著名的数据绑定框架。数据绑定非常强大，尤其是当您的模型碎片散布在 UI 中时。在一个地方更新一个值并因此改变 10 个 UI 元素会让任何 trollface 发笑。

数据绑定是 Knockout 提供的最甜的调味汁，但不仅仅如此。模板非常强大，您可以使用任何喜欢的 javascript 模板库(jQuery 模板、mustache 等)。Knockout 也是可扩展的，提供了一个插件框架，我们将在演示应用程序中利用这个框架来展示 Knockout。

## 舔它，好好舔它

演示应用程序是一个简单但有点恶心的应用程序，名为 LinkLicker。这个想法是，链接提交，你点击你喜欢的。是的，我是一个 4 岁的孩子。

这个应用程序的服务器端既是一个 Rails 应用程序，也是不相关的。您的服务器端应用程序只需要返回 JSON，所以它可以是 Sinatra 或 Rails 或 Cuba 或其他什么。我不会用 web 应用程序的细节来烦你，但是如果你一定要知道，它们就在 [github 存储库中。](http://github.com/rubysource/linklicker)

该应用程序显示了一个允许提交链接的表单，一个已提交链接的列表，以及一个当前用户已经舔过的链接的列表。当一个链接被点击后，它的图标会改变。我确信，这是下一个 FacebookTwitter。

### 设置

您将需要在您的 web 应用程序中可用的 Knockout 代码来实现这一点。我已经把我的放在供应商目录中，和最常用的淘汰插件放在一起。这个插件是 [Knockout Mapping 插件](http://knockoutjs.com/documentation/plugins-mapping.html)，它承担了将 JSON 从服务器映射到客户端的 javascript 对象的所有工作。注意:在本文中，我不会真正涉及映射插件，但是重要的一点是，当它映射对象时，它会在我们所有的 JSON 属性中创建可观察的对象。你很快就会看到什么是可观的。

从结构上讲，我喜欢将每个“模型”类放在客户机上它自己的文件中。LinkLicker 真的只有一个型号:Link。我还喜欢有一个全局类来表示我的应用程序，我称之为 App。javascript 的最终目录结构如下所示:

![](img/7886e824f48f66a285472a5ba7099c17.png "Screen Shot 2012-12-11 at 7.30.53 PM")

### 用户界面

LinkLicker 的用户界面主要是由我的愿望驱动的，即尽可能少地做一些事情，同时仍然能让大家理解令人惊叹的概念。正如我上面提到的，有一个表单，以及三个链接列表。三个列表都是链接，新的(意思是，未链接的)，舔链接。您可能已经猜到，在任何给定的时间，一个链接可以出现在多个列表中。UI 截图如下:

![](img/6e16dd1e1de890ac6575e47fbbc6a24b.png "Screen Shot 2012-12-09 at 10.54.30 AM")

### 引导程序

自举 LickLinker 包括绑定到表单和创建我们的三个列表。我们将在应用程序中创建所有这些属性。简单的东西。

```
var App = function() {
  this.newLink = ko.observable(new Link());
  this.mylinks = ko.observableArray();
  this.lickedLinks = ko.observableArray();
  this.allLinks = ko.observableArray();
};
```

你可能想知道所有这些**可观察到的**疯狂是什么。当你使一个属性成为可观察的，它就成为一个超级强大的属性变化的通知者。Knockout 会跟踪(尽最大努力，而且它很擅长)对你的房产感兴趣的东西，并在它发生变化时通知你。淘汰赛网站上的 [Observables docs](http://knockoutjs.com/documentation/observables.html%20) 很棒，也有互动教程可以使用。

简而言之，如果你能提前考虑，当我们将 UI(表单)绑定到新链接时，它会通知我们的表单发生了变化，反之亦然。

其他三个属性是 **observableArrays** 。从概念上讲，它们与 observables 完全相同，只是它们作用于集合。observableArray 将跟踪数组中的对象，它有许多帮助方法来添加和删除这些对象。您可以轻松地将项目弹出或推送到 observableArray 上，对其进行切片，获取其长度，反转它，以及您可以想到的许多其他方法。

同样，由于 observables 和 observableArrays 是变化的通知，当我们将 UI 中的列表绑定到这些链接列表时，所有内容都将被更新以反映最新的状态。希望这个概念正在深入人心。

#### 模型

LinkLicker 的模型只是一个链接，它有 3 个属性:url、描述和 lickedByCurrentUser。

#### 表单

Knockout 是关于数据绑定的，记得吗？因此，我们可以将一个新的链接绑定到表单，然后，当用户输入数据时，它将填充这个新绑定的链接。在上面的代码片段中，我们将 newLink 属性创建为一个可观察对象。现在，将它绑定到表单:

```
<form id="create_form" class="one-third column alpha" data-bind="submit: createNewLink"><!-- ko with: newLink -->

 <label for="link_url">Link:</label>
 <input id="link_url" type="text" name="linkhttps%3A%2F%2Feditor.sitepoint.com" data-bind="value: url" />

 <label for="description">Description:</label>
 <input id="link_description" type="text" name="link[description]" data-bind="value: description" />

 <!-- /ko -->
 <input type="submit" value="Add" /></form>
```

在上面的代码片段中，关注`<!-- ko with: newLink -->`行。Knockout 提供了几种不同的方式将 DOM 的一部分绑定到视图模型。这个例子使用了“无容器绑定”，也就是说，它没有为这个声明创建 DOM 元素。

另一种方法是创建一个 DOM 元素(比如一个`div`)并添加一个指定模板的`data-bind `属性。如果你感到困惑，在后者的淘汰赛中有很多例子。

那个`ko with: newLink`告诉 Knockout 找到这个视图绑定到的任何对象的 newLink 属性，并将块中的所有内容都作用于它。如果你看一下`input`标签，这应该会更清楚一点。每个输入标签都有一个`data-bind`属性，该属性将输入的值绑定到 newLink 上的一个属性。

#### 束缚的纽带

有了我们的 UI，我们需要一些机制将它绑定到我们的视图模型。在这个应用程序中，视图模型是我们在客户端的全局应用程序类。加载页面时，应用程序会绑定到视图，如下所示:

```
$(function(){
  App.globalApp = new App();
  ko.applyBindings(App.globalApp);

  App.globalApp.loadLinks();
});
```

很简单。

过滤列表可以通过创建两个“computeds”(以前的 dependentObservables)来完成。来，让我给你看看:

```
this.allLinks = ko.observableArray();

this.lickedLinks = ko.computed(function() {
  return this.allLinks().filter(function(link) {
    return link.isLicked();
  });
}, this);

this.newLinks = ko.computed(function() {
  return this.allLinks().filter(function(link) {
    return !link.isLicked();
  });
}, this);
```

A `computed`是一个依赖于其他可观察值的可观察值。您可以看到，我们根据每个链接的 isLicked()值过滤了所有链接 observableArray。computed ( `this`)的第二个参数是绑定函数的上下文。

电脑非常有用，你会发现自己在任何地方都在使用它们。

#### 扩展敲出 JS

虽然 Knockout 有很多很多你可以使用的绑定，比如 text，html，css，visible，但是你会发现有些场合需要一个新的绑定。编写自定义绑定非常简单。例如，我将编写一个与`visible`绑定相反的`hidden`绑定。它在下面:

```
ko.bindingHandlers.hidden = {
  update: function(element, valueAccessor){
    var value = ko.utils.unwrapObservable(valueAccessor());

    ko.bindingHandlers.visible.update(element, function() {
      return !value;
    });

  }
};
```

在绑定中，我获取被绑定元素的值，然后将其传递给现有的`visible`绑定。结果被求反并返回。

显然，这是一个简单的绑定，但是如果需要的话，可以变得非常复杂。例如，Google 将向您展示 jQuery UI 元素的绑定，以及社区创作的数千个其他自定义绑定。

## 妈妈说把这个包起来！

我还想介绍 Knockout 的许多其他方面，但我认为我们已经很好地了解了它的许多特性。希望当您评估 javascript 框架时，这篇文章能帮助您做出决定。

记住，LinkLicker 的全部源代码都是你的，你可以在 [github](https://github.com/RubySource/linklicker) 上浏览、使用、模仿任何东西。

如果你对 LinkLicker 或 Knockout 有任何疑问，请在评论中告诉我。感谢阅读！

## 分享这篇文章