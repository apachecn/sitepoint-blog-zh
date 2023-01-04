# 掌握更少的保护和循环

> 原文：<https://www.sitepoint.com/mastering-less-guards-and-loops/>

在[之前的文章](https://www.sitepoint.com/understanding-less-guards-loops/)中，我们学习了少混合保护和循环的基础知识。我们看到，一旦我们对它们的结构有了清楚的了解，我们就能正确地使用它们。现在，我们将通过研究一些真实世界的例子来探索如何将这些知识付诸实践。

## 创建警告框

第一个例子是创建一个有四种变化的警告框。我们将创建一个 mixin guard，根据我们传递给 mixin 的参数(警告框的类型),将常规的`div`转换成不同风格的警告框。首先，我们需要定义一些变量:

```
@color_info: #00a8e6;
@color_success: #8cc14c;
@color_warning: #faa732;
@color_error: #da314b;
```

这是四个颜色变量，我们将用它们来表示警告框的四种变体。现在，让我们添加所需的 mixin 保护:

```
.alert(@mode) when (@mode = 'info'){
 background-color: @color_info;
 border: thin solid darken(@color_info, 15%);
}

...

.alert(@mode) {
 width: 300px;
 padding: 25px;
 color: #fff;
 text-shadow: 0.5px 0.5px #000;
}
```

上面代码的第一部分创建了一个条件语句，如果`@mode`参数等于“info ”,它将使用`@color_info`变量来设置警告框的背景颜色和边框颜色。我们使用 [`darken()`](http://lesscss.org/functions/#color-operations-darken) 功能使边框颜色比背景颜色深 15%。

我们需要对其余三个变量重复相同的模式。我们通过复制和粘贴第一个变体来做到这一点。对于每个变量，我们使用适当的颜色变量，并将单词“info”分别改为“success”、“warning”和“error”。

最后，在最后，我们把所有警告框的通用样式。我们将文本颜色设置为白色，并对其应用微妙的文本阴影效果，以获得更好的对比度。

现在，要使用 mixin，我们需要把它放在`div`的 CSS 规则中。*等着瞧*。我们可以看到一个漂亮的绿色背景的警告框。

根据我们的需要，我们可以为我们的警告框使用不同的颜色——通过改变颜色变量的值——并且仍然保持 mixin 语义。

参见 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔[掌握少守卫和循环#1](http://codepen.io/SitePoint/pen/gpvGjJ/) 。