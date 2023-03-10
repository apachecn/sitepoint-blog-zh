# Bootstrap 中 5 个有用的 Sass 混合

> 原文：<https://www.sitepoint.com/5-useful-sass-mixins-bootstrap/>

正如你们中的许多人可能知道的那样， [Bootstrap](http://getbootstrap.com/) 是前端开发人员的一个强大的工具包。它允许我们创建干净的、响应性强的、跨多种设备工作的网站。

如果您正在使用 Bootstrap 进行严肃的开发，那么您很有可能使用它的 [Less](https://github.com/twbs/bootstrap/tree/master/less) 或 [Sass](https://github.com/twbs/bootstrap-sass) 实现。

与此保持一致，下面我将描述我认为你应该尝试的 5 个引导混合。在 Sass 和 Less 中，它们都被直接构建到框架中，可能会节省您编写下一个站点的时间。在这篇文章中，我将专门讨论 mixins 的 Sass 版本。

## @mixin make-row()

[mixin](https://github.com/twbs/bootstrap-sass/blob/881ce27c6b4101698ea849283933203e20861a33/assets/stylesheets/bootstrap/mixins/_grid.scss#L15-L19)完全按照它说的做，在网格中生成一行。为什么要使用这个 mixin 而不是仅仅添加`.row`类呢？两个原因。首先，我们可以使用语义代码，而不是:

```
<div class="row">
```

我们可以使用类似于:

```
<section>
```

这样，我们可以使用合适的标签(`section`、`article`等)。)来指定页面的一部分。这可能会使代码更容易阅读和理解该行中的内容。我们现在要做的就是

```
section {
 @include make-row();
}
```

正如你所看到的非常有用，但是当你把它和下一个 mixin 结合起来使用时，这个 mixin 的真正效用就被释放了。

## @mixin make-*-column()

这个 mixin 和`@mixin make-row()`一起工作，实际上是[一群 mixin](https://github.com/twbs/bootstrap-sass/blob/881ce27c6b4101698ea849283933203e20861a33/assets/stylesheets/bootstrap/mixins/_grid.scss#L22-L122)。其中一个 mixinss 允许您定义想要使用的列的大小，而其他 mixin 允许您推、拉和偏移列。

如果您熟悉 Bootstrap(或任何网格系统)，那么网格系统是基于包含列的行的。因为很可能永远不会出现不使用`make-row()` mixin 而使用其中一个列 mixin 的情况，反之亦然，所以您可以使用类似这样的东西来创建列/行:

```
section {
 @include make-row();
    article {
 @include make-lg-column(2);
    }
}
```

父节创建包含嵌套列的行。在这种情况下，每篇文章将由 2 个专栏组成。因此，您可以创建多达 6 个文章部分来跨越页面的 12 个栏。

```
<section>
    <article>column 1</article>
    <article>column 2</article>
<section>
```

如果您有一个每个页面都有相同布局的站点，您可以在 Sass 中创建整个布局。即使每个页面都不同，使用这些 mixins 来布局站点的静态部分(如页眉和页脚)也会有好处。

## @mixin size()

size() mixin 是一个简单但非常有用的 mixin。在 Bootstrap 中创建行和列时，默认行为是根据内容调整列的大小。这在很多情况下是没问题的，但是有时你需要一个特定的尺寸。这就是尺寸混合的原因。它有两个参数——宽度和高度:

```
.example {
 @include size (16%, 300px)
}
```

在这种情况下，我选择了宽度的百分比(保持其响应)。当然，你可以在普通的 CSS 中手动设置宽度和高度，但是这在一行代码中做了同样的事情。这个 mixin 还有一个同伴 [@mixin square()](https://github.com/twbs/bootstrap-sass/blob/881ce27c6b4101698ea849283933203e20861a33/assets/stylesheets/bootstrap/mixins/_size.scss#L8-L10) ，它采用单参数。

## @mixin panel-variant()

大多数人对 Bootstrap 的一个缺点是，用它创建的大多数网站看起来都一样。这是因为许多人采用股票组件，并使用它们。尽管这很好，但我们中的许多使用 Bootstrap 的人会定制我们使用的每个组件。

在这方面，[面板变体()混合](https://github.com/twbs/bootstrap-sass/blob/881ce27c6b4101698ea849283933203e20861a33/assets/stylesheets/bootstrap/mixins/_panels.scss)有所帮助，它让你可以轻松地设计[面板](http://getbootstrap.com/components/#panels)。mixin 有四个参数`$border`、`$heading-text-color`、`$heading-bg-color`和`$heading-border`。

```
.panelrow {
 @include panel-variant(#d01919, #1cf21c, #000000, #bad017);
}
```

在这个例子中，我创建了一个包含 mixin 的类，它将与 Bootstrap 的 panel 类一起使用:

```
<div class="panel panel-default panelrow">
    <div class="panel-heading">
        <h3 class="panel-title">Panel title</h3>
    </div>
    <div class="panel-body">
        Panel content
    </div>
</div>
```

## @mixin button-variant()

另一个 mixin 旨在让你轻松定制引导组件，[按钮变体()mixin](https://github.com/twbs/bootstrap-sass/blob/881ce27c6b4101698ea849283933203e20861a33/assets/stylesheets/bootstrap/mixins/_buttons.scss#L6-L42) 让你定制按钮。这个 mixin 有三个参数——`$color`、`$background`和`$border`:

```
.custom-btn {
 @include button-variant(#cece19, #000000, #2dbe27);
    }
```

## @mixin gradient-*()

这实际上是[一组 mixin](https://github.com/twbs/bootstrap-sass/blob/881ce27c6b4101698ea849283933203e20861a33/assets/stylesheets/bootstrap/mixins/_gradients.scss)允许你快速创建 CSS 渐变。

例子包括`gradient-horizontal()`、`gradient-vertical()`和`gradient-horizontal-three-colors()`。每个 mixin 都有不同的参数，所以一定要查看源代码以确定您需要哪些参数。

下面的 mixin 有四个参数——`$start-color`、`$end-color`、`$start-percent`、`$end-percent`:

```
.example {
 @include gradient-horizontal(#1834cc, #1dc937, 0%, 100%);
    }
```

## 结论

正如你所看到的，Bootstrap 内置了一些强大的 mixins。这 5 只不过是皮毛。尝试深入 Bootstrap 的存储库中的 mixins 文件夹并四处查看。找出需要哪些参数以及如何使用它们，然后在自己的项目中尝试它们。你可能会发现自己使用其中一些作为你的首选混音，就像我一样。

## 分享这篇文章