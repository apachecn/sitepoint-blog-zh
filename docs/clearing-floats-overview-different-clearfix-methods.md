# 清除浮动:不同 clearfix 方法概述

> 原文：<https://www.sitepoint.com/clearing-floats-overview-different-clearfix-methods/>

在前端开发中，清除浮动一直是一种常见的需要。难怪多年来，我们已经接触了多种清除浮动的方法，现在更普遍的说法是“clearfix 方法”。在我们深入研究各种方法之前，让我们看一下 clearfix 方法试图解决的问题。

**场景:** `.el-1`和`.el-2`并排浮动在一个`.container`元素中，在`.container`之后还有一个`.main`元素。

**期望的结果:**我们希望`.container`扩展到其子元素的高度(即`.el-1`或`.el-2`中较高的那个)，我们希望`.main`在`.container`之后。

**实际结果:** `.container`塌陷，完全没有高度，好像里面什么都没有，把`.main`放在一个不需要的位置，潜在地导致`.container`上的任何背景或边界丢失。

基于上述场景，我们的标记可能如下所示:

```
<div class="container">
  <div class="el-1">A long string of stuff here...</div>
  <div class="el-2">A short string of stuff here...</div>
</div>
<div class="main">
  Some stuff here...
</div>
```

那么我们的 CSS 可能看起来像这样:

```
.el-1, .el-2 {
  float: left;
  width: 50%;
}

.el-1 {
  /* styles for .el-1 here */
}

.el-2 {
  /* styles for .el-2 here */
}

.main {
  /* styles for .main here */
}
```

最后，结果如本演示所示:

看笔[为什么要通过](http://codepen.io/SitePoint/pen/flemL/) [CodePen](http://codepen.io) 上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )清除 float。