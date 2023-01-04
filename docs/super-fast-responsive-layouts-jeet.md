# Jeet 的超快速响应布局

> 原文：<https://www.sitepoint.com/super-fast-responsive-layouts-jeet/>

今天有大量的网格系统可供我们使用。设计师和前端开发人员可以对它们进行试验，并选择最符合他们需求的产品。CSS 框架和样板如 [Bootstrap](http://getbootstrap.com/) 、 [Foundation](http://foundation.zurb.com/) 和 [Skeleton](http://getskeleton.com/) 提供了数千人使用的网格。

在本文中，我将向您介绍 [Jeet](http://jeet.gs/) ，一个基于 CSS 预处理器的替代网格系统。首先，我将介绍它的基础知识，展示它的核心组合。然后，我将向您展示如何利用它的特性来构建一个项目。

## Jeet 是什么

Jeet 是建立在 [Sass](http://sass-lang.com/) 和 [Stylus](http://learnboost.github.io/stylus/) 之上的网格系统。它由强大的混音和函数组成，您可以使用它们来创建快速响应的布局。

与许多其他网格不同，Jeet 提供了一种更灵活的方法来生成布局。以下是它的主要优点:

1.  它没有添加任何额外的标记。
2.  它不限于特定的列大小(例如 12 列网格)。
3.  您可以使用分数(例如 1/4)、小数(例如 0.75)，甚至它们的组合(例如 1.5/4)来生成所需的布局。

网格被设计成可以在包括 IE9+在内的所有主流浏览器上工作。如果你想瞄准以前版本的 IE，看看 [Boy](https://github.com/corysimmons/boy) 样板文件。

## 入门指南

网格有两种风格，一种用于 Sass(SCSS ),另一种用于手写笔。根据您想要使用的预处理器，有不同的安装方法。Jeet 的官方页面描述了将网格包含到项目中的所有步骤。

基于它的 Sass 版本，我建立了一个[演示项目](http://geomarts.github.io/jeet-demo/)。我将在下一节中引用它。现在，让我们来看看它的基本结构:

```
jeet-demo/
├── index.html
├── jeet/
│     ├── _functions.scss
│     ├── _grid.scss
│     ├── _settings.scss
│     └── index.scss
├── style.css
└── style.scss
```

你可以通过访问这个 github 库来获取这个项目的所有文件。

在这一点上，是时候通过 Jeet 的主要混合。

注意:为了演示 mixins 是如何工作的，我将使用 SCSS 语法。

## 容器

mixin 允许你将一个元素水平居中。大多数情况下，您将使用这个 mixin 来定义充当列容器的元素。下表显示了其参数:

| 参数 | 描述 | 缺省值 |
| --- | --- | --- |
| 最大宽度 | 设置元素的`max-width` | 1410px |
| 衬垫 | 指定元素的`padding-left`和`padding-right`属性 | Zero |

这是一个混合的例子:

通过 [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看 Pen[Jeet Grid-container](http://codepen.io/SitePoint/pen/rampvj/)。