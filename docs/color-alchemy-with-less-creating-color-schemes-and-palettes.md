# 色彩炼金术与更少:创建配色方案和调色板

> 原文：<https://www.sitepoint.com/color-alchemy-with-less-creating-color-schemes-and-palettes/>

色彩是任何视觉设计中最重要的元素之一。如果使用得当，它会对您的网站或应用程序产生巨大的影响。但是仅仅了解色彩理论还不足以达到这样的效果。你需要一条合适的工具带来轻松成功地使用多种颜色。幸运的是， [Less](http://lesscss.org/) 通过提供大量的颜色函数来解决这个实际问题。

在本教程中，我将探索如何使用这些颜色函数，结合其他较少的功能，以产生灵活的和可重用的颜色处理混合。

## 创建配色方案

当试图用更少的颜色来创建配色方案时，大多数人会采取最显而易见的方法，如下所示:

```
@base-color: #00ff00;
@triad-secondary: spin(@base-color, 120);
@triad-tertiary: spin(@base-color, -120);
```

这个方法使用变量和 Less 的`spin()`函数来创建一个配色方案(在我们的例子中是三色的)。这很好，但对我来说，它不是特别可重用，也不够灵活。幸运的是，这个问题可以通过使用 mixins 来解决。让我们看看我的意思。

```
.analog(@color, @variant, @property) {
  @first: spin(@color, 30);
  @second: spin(@color, -30);
  @list: @first, @second;
  @return: extract(@list, @variant);
  @{property}: @return;
}

.triad(@color, @variant, @property) {
  @first: spin(@color, 120);
  @second: spin(@color, -120);
  @list: @first, @second;
  @return: extract(@list, @variant);
  @{property}: @return;
}

.quad(@color, @variant, @property) {
  @first: spin(@color, 90);
  @second: spin(@color, -90);
  @third: spin(@color, 180);
  @list: @first, @second, @third;
  @return: extract(@list, @variant);
  @{property}: @return;
}
```

上面的代码创建了三种颜色方案。我只解释最后一个，因为前两个结构一样，不需要单独解释。

`.quad()` mixin 有三个参数。第一个设置方案的基本颜色。第二个告诉 mixin 返回哪个颜色变量。第三个定义 Less 编译代码时使用哪个 CSS 属性。在 mixin 的主体中，`spin()`函数在四元方案中创建三种可用的颜色变量，然后将这些变量放在一个列表中。`extract()`函数获取所需的变量，在第二个参数中定义。最后，在[变量插值](http://lesscss.org/features/#variables-feature-variable-interpolation)的帮助下，将颜色变量赋给定义好的 CSS 属性。

我们现在可以将上述代码放在一个名为`color_schemes.less`的单独文件中，并按如下方式使用它:

```
@import "color_schemes.less";

@base-color: #00ff00; 

div {
  width: 200px;
  height: 100px;
  border: thick solid;
  .quad(@base-color, 3, border-color);
  .quad(@base-color, 2, color);
  .quad(@base-color, 1, background-color);
}
```

在这里，我们导入带有配色方案的文件，然后为我们的网站或应用程序定义基本颜色。`div`规则集中的最后三行定义了`border-color`、`color`和`background-color`属性的颜色。

如您所见，mixin 可以用于任何预期值是颜色的属性。此外，很容易看出特定语句用于哪个属性；看一看结尾，我们就知道了。例如，在最后一个语句中，您可以清楚地看到四色方案的第一个颜色变体将被用作`background-color`属性的值。很酷吧。

下面是编译后的输出:

```
div {
  /* ... etc... */
  border-color: #ff00ff;
  color: #ff8000;
  background-color: #007fff;
}
```

通过[码笔](http://codepen.io)上的 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )看笔 [xwxmeP](http://codepen.io/SitePoint/pen/xwxmeP/) 。