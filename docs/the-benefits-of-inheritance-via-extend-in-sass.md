# Sass 中通过@extend 继承的好处

> 原文：<https://www.sitepoint.com/the-benefits-of-inheritance-via-extend-in-sass/>

组织 CSS 样式表已经成为有效设计大型网站的关键，但是随着项目的发展，我们项目中的样式表已经变得越来越大，越来越复杂，越来越难维护。这就是 Sass 让一切变得更简单的地方。

对于那些还没有探索 Sass 的人来说，它是 CSS 的扩展。它为我们提供了原生 CSS 中不存在的特性，比如表达式、变量、嵌套、混合(Sass 对函数的称呼)、继承等等。

在本文中，我将使用`@extend`概述 Sass 中的继承。我将介绍这个特性带来的优势，以及我在自己的项目中使用它的经验。需要注意的是`@extend`可能会被误用，Hugo Giraudel 在 SitePoint 甚至写了一篇关于[为什么你应该避免 Sass @extend](https://www.sitepoint.com/avoid-sass-extend/) 的文章。所以要知道`@extend`可能是一个有争议的话题。

在下面的例子中，我将使用 SCSS 语法。这个语法包含了 CSS 的所有特性和结构，以及 Sass 的附加特性。

## 什么是`@extend`？

是 Sass 的一个特性，它允许类之间共享一组属性。Sass 中的一个类的选择器将把它们的选择器包含在它所扩展的类的右边，产生一个逗号分隔的列表。

它的语法看起来像这样:

```
@extend .class-name;
```

### 使用

使用`@extend`看起来是这样的:

```
.foo {
  color: black;
  border: 1px solid black;
}

.bar {
  @extend .foo;
  background-color: red;
}
```

这被编译成:

```
.foo, .bar {
  color: black;
  border: 1px solid black;
}

.bar {
  background-color: red;
}
```

在上面的例子中，我定义了具有以下特征的`.foo`和`.bar`:

*   `.bar`继承自`.foo`，包含父类`.foo`的所有属性。
*   `.bar`通过添加属性`background-color`来扩展`.foo`。

了解了基础知识，我们现在来看看`@extend`的一些用例。

## 使用`@extend`

### 用例 1:复制

在组合 CSS 时，类之间的属性重复是难以避免的。这可能会使您的样式表更加复杂。例如:

```
.breakfast {
  color: #333;
  border: 1px solid #bbb;
  box-shadow: 1px 1px 0 #ddd;
  margin: 0 0 10px;
  padding: 15px;
}

.lunch {
  background-color: yellow;
  color: #333;
  border: 1px solid #bbb;
  box-shadow: 1px 1px 0 #ddd;
  margin: 0 0 10px;
  padding: 15px;
}

.dinner {
  background-color: orange;
  color: #333;
  border: 1px solid #bbb;
  box-shadow: 1px 1px 0 #ddd;
  margin: 0 0 10px;
  padding: 15px;
}
```

从上面的例子中我们可以看到，`.breakfast`、`.lunch`和`.dinner`包含了具有相同值的属性`color`、`border`、`box-shadow`、`margin`和`padding`。这些属性是重复的，使得我们的代码看起来很混乱，所以让我们用`@extend`重写它:

```
.meal-box {
  color: #333;
  border: 1px solid #bbb;
  box-shadow: 1px 1px 0 #ddd;
  margin: 0 0 10px;
  padding: 15px;
}

.breakfast {
  @extend .meal-box;
}

.lunch {
  @extend .meal-box;
  background-color: yellow;
}

.dinner {
  @extend .meal-box;
  background-color: orange;
}
```

在上面重写的 CSS 中，我们可以看到使用 Sass 有助于我们的标记变得比单独使用 CSS 更干净。

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看笔[在 SCSS](http://codepen.io/SitePoint/pen/BjZYVL/) 的重复属性。