# 使用 Backbone.js 构建自己的框架

> 原文：<https://www.sitepoint.com/roll-your-own-framework-with-backbone-js/>

让我们在这里做一个集体决定。我们将不再把 A 级手机视为外星人，而是看到它们的本来面目:强大、有能力的设备，运行强大的渲染引擎。

在你的应用中加入一个框架，比如 jQuery Mobile，Sencha，或者任何能给你带来流行的类似 iOS 的原生外观和感觉的东西，立刻就能让你在设计和功能上有所选择。你的应用程序变成了一个麦当劳汉堡:和一堆其他 B 端应用程序一样，看起来和感觉都比原生应用程序稍差。你可以做得更好，利用这些设备中的浏览器所提供的功能。

我们将看看如何做到这一点，使用 HTML5、CSS3 和 [Backbone.js](http://documentcloud.github.com/backbone/) 采取极简的方法。对于桌面网络应用程序来说，极简主义现在很流行(你的里程数可能会有所不同)，但在移动设备上，好处是巨大的，更重要的是，是显而易见的。

将你的 iPhone/Android 指向[http://bm-example.heroku.com](http://bm-example.heroku.com)，玩一玩。这个例子取自我一直在做的一个应用程序，*非常*慢，有一段时间了。这个实现又是基于 [Zepto.js](https://github.com/madrobby/zepto/tree/master/examples/iphone) 附带的一个例子。

### 重要的事情先来

我们先来了解一下标记。我们应用程序中的每个“页面”都由一个`<section>`标签定义。导航将包括我们从一个部分跳到另一个部分，因此在任何时候都只有一个部分是可见的，这是几乎每个移动 web 框架在现实中的工作方式，尽管我们这样做的方式非常简单和语义化。

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
  </head>
  <body>
    <section id="home"></section>
    <section id="settings"></section>
    <section id="shop-1"></section>
  </body>
</html> 
```

这是 CSS 中处理导航的部分。我省略了美学部分以保持讨论的重点，但是你可以在[build mobile GitHub backbone . js](https://github.com/buildmobile/backbone.js)repo 上看到整个代码，而不仅仅是 CSS。

```
* { margin: 0; padding: 0; outline: 0; -webkit-box-sizing: border-box }

body {
  position: absolute;
  height: 100%;
  width: 100%;
  overflow-x: hidden;
  -webkit-perspective: 800;
  -webkit-text-size-adjust: none;
  -webkit-transform-style: preserve-3d;
  -webkit-user-select: none;
}

body > section {
  position: absolute;
  left: 0;
  width: 100%;
  opacity: 0;
  -webkit-transition: all 0.25s ease-in-out;
  -webkit-transform-style: preserve-3d;
  -webkit-transform: translate3d(100%,0%,0%);
}

body > section.current { -webkit-transform: translate3d(0%,0%, 0%); opacity: 1 } 
```

上面大致说了:

*   在每个元素上，将边距、填充和轮廓重置为零。此外，让元素的宽度由边框的位置来定义，而不是它的内容。最后一个设置在移动环境中特别方便，因为会有很多元素被允许自由流动到屏幕的最大宽度。如果我们向其中任何一个添加填充并忽略它，就会发生溢出。
*   将主体拉伸到最大宽度，同时拉伸每个紧邻的子部分。
*   默认情况下，每个部分都是隐藏的。只有包含类`current`的部分可见。
*   我们希望允许垂直滚动，同时保持水平滚动。
*   每个可转换的属性都应该发生转换。这将帮助我们在页面之间创建一个很酷的过渡效果。

这听起来有点疯狂，但这确实是我们的应用程序在构建其部分时所做的全部工作。但是在我们能按下按钮从一个地方到另一个地方之前，我们没有多少。因此，让我们通过主干路由器和一些`pushState`行动来实现这一点。

```
var Router = Backbone.Router.extend({
  routes : {
    ''            : 'home',
    '/'           : 'home',
    '/shops/:id'  : 'shop',
    '/:id'        : 'page'
  },

  hideAll : function() {
    $('body > section').removeClass('current');
  },

  home : function() {
    this.hideAll();
    $('#home').addClass('current');
  },

  page : function(id) {
    this.hideAll();
    $('#' + id).addClass('current');
  }
});
App.placeAnchorHooks();
Backbone.history.start({ pushState: true }); 
```

我前面说过，一次只能看到一个部分。这是通过将 CSS 类`current`分配给可视部分来实现的。因为默认情况下每个部分都是隐藏的，正如我们之前写的 CSS 一样，所有其他部分都是隐藏的。`hideAll`方法将`current`类从拥有它的任何部分中移除，所以我们在任何其他部分分配它之前先调用它。

我们定义了三条路线。一个主页路径，这是你进入应用程序后首先看到的，一个匹配我们要浏览的商店的路径，以及一个当我们指定其他任何内容时会出现的总括路径。

请注意，如果这是一个小册子类型的应用程序(只是单独的部分和导航)，我们不需要任何其他来自主干除了路由器。这是这个框架背后的极简主义精神的一大进步。你可以使用你想要的部分，而不使用其他部分。

### 连接

因为我们使用的是[推状态](http://diveintohtml5.info/history.html)，所以我们不是在讨论技术意义上的链接。它们不是锚点，锚点使用`href`属性让浏览器发出新的请求，`pushState`是另外一种东西:你依靠 JavaScript 通过将 URL 推入浏览器来操纵浏览器历史。反过来，Backbone 会检测到这些变化，并把你发送到一个与被推送的 URL 相匹配的“路由”。

明确一点:忘掉真正的主播。除非你担心让应用程序退化到不支持`pushState`的浏览器，或者甚至不支持 JavaScript，这取决于你的标记的语义，否则你还不如放弃它们。

截至本文撰写之时，iOS 如何处理真实锚、事件被中断以及`pushState`都存在问题。所以为了使事情变得简单，我设计了一个变通办法:我将依赖一个名为`data-href`的 HTML5 属性，然后在每个设置了它的锚或按钮上运行一个 jQuery `live`调用，并在`touchstart`上将一个调用挂接到路由器的`navigate`。这听起来很烦人，但是实现非常简单:

```
placeAnchorHooks : function() {
  $('[data-href]').live('touchstart', function() {
    App.Router.navigate( $(this).attr('data-href'), true );        
  });
} 
```

使用`touchstart`而不是`click`对应用程序的响应能力有很大的影响。作为一个练习，用`click`替换它(您必须首先克隆存储库)，看看会发生什么。你会注意到，每次点击，在屏幕对触摸做出反应之前，都会有明显的延迟。通过使用`touchstart`而不是`click`，我们解决了这个问题。

如果你想知道，是的，我们可以没有`pushState`而是依靠“hashbangs”来让事情进行。我这样做是为了说明在你的应用中拥有透明的`pushState`链接是多么容易。这也可以在非移动网络应用中重复使用。

### 设置

在这个例子中，我们只允许一种设置。用户可以指定应用程序将用来缩小它应该显示的商店的关键词。然而，这可以很容易地扩展到许多属性。这里特别值得注意的是，我们如何保持这些偏好。我已经覆盖了`Settings`主干模型的`sync`方法，因此它使用 [localStorage](http://diveintohtml5.info/storage.html) 保存属性。

当你意识到我们是在每个设备的基础上保持偏好时，这是非常有意义的。在人们携带一部以上的手机成为常态之前，这就够了。该覆盖包括以下内容:

```
var Settings = Backbone.Model.extend({
  sync: function(method, model, options) {
    switch(method) {
      case 'create':
      case 'update':
        localStorage.setItem('Settings', JSON.stringify(model));
        break;
      case 'delete':
        localStorage.removeItem('Settings');
        break;
      case 'read':
        var settings = localStorage.getItem('Settings');
        model.attributes = (settings && JSON.parse(settings) || {});
        return model;
    }   
    return this;
  } 
```

### 视图是子应用程序

这个例子使用了两个[主干视图](http://documentcloud.github.com/backbone/#View):一个用于主页，一个用于设置页面。我不会详细讨论什么是主干视图，但是可以把它们看作是大型应用程序中自包含的子应用程序。`HomeView`类有一个基本元素，所有与之相关的可视化内容都发生在这个元素中。它还会关注`Settings`型号的变化:

```
App.Settings.bind('change', this.hideMenu, this); 
```

因此，一旦用户设置了一些关键字，我们就不会再用那个跳跃的感叹号来烦他们了。

`SettingsView`类依次有两个任务。首先，它获取已经存在于`Settings`模型中的内容(比如，您之前已经设置了关键字),并用它填充文本字段，这样用户就可以看到那里有什么。其次，当我们通过提交表单更改值时，它获取文本字段中的内容并将其放入`Settings`模型中。

### 自定义页面

允许店主定制他们移动商店的外观会很有趣。但只是在一定程度上，因为我们不想给世界带来另一个 MySpace。因此，我们将允许他们选择主题，这些主题由 CSS 类中的一些覆盖组成。幸运的是，这很容易做到。

```
body > section.coffee {
  background: -webkit-gradient(radial, 50% center, 200, 50% center, 40, from(#a54e03), to(#bc7940)), #a54e03;
}
body > section.coffee h1,
body > section.coffee p   { text-shadow: 1px 1px 0 #000 } 
```

“咖啡”主题仅仅是原始红色主题的变体，带有类似咖啡的颜色。作为奖励，我给文本添加了阴影以提高可读性，因为考虑到白色文本，浅棕色比原来的深红色对比度低得多。其余的示例主题都是这个主题的变体。

比如说，店主在开店时选择了“咖啡”主题。在这个例子中，我们在 HTML 中有几个硬编码的链接，几个工作存储，只有一个内容很少。如果我们通过 Ajax 从服务器加载这些内容，我们将加载商店的首选项，检测主题选择，并将相应的 CSS 类应用于`<section>`元素。

简而言之，这是在我们的小框架中定制页面的一个诀窍。

### 基本原理

你可能想知道为什么你要经历这一切，当抛出一个框架听起来如此简单和快捷。这里的想法是双重的:让你明白从头开始构建这个非常简单，并给你一个可以随身携带的基本样板，这样你就可以随时从它开始一个新的应用程序。

您可以将这些文件的内容宏到您的文本编辑器中，或者编写一个脚本并创建一个迷你工具链，或者甚至手动将这个样板文件复制到一个新文件夹中。一旦你做到了这一点，你就可以像使用其他框架一样快速地启动一个新的应用程序。

### 表演

你们中的一些人会注意到 JavaScripts 是未压缩的，每个都被分离到它自己的文件中。在生产中保持这种状态是一个糟糕的想法，尤其是当我们谈论移动应用程序时，其延迟要昂贵得多。如果你把它作为一个应用程序来部署，确保你缩小并捆绑你的脚本，因为这将使事情变得更快。

另一个重要的考虑因素是，在尽量减少移动浏览器加载应用程序所需的 HTTP 请求数量的同时，要确保构成应用程序一部分的任何图像，例如按钮的箭头、应用程序徽标等:

*   使用[数据 URI](http://en.wikipedia.org/wiki/Data_URI_scheme) 嵌入 CSS 中。这将增加一点图像的大小，但你仍然会从中受益。你的应用程序需要发出的 HTTP 请求越少，速度就越快。
*   转换成 SVG 并嵌入到 HTML 中。这是我个人的最爱。iOS 4 和 5 以及 Android Honeycomb 都支持 SVG，所以为了安全起见，如果您希望您必须支持不支持 SVG 的浏览器，请选择前面的选项。

### 离别赠言

冒着引发咆哮的风险，让我澄清一件事:移动框架没有什么本质上的错误。嗯，其中一些非常糟糕，但总的来说，大多数都支持旧的、无特色的移动浏览器。如果你需要支持这些浏览器，那么无论如何，试一试框架，因为它肯定会节省你的时间。

然而，如果你想让你的应用程序在 Android 或 iOS 上运行，接受这些限制。限制很少，而且大部分都是以设计为中心的，所以使用一种极简的目标方法，用 Backbone.js 来构建你自己的框架。

## 分享这篇文章