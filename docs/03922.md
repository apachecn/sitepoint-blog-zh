# 用 Sass 设计瓷砖样式

> 原文：<https://www.sitepoint.com/style-tiles-sass/>

随着响应式网页设计成为新常态，许多设计师、开发者和代理机构已经意识到静态构图不再是向客户展示他们网站的有价值的方式。正如斯蒂芬·海伊所说的

> "我们不是在设计页面，我们是在设计组件系统."

随着视口和布局的变化，这些组件会以不同的容器大小进行重排和调整。许多设计师不再花费数周时间为一个网站设计制作 3-4 个不同屏幕尺寸的静态构图(冒着客户拒绝所有这些工作的风险)，而是转向[“风格瓷砖”](http://styletil.es/)来帮助客户理解和批准设计方向，而无需制作完全详细的构图。风格瓷砖旨在“为客户提供界面选择，而无需投资多个 photoshop 模型。”有时它们被称为[元素拼贴画](http://danielmall.com/articles/rif-element-collages/)或 [UI 地图](http://mds.is/real/)。

风格指南的兴起与前端开发的另一个最新发展非常吻合:在开发过程中尽可能早地开始浏览器内原型开发。当我们通过整合风格指南来帮助客户理解设计，并将这一过程移入标记并移出设计软件时，我们最终会创建许多客户使用的 PDF 品牌指南文档的实时网络版本。

正如这篇文章的标题所暗示的，Sass 有一些特性可以让我们更容易地创建生活方式指南。现在让我们来看看这些。

## 调色板

使样式指南更容易的一个简单方法是自动化可重复的信息。例如，要向客户展示您的调色板，您可以创建几个正方形，每个正方形显示一种颜色。您的 HTML 可能如下所示:

```
<ul class="color-palette">
  <li class="color-blue">
    <span class="swatch"></span>
    Blue
  </li>
  <li class="color-red">
    <span class="swatch"></span>
    Red
  </li>
  <li class="color-green">
    <span class="swatch"></span>
    Green
  </li>
  <li class="color-gray">
    <span class="swatch"></span>
    Gray
  </li>
  <li class="color-dark-gray">
    <span class="swatch"></span>
    Dark Gray
  </li>
</ul>
```

这可能不是理想的标记(您肯定可以使用`::before`而不是`span.swatch`)，但是它将适用于这个演示。

一旦你完成了这些，你将需要 CSS 来为你的客户布局这些颜色样本。我们可以用 Sass 来简化这个过程。Sass 贴图是存储这些颜色值的好方法:

```
$colors: (
 blue: #2980b9,
 red: #e74c3c,
 green: #27ae60,
 gray: #95a5a6,
 dark-gray: #2c3e50
);
```

通过将这些值存储在一个映射中(而不是作为 5 个或更多的变量)，我们现在能够遍历它们并自动生成 CSS。

```
// just a little layout style to make square swatches
ul {
 margin: 0;
 padding: 0;
 text-align: center;
}

li {
 display: inline-block;
 width: 15%;
 margin: 1%;
 padding: .5em;
 box-shadow: 0 1px 4px -1px rgba(0,0,0,.5);
}

.swatch {
 display: block;
 height: 0;
 margin-bottom: .5em;
 padding: 100% 0 0;
}

// now, assign each .swatch the right background-color
@each $name, $value in $colors {
  .color-#{$name} {
    .swatch {
 background-color: $value;
    }
  }
}
```

## 标题

您也可以将地图用于标题样式。下面的例子比彩色地图稍微复杂一点。

```
$sans: Open Sans, Tahoma, sans-serif;
$serif: Droid Serif, Palatino, serif;

$headings: (
 h1: bold 2em/1.2 $sans,
 h2: light 1.5em/1.2 $sans,
 h3: bold 1.2em/1.2 $sans,
 h4: bold 1em/1.2 $sans,
 h5: bold 1em/1.2 $serif,
 h6: italic 1em/1.2 $serif
);

@each $element, $font-property in $headings {
  #{$element} {
 font: $font-property;
  }
}
```

这很好，除非你的标题风格变得更加复杂。如果您计划添加像`color`、`letter-spacing`或`text-transform`这样的属性，您将需要使用嵌套地图，如下例所示:

```
$headings-complex: (
 h1: (
 font: bold 2em/1.2 $sans,
 color: map-get($colors, blue)
  ),
 h2: (
 font: 200 1.5em/1.2 $sans,
 letter-spacing: .1em,
 text-transform: uppercase,
 color: map-get($colors, dark-gray)
  ),
 h3: (
 font: bold 1.2em/1.2 $sans,
 color: map-get($colors, dark-gray)
  ),
 h4: (
 font: normal 1em/1.2 $sans,
 letter-spacing: .1em,
 text-transform: uppercase,
 color: map-get($colors, dark-gray)
  ),
 h5: (
 font: bold 1em/1.2 $serif,
 color: map-get($colors, blue)
  ),
 h6: (
 font: italic 1em/1.2 $serif,
 color: map-get($colors, dark-gray)
  )
);

@each $element, $style-map in $headings-complex {
  #{$element} {
 @each $property, $value in $style-map {
 #{$property}: $value;
    }
  }
}
```

此时，您可能会问，“为什么不直接用 CSS 编写所有嵌套的地图数据呢？它看起来像 CSS，我让它成为一个循环并没有真正节省多少时间。”这就是使用 Sass 使事情变得更容易的伟大之处:如果一种技术不能使你更容易*，就不要使用它。但是如果这个复杂的地图适合你的项目，那就去做吧！*

 *## 小跟班

回到 Sass 地图的简单用法，按钮是另一个自动化样式图块的好机会:

```
@mixin button($color) {
 display: inline-block;
 background-color: $color;
 color: white;
 border-radius: .25em;
 line-height: 1;
 text-transform: uppercase;
 padding: .5em 1em;
 text-shadow: 1px 1px 1px rgba(0,0,0,.5);
 box-shadow:
  inset 0 .125em .5em rgba(255,255,255,.25),
  inset 0 -.125em .5em rgba(0,0,0,.25);

 &:hover {
 background-color: darken($color, 5%);
  }
}

@each $name, $value in $colors {
  .button-#{$name} {
 @include button($value);
  }
}
```

该代码片段将基于与上面调色板相同的颜色为按钮创建 CSS。如果你有不同的 UI 按钮颜色，你可以为`$button-colors`使用不同的贴图。

以下是本文中所有代码的代码栏:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看带 Sass 的钢笔[样式磁贴。](http://codepen.io/SitePoint/pen/xGgZgK/)*