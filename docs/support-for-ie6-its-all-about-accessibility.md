# 对 IE6 的支持:这完全是关于可访问性

> 原文：<https://www.sitepoint.com/support-for-ie6-its-all-about-accessibility/>

![Internet Explorer 6 Logo](img/53ce6bb1a795f190f4ee4789c08b7aa7.png "Internet Explorer 6 Logo")

随着 IE8 的发布，人们对结束对 IE6 的支持感到兴奋。2009 年将是每个人都可以永远放弃 IE6 支持的一年吗？

你可能见过反 IE6 运动([停止 IE6](http://www.stopie6.org/) 、[搞垮 IE6](http://www.bringdownie6.com/) 、[结束 6](http://www.end6.org/))；有些是善意的，如[浏览器升级信息](http://www.browserupgrade.info/ie6/)，而其他的则具有政治迫害的所有微妙之处，如[令人震惊的大 IE6 警告插件。在我们决定把 IE6 用户烧死之前，我想我们需要了解一下谁还在用 IE6，为什么。我可以提供一个轶事，希望能让你相信 IE6 用户不是一群不知名的恶魔。](http://wordpress.org/extend/plugins/shockingly-big-ie6-warning/screenshots/)

在之前的工作中，我为一家建筑公司工作，开发他们的内部网和网站。关于 IT 政策，这家公司一点也不保守，经常研究新技术，以期改善他们的核心产品:优秀的设计。几年前，就在 IE7 发布之前，该公司决定购买一个功能齐全、基于网络的项目管理和会计系统。这将极大地改善整个公司的项目管理——但只与 IE6 兼容。

承诺与 IE7 兼容，但直到 2008 年底才交付。IT 经理避免将公司升级到 IE7，而是选择等待 IE8。幸运的是，由于微软方面的一些清醒的思考，IE8 可以像 IE7 一样工作，所以他们将能够升级到 IE8，并在他们的内部网中使用 IE7 模式。他们有 200 多名员工，只有少量 IT 人员来管理升级。这可能会发生在今年，2009 年。

您可能想知道为什么他们选择继续使用 Internet Explorer，而不是为他们的日常网络浏览安装另一个浏览器。该公司使用 Windows 漫游配置文件；这允许架构师在项目团队之间移动时使用公司中的任何工作站——有时是在不同州的办公室之间。Windows 组策略功能还用于更有效地管理员工凭据、权限和软件设置。与其他浏览器不同，Internet Explorer 非常好地支持这两种工具。为什么要安装第二个浏览器，这只会增加规模虽小但高效的 IT 支持团队的工作量？

如果该公司的架构师收到一条消息说他们的浏览器软件应该升级，他们将无法采取行动。这样的信息只会让他们感到烦恼，如果他们访问某个网站受到限制，情况会更糟。这是一个可访问性问题。

IE6 仍然平均占网络流量的 20%左右，所以阻止 IE6 用户访问你的网站是错误的做法。我怀疑鼓励他们更新浏览器的信息是否会有所帮助。在 IE6 中简单地停止测试也是不可取的。一个理性的方法是需要的，一个考虑你的网站的访问者使用 IE6 的百分比，以及你应该为他们提供什么样的体验的方法。

## 条件注释:IE6 的变通办法

[条件注释](https://reference.sitepoint.com/css/conditionalcomments)是控制你 IE6 用户体验的最好方法。至少，IE6 用户应该能够查看你的内容。使用容易混淆的命名为“下层显示”的条件注释，您可以隐藏 IE6(和早期版本)中的所有 CSS，只显示未样式化的内容:

```
<!--[if gte IE 7]><!-->
  <link rel="stylesheet" type="text/css" 
      href="style.css"/>
<!--<![endif]-->
```

这类似于使用`[@import](https://reference.sitepoint.com/css/at-import)`隐藏 Netscape Navigator 4 中的 CSS。NN4 中的 CSS 支持被破坏了，可能会使浏览器崩溃，这证明了这种激烈的方法是正确的；IE6 的能力远超 NN4。第二个选择是为 IE6 做一个单独的、简单但可用的布局。例如，如果您的站点布局有多列，那么为 IE6 创建一个单列布局。IE6 提供了一个特殊的样式表，而所有其他浏览器都提供了标准样式表:

```
<!--[if gte IE 7]><!-->
  <link rel="stylesheet" type="text/css" 
      href="style.css"/>
<!--<![endif]-->
<!--[if lt IE 7]>
  <link rel="stylesheet" type="text/css" 
      href="ie6-layout-style.css"/>
<![endif]-->
```

第三种选择是提供 IE6 的标准布局，但在这里和那里添加一些修复，这样 IE6 版本中可能会有一些差异。除了 IE6 之外的所有浏览器都提供标准样式表，而 IE6 则提供标准样式表和特殊的 IE6 样式表。使用级联特殊样式表覆盖一些标准样式规则来修复 IE6 问题:

```
<link rel="stylesheet" type="text/css" 
    href="style.css"/>
<!--[if lt IE 7]>
  <link rel="stylesheet" type="text/css" 
      href="ie6-fixes-style.css"/>
<![endif]-->
```

如果上面的方法不适合您管理样式的方法，另一种方法是使用条件注释来修改 body 标签，如下所示:

```
<!--[if gte IE 7]><!-->
  <body>
<!--<![endif]-->
<!--[if lt IE 7]>
  <body class="ie6">
<![endif]-->
```

通过以上操作，您只需在样式规则选择器前面加上类选择器“`.ie6`”，就可以创建一个只支持 IE6 的规则。

无论你选择做什么，它必须对你的网站有意义。研究有多少 IE6 用户访问你的网站，并试图找出他们是谁。最近写了一些关于如何应对 IE6 的优秀文章:

*   杰里米·基思的 IE6 方程式
*   Niels Matthijs (他也发布了 [IE6 CSS Fixer: Starter Kit](http://www.onderhond.com/tools/ie6fixer/) )
*   Rachel Andrew 为 IE6 和 ie7 开发 CSS
*   Craig Buckler 提出的解决 IE6 问题的 10 个修复

那么你将如何处理 IE6 呢？

## 分享这篇文章