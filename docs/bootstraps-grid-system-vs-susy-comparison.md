# Bootstrap 的网格系统与 Susy 的比较

> 原文：<https://www.sitepoint.com/bootstraps-grid-system-vs-susy-comparison/>

现在有很多网格框架可供我们在开发网站时选择。在众多框架中，最受欢迎也最受讨厌的一个无疑是 [Bootstrap](http://getbootstrap.com/) 。

今天，我想向大家介绍一个稍微不太为人所知的框架，它已经被很多人所接受—[Susy](http://susy.oddbird.net/)。在本文中，我将展示 Susy 和 Bootstrap 的网格系统之间的比较，这样您就可以完全理解 Susy 是什么，以及您是否想尝试一下。

## 什么是 Susy？

为了回答这个问题，我们首先要回答“什么是网格？”

在 web 术语中，网格只不过是一组从页面顶部到底部的垂直线。它们起源于印刷设计，现在每天都被网页设计者用来作为组织和展示网页信息的基础。

![Basic grid example](img/b45c95daf4646096a3c8a824894e5e02.png)

这些垂直线有助于将页面分割成两种垂直空间。我们称较宽的空间为柱子，较窄的空间为檐槽。我们对元素排序和定位的地方就是我们所说的布局。

![Grid columns and gutters](img/8b97c2a771e85a90ef96065b54031ec7.png)

过去使用了很多种网格框架，你可能知道。一些例子是:

*   960 网格系统
*   1140 像素网格系统
*   引导程序
*   基础

所有这些框架都有一个共同点。它们都有一个已经存在的网格，您可以在有限的范围内对其进行定制。

然而，使用非对称网格的有趣的网页设计已经开始在网上涌现，像刚刚提到的那些传统框架不可能帮助你为此创建一个网格。

这就是苏西的作用。

Susy 是一个布局引擎。它为你提供了**工具来构建一个定制的网格框架**来满足你网站的需求。这给了你灵活性和自由来发挥你的设计。

## 什么是自举？

Bootstrap 不仅仅是一个网格系统。它是一个功能齐全的框架和工具包，为我们提供了:

*   网格系统
*   导航、表单、提醒和图标等常见 web 元素的样式。
*   复杂元素的 jQuery 插件，比如手风琴和旋转木马。

一旦你理解了 Susy 和 Bootstrap 是什么，很容易看出我们不能在逻辑上比较这两者。首先，它们是完全不同的东西。

因此，我们可以对 Susy 和 Bootstrap 中的网格系统进行最接近的比较。当我们这样看待它时，有几件事我们可以比较。以下是我们将在本文中涉及的一些内容:

*   预处理器语言
*   标记用法
*   定制网格的能力
*   能够响应多个断点
*   HTML 和 CSS 的耦合
*   文档和社区

## 预处理器语言

预处理器语言很重要，因为您需要工具来适应您的工作流，而不是相反。让我们首先弄清楚什么对什么起作用。

虽然 **Bootstrap** 最初是用[少](http://lesscss.org/)建造的，但是现在它**可以同时用 Sass 和 Less** 。因此，语言根本不是使用 Bootstrap 的障碍。

最新版本的 **Susy (Susy 2)只适用于 Sass 版本 3.3** 及以上，因为它需要使用 [Sass 地图](https://www.sitepoint.com/using-sass-maps/)。如果你只能使用低于 3.3 版本的 Sass，还有一个更老的版本[。可惜 Susy 不支持少。](http://susydocs.oddbird.net/en/latest/susyone/)

## 标记用法

让我们用一个简单的例子来比较 Bootstrap 和 Susy 的标记。

![Example layout](img/622458fb569004786baebb155f38f82c.png)

Bootstrap 要求在它的所有标记中使用像`.row`和`.col-md-4`这样的类，以便正确地设计 HTML 的样式。

这也是 Bootstrap 大部分批评的来源。批评家认为这些类是表示类，不应该出现在标记中。

下面是 Bootstrap 需要的标记，其中包含一些基于网格的示例类:

```
<div class="container-fluid">
  <div class="row">
    <div class="col-md-8 content"><h2>Content</h2></div>
    <div class="col-md-4 sidebar"><h2>Sidebar</h2></div>
  </div>
</div>
```

相比之下，Susy 的要求是。

```
<div class="wrap">
  <div class="content"><h2>Content</h2></div>
  <div class="sidebar"><h2>Sidebar</h2></div>
</div>
```

与 Bootstrap 相比，Susy 的 HTML 更简单，语义更丰富。然而，仅仅用 Susy 编写 HTML 是不够的。你必须给`.content`和`.sidebar`添加一些样式，这样它们才能正确显示。

让我们做一个快速配置来演示 susy 是如何工作的。

首先，请注意 Bootstrap 将间距作为填充应用于每列的两边。

![Bootstrap gutters](img/767e2635f6d4c4d4403f7a8afe6ebb53.png)

默认情况下，Susy 将装订线作为边距应用于每列的右侧。要创建与 Bootstrap 相同的网格，您必须更改`$susy`图中的`gutter-position`设置。

```
$susy: (
 gutter-position: inside
)
```

对于每一个 Susy 网格，你必须包含一个`.container`类的`container` mixin。在上面的例子中，容器是`.wrap`，它的最大宽度是 1140 像素。

```
.wrap {
 @include container(1140px); 
}
```

我们的 Bootstrap 示例使用 8 列内容和 4 列`.sidebar`。你必须使用 Susy 中的`span` mixin 来创建`.content`和`.sidebar`的布局，它们将分别占据 8 列和 4 列。

```
.content {
 @include span(8 of 12); 
}

.sidebar {
 @include span(4 of 12);
}
```

与 Bootstrap 相比，Susy 的标记更加简洁，但是启动和运行初始网格需要做更多的工作，因为 Susy 要求您自己配置它们，而 Bootstrap 已经为您提供了必要的样式。

## 定制网格的能力

Bootstrap 提供了在一定程度上定制网格的能力。您可以自由地更改以下内容:

*   列数
*   排水沟的尺寸
*   4 个可用断点的大小(`xs`、`sm`、`md`和`lg`)

您可以从两个不同的区域自定义这些网格:

1.  通过[自定义选项卡](http://getbootstrap.com/customize/)进行更改，或者
2.  在您使用的任何预处理器中更改`_variables`部分中的变量(Sass 或更少)。

对于这两种情况，可以在“网格系统”部分找到列和空白，而媒体查询可以在“媒体查询断点”部分找到。

另一方面，Susy 允许你改变更多的事情。您可以更改如下内容:

*   列数
*   每个单独列的大小
*   檐槽尺寸
*   檐槽的位置
*   断点数量
*   断点的大小
*   文档从左到右或从右到左流动
*   用于网格的箱式模型

…以及更多。

虽然其他网格系统如 [Singularity](http://singularity.gs) 和 [GridsetApp](https://gridsetapp.com) 因不对称网格而更加出名，但是如果你知道如何设置，Susy 也允许你使用这样的网格。一些伟大的不对称布局的例子是[这个](https://gridsetapp.com/specs/marber/?gridset=show)、[这个](https://gridsetapp.com/specs/gerstner/?gridset=show)和[这个](https://gridsetapp.com/specs/typekit-demos/chaparral.html?gridset=show)。

我特别喜欢内森·福特的最后一幅作品，并与苏西成功地再现了同样的不对称布局。不再需要坚持使用标准网格。Susy 让你对你或你的设计师制作的网格有疯狂的创造力。

## 能够轻松响应多个断点

正如我上面提到的，Bootstrap 允许你为你的网格使用 4 个不同的断点:`xs`、`sm`、`md`和`lg`。这些断点被直接写入网格的引导类中。

在我们上面的例子中，如果你把浏览器的尺寸缩小到 991 像素以下，`.content`将变成容器的 100%,而`.sidebar`将下拉到下一列。

假设您希望`.content`和`.sidebar`在较小的视窗(低于 991px)中占据半个屏幕，那么包含多个断点参数的 HTML 并不少见，如下所示:

```
<div class="container-fluid">
  <div class="row">
    <div class="col-xs-6 col-md-8 content"><h2>Content</h2></div>
    <div class="col-xs-6 col-md-4 sidebar"><h2>Sidebar</h2></div>
  </div>
</div>
```

而当你用 Susy 做这件事时，你改变的是 CSS 而不是 HTML。Susy 的 HTML 与我们之前看到的一样，没有添加任何内容:

```
<div class="wrap">
  <div class="content"><h2>Content</h2></div>
  <div class="sidebar"><h2>Sidebar</h2></div>
</div>
```

相反，您必须在 Sass 文件中添加一个断点(比如 991px)。

这样做的时候，可以从移动优先的方法出发，先声明`.content`和`.sidebar`各占一半屏幕。991px 之后，`.content`占 8 列，`.sidebar`占 4 列:

```
.content {
 @include span(6 of 12);
 @media (min-width: 980px) {
 @include span(8 of 12); 
  }
}

.sidebar {
 @include span(6 of 12);
 @media (min-width: 980px) {
 @include span(4 of 12);
  }
}
```

Bootstrap 要求你小心地用 HTML 类编码，这有时会让我感到困惑。当我用 Bootstrap 编码时，我总是必须记住特定的断点，如果我想让它们正确响应，我必须在网格中包含正确的断点缩写。

另一方面，Susy 不去管 HTML，而是要求你在 Sass 文件中仔细编码。我强烈倾向于在样式表中留下断点，因为从认知上来说，我更容易看到发生了什么。

根据您的偏好，Bootstrap 或 Susy 都是合适的。我个人更喜欢 Susy，因为有了 Susy，我现在可以安全地将 HTML 与 CSS 分离。这就引出了下一点。

## HTML 和 CSS 的耦合

今天，任何重要的网站都需要包含大量的 HTML 和 CSS。HTML 和 CSS 结合得越紧密，代码就越难维护。

想象一下，如果我们回到使用`style`属性在 HTML 中编写所有 CSS 代码的时代。在 HTML 中使用像`.col-xs-6`、`.col-md-4`和`.row`这样的类类似于使用`style`属性。

当您有多个 HTML 元素需要一个带有`.col`类的特定布局时，您需要包含这些类的次数就会增加。如果设计必须改变，并且`.content`需要减少到 7 列而不是 8 列，该怎么办？你一定要回去到处修改所有的 html 类吗？

```
<div class="col-md-7 content">
  <h2>Content</h2>
</div>
```

对我来说，这真的很难维持。

我个人倾向于使用 Susy，并将这些表示类保留在它们所属的外部 CSS 中。例如，在 Susy 中，如果我必须将`.content`更改为 7 列，我必须只更改我使用的`span` mixin，并使其为 7 列而不是 8 列！

```
.content {
 @include span(7 of 12); 
}
```

我认为这更容易维护，因为 HTML 和 CSS 现在是分离的。

既然你在使用 Susy 时可以自由支配 HTML 和 CSS，你现在可以应用各种 CSS 架构实践，比如 [SMACSS](https://smacss.com) 、 [OOCSS](http://oocss.org) 和 [BEM](http://bem.info) ，而不必过多考虑它们会如何影响你的 HTML。

## 文档和社区

最后，文档和社区在每个框架中都扮演着重要的角色。社区越大，文档越强大，您就越容易找到信息和提出问题。

Bootstrap 的文档和社区绝对是强大的。无论何时你有问题，你都可以在 StackOverflow 或其他编码论坛上发布你的问题[，一定会有人帮助你。](http://stackoverflow.com/questions/tagged/twitter-bootstrap)

然而，Susy 的社区还没有这么大，文档也不够健壮。目前网上也缺乏关于如何用 Susy 建立网站的案例研究，这实际上阻止了许多人去选择它。为了解决这个问题，我写了一本关于学习 Susy 的书，所以如果你有兴趣了解的话，请看看。

## 结论

虽然 Susy 和 Bootstrap 是根本不同的工具，但是如果我们比较一下他们的两个网格系统，我们就可以清楚地看到有什么不同。正如我在上面强调的，我坚信 Susy 比 Bootstrap 有更好的底层系统。

如果你有兴趣了解更多关于 Susy 的信息，请前往[官方网站](http://susy.oddbird.net)，查看[文档](http://susydocs.oddbird.net/en/latest/)，或者从[我的书](http://zell-weekeat.com/learnsusy/)中抓取 4 个免费章节。

## 分享这篇文章