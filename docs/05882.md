# 在 Sass 中处理配色方案

> 原文：<https://www.sitepoint.com/dealing-color-schemes-sass/>

很多网站的每个部分都有特定的配色方案。甚至你正在阅读的这个网站也是这样——你在蓝色部分，也就是 CSS 的 T1 部分。这是一个聪明的设计趋势，在同一个实体的不同部分之间创造出清晰的区别。

但是在技术方面，这可能是一个痛苦的工作。不是因为它过于复杂，而是因为它包含了许多令人讨厌的重复。

除非，你用——比如说——Sass 来自动化这个过程。那么你就有了真正容易使用的东西。由于我不得不这样做不止一次，我会告诉你我如何处理这种情况。

## 配色方案是如何工作的？

在开始写代码之前，我们应该问*CSS 中的颜色计划实际上是如何工作的*？嗯，如果你不打算使用任何 JavaScript(这听起来对我来说是个好主意)，最简单的方法可能是在你的 HTML 和 CSS 中建立一个主题类，这样每个需要根据主题进行样式化的元素都成为这个类的*子元素。*

SitePoint 遵循这种方法。如果您查看 ID 为`#primary`的主包装器，您会看到它也有一个类`.category-css`。这使得主题样式变得容易。

*注意:你可以从我自己的博客中读到我是如何用树枝处理[主题和布局的。](http://hugogiraudel.com/2013/11/12/themes-layouts-twig/)*

一旦在包装器上设置了类，就很容易使样式有条件。假设您有一个包含类`.element`的元素，它需要根据主题进行样式化:

```
.theme-banana .element {
  color: yellow;
}

.theme-blueberry .element {
  color: purple;
}

.theme-cherry .element {
  color: red;
}
```

我使用了`color`属性，但是它也可以是`background`、`border-color`甚至是`box-shadow`。随便啦！如果需要的话，甚至可以是精灵或图像路径的背景位置。

它工作起来很神奇，但是，正如你所看到的，它要求我们连续三次输入同样的内容。如果我们需要添加另一个主题，我们必须再次重复它。然后，如果我们需要让其他东西以主题为条件，我们需要一次又一次地重复这些规则…必须有一个更简单的方法！

## 萨斯拯救

如果我们可以将主题的名称和颜色赋予某种功能，那么它会立即转储我们需要的所有内容，这不是很酷吗？这听起来确实像是 mixin 的完美用例，不是吗？

这里有一个简单的 mixin，我们只需要编写一次:

```
@mixin theme($name, $color) {
  .#{$name} {
    .element {
      color: $color;
    }
  }
}
```

下面是你如何使用它:

```
@include theme(theme-banana, yellow);
@include theme(theme-blueberry, purple);
@include theme(theme-cherry, red);
```

胡言乱语。下面是 CSS 编译后的样子([查看这里的代码](http://sassmeister.com/gist/9342088)):

```
.theme-banana .element {
  color: yellow;
}

.theme-blueberry .element {
  color: purple;
}

.theme-cherry .element {
  color: red;
}
```

嗯，看起来很整洁，不是吗？除了一个小的 mixin 和每个主题的 include 之外，我们设法像变魔术一样把所有的 CSS 都放了进去。所以每当我们需要制作主题为 T1 的东西时，我们只需要更新 mixin 核心来添加任何需要的东西。

假设我们想让`.other-element`有一个主题背景色([在这里查看代码](http://sassmeister.com/gist/9342111)):

```
@mixin theme($name, $color) {
  .#{$name} {
    .element {
      color: $color;
    }

    .other-element {
      background: $color;
    }
  }
}
```

就这么简单！正如你所看到的，Sass 使色彩计划变得更加容易。

## 每个主题多种颜色

根据你想给你的站点的感觉，你可能希望在一个主题中有几种颜色。在这种情况下，您有两种选择:

*   将第二种颜色插入混音签名(如`$secondary`)。
*   使用[颜色操作功能](http://sass-lang.com/documentation/Sass/Script/Functions.html)从第一个颜色产生新的颜色。

我建议尽可能使用第二种选择，因为这比要求第二种颜色容易得多。话虽如此，有时(经常？)设计者希望有特定的颜色，这些颜色不容易用颜色函数产生。

如果您想尝试第二个选项，下面是您的 mixin 可能的样子([查看这里的代码](http://sassmeister.com/gist/9342563)):

```
@mixin theme($name, $color) {
  $primary: $color;
  $secondary: lighten(adjust-hue($color, 20), 10%);

  .#{$name} {
    .element {
      color: $primary;
    }

    .other-element {
      background: $secondary;
    }
  }
}
```

## 自动化 Mixin 包含

你可能已经注意到，当我们为每个主题添加 mixin 时，仍然有一些代码重复。我们不得不重复`@include theme(/* ... */)`，这有点糟糕。不是很<abbr title="Don't Repeat Yourself (programming principal)">干</abbr>。

如果我们有某种可以用来动态生成所有主题的配置对象会怎么样？这听起来像是地图的完美用例(在 Sass 3.3 中有[):](https://github.com/nex3/sass/issues/642)

```
$themes: (
  theme-banana: yellow,
  theme-blueberry: purple,
  theme-cherry: red
) !default;
```

*注意:如果你需要一个以上的参数，可以随意使用一个[列表](http://sass-lang.com/documentation/file.SASS_REFERENCE.html#lists)作为值(例如`(banana: yellow orange)`)；它被支持。*

现在我们已经设置好了地图，我们可以遍历它并在循环中包含主题。

```
@each $theme, $color in $themes {
  @include theme($theme, $color);
}
```

([查看代码](http://sassmeister.com/gist/9342576))

也就是说，每个页面很可能只使用一个主题，所以每次都包含所有主题可能有些过头了。在我的公司，我们只包含当前页面需要的主题；因此，最小的 CSS 输出。然而，这取决于您自己的部署过程，这超出了本文的范围。

注意:如果你正在使用 Sass 3.2，你可以使用嵌套列表来代替映射。那完全一样。

## 最后的话

正如您所看到的，创建一个基于主题的站点不仅容易建立，而且非常优雅，因为您可以在一个完全不同的地方(或文件)配置您的主题，而不是在调用主题的地方。

例如，您可以在一个配置文件(`_config.scss`)中拥有您的配置映射，在一个主题文件(`_themes.scss`)中拥有您的主题实例。相当方便。

如果你对使用 CSS 配色方案时使用 Sass 有任何建议，请在评论中告诉我们。有趣的是，下面你会发现一个 CodePen 演示，它将这些想法整合到一个由 JavaScript 触发的主题切换器中。

请看 [CodePen](http://codepen.io) 上 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )的笔 [Sass 配色方案演示](http://codepen.io/SitePoint/pen/czixa)。