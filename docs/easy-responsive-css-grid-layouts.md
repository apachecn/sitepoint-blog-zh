# 易于响应的 CSS 网格布局:4 种方法

> 原文：<https://www.sitepoint.com/easy-responsive-css-grid-layouts/>

如今，每件事情都有一个框架，似乎在我们消化一个框架之前，另一个框架就发布了。对于 CSS 网格布局来说尤其如此，并且不乏被认为是“迄今为止最好、最轻量级”的框架。这种过量的信息会让我们困惑，并让我们怀疑像[html 9 Responsive boilers trap JS](http://html9responsiveboilerstrapjs.com/)这样的框架是否真的可行。

让我们退后一步，喘口气，问自己一个问题:我们真的要使用那 24 个变体中的每一个，以及“那个伟大的框架”附带的百万个子变体吗？在我们的项目中，我们通常只需要一个简单灵活的解决方案，有一些变体，并且对基础知识有很强的掌握，我们可以根据自己的需要进行扩展。我将介绍四种不同的技术来开发你自己的 CSS 网格，并且每一种技术都很容易扩展。这里有四种方法:

1.  响应式网格布局，v1(使用负边距)
2.  响应式网格布局，v2(使用`box-sizing: border-box`)
3.  使用表格显示的响应式网格布局
4.  使用 flexbox 的响应式网格布局

我将通过使用最少量的简单易懂的 CSS 来简化这些方法。每个示例都将提供一个 CodePen 演示，因此您可以在演示中使用 CSS。让我们开始吧。

*注意:我已经为每种技术嵌入了演示，但是为了看到每种技术的完整响应特性，最好通过点击每个演示顶部的“在 CodePen 上编辑”链接全屏查看 CodePen 演示。*

## 通用 CSS

在我们深入研究每个方法之前，让我们先来看看我们将使用的一些常见的 CSS。我们将在我们的文档中普遍使用`box-sizing: border-box`声明，并且我们还将为[清算浮动](https://www.sitepoint.com/clearing-floats-overview-different-clearfix-methods/)实现一个`.clearfix`类。这是我们的基本 CSS:

```
/* resets */
*,
*:before,
*:after {
  box-sizing: border-box;
}
.clearfix:after {
  content: "";
  display: table;
  clear: both;
}
```

如果需要任何厂商前缀，我们将使用 CodePen 的 [Autoprefixer](https://github.com/ai/autoprefixer) 特性为我们填充这些前缀。现在让我们进入四种方法中的第一种。

## 方法 1:使用负边距

这种方法利用负边距创建 CSS 网格块，每个块之间有固定的边距。负边距根据网格块的位置而变化，网格块之间的边距保持固定。让我们先来看看 HTML:

```
<div class="row-2 clearfix">
  <div class="col-1-2"></div>
  <div class="col-1-2"></div>
</div><!-- /.row -->

<div class="row-4 clearfix">
  <div class="col-1-4"></div>
  <div class="col-1-4"></div>
  <div class="col-1-4"></div>
  <div class="col-1-4"></div>
</div><!-- /.row -->

<div class="row-8 clearfix">
  <div class="col-1-8"></div>
  <div class="col-1-8"></div>
  <div class="col-1-8"></div>
  <div class="col-1-8"></div>
  <div class="col-1-8"></div>
  <div class="col-1-8"></div>
  <div class="col-1-8"></div>
  <div class="col-1-8"></div>
</div><!-- /.row -->
```

我们的 CSS:

```
/* grid */
[class*="row-"] {
  margin-bottom: 20px;
}
[class*="row-"]:last-child {
  margin-bottom: 0;
}
[class*="col-"] {
}

@media all and ( min-width: 768px ) {

  /* all cols margin */
  [class*="col-"] {
    margin-right: 20px;
  }
  [class*="col-"]:last-child {
    margin-right: 0;
  }

  /* make the columns responsive */
  .col-1-2 {
    float: left;
    width: 50%;
  }
  .col-1-4 {
    float: left;
    width: 25%;
  }
  .col-1-8 {
    float: left;
    width: 25%;
  }

  /* 2 span rows */
  .row-2 {
    padding-left: 20px;
  }
  .row-2 [class*="col-"]:first-child {
    margin-left: -20px;
  }

  /* 4 span rows */
  .row-4 {
    padding-left: 60px;
  }
  .row-4 [class*="col-"]:first-child {
    margin-left: -60px;
  }

  /* 8 span rows */
  .row-8 {
    padding-left: 60px;
  }
  .row-8 [class*="col-"]:nth-child(4n+1) {
    margin-left: -60px;
  }
  .row-8 [class*="col-"]:nth-child(5n-1) {
    margin-right: 0;
  }
  .row-8 [class*="col-"]:nth-child(6n-1) {
    clear: both;
  }

}

@media all and ( min-width: 1200px ) {

  /* adjust width */
  .col-1-8 {
    float: left;
    width: 12.5%;
  }

  /* 8 span rows */
  .row-8 {
    padding-left: 140px;
  }
  /* reset these... */
  .row-8 [class*="col-"]:nth-child(4n+1) {
    margin-left: 0;
  }
  .row-8 [class*="col-"]:nth-child(5n-1) {
    margin-right: 20px;
  }
  .row-8 [class*="col-"]:nth-child(6n-1) {
    clear: none;
  }
  /* and add this */
  .row-8 [class*="col-"]:nth-child(1) {
    margin-left: -140px;
  }

}
```

这是 CodePen 演示:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [ncDxo](http://codepen.io/SitePoint/pen/ncDxo/) 。