# 用 aXe 自动检查可访问性

> 原文：<https://www.sitepoint.com/automated-accessibility-checking-with-axe/>

![An a11y drone hovers over the developer's shoulder, accessibility checking a website on the computer screen](img/b533ca7d19274889e6d29d0cdba235ce.png)

您花了多少时间和精力来规划上一个网站的设计，使其对有特殊需求和残疾的人来说是可访问的？我有一种预感，许多读者的答案将是“没有”。但是几乎没有人会否认，有相当数量的互联网用户在访问网站时有困难，因为他们在区分颜色、阅读文本、使用鼠标或者只是在复杂的网站结构中导航时存在问题。

易访问性问题经常被忽略，因为需要检查它们并实现解决方案。开发人员不仅要熟悉底层标准，还要经常检查是否符合这些标准。我们是否可以通过自动执行标准检查来简化可访问网站的开发？

在本文中，我将向您展示如何使用 aXe 库和一些相关工具来自动检查和报告您的站点和应用程序中潜在的可访问性问题。通过降低此类活动所需的工作量并自动化一些手工工作，我们可以为使用我们创造的东西的每个人实现更好的结果。

## 介绍 aXe

aXe 是一个自动化的可访问性测试库，它已经开始将可访问性测试引入主流 web 开发。 [axe-core](https://github.com/dequelabs/axe-core) 库是开源的，并且被设计成可以与不同的测试框架、工具和环境一起使用。例如，它可以在功能测试、浏览器插件中运行，或者直接在应用程序的开发版本中运行。它目前支持大约 55 个规则来检查网站的各个方面的可访问性。

为了快速演示这个库是如何工作的，让我们创建一个简单的组件并测试它。我们不会创建一个完整的页面，而只是一个标题。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔[斧可达性检查](http://codepen.io/SitePoint/pen/OpaJeZ/)。