# 值得一试的有用的指南针扩展

> 原文：<https://www.sitepoint.com/compass-extensions-worth-a-try/>

[Compass](http://compass-style.org/) 是一个很棒的 Sass 框架。不仅因为它提供了一百个有用的 mixins 和函数以及一个 sprite 生成器，还因为它允许作者构建自己的扩展，从而使用其他作者的扩展。

Compass 扩展是某种打包成宝石的包，可以安装、导入，然后在 Sass 样式表中使用。安装指南针扩展需要 3 个步骤:

1.  在您的终端中，运行`gem install {extension_name}`
2.  在你的`config.rb`文件(指南针)中，添加`require "{extension_name}"`
3.  在样式表中，添加`@include "{extension_name}"`

第一步是在你的机器上安装 gem。第二个告诉指南针使用宝石。第三个函数导入扩展内容，以便可以在您的 Sass 文件中使用。

很容易，不是吗？现在您已经知道了如何使用它们，如果我向您展示几个绝对值得一试的 Compass 扩展会怎么样？记住，你要做的就是运行上面列出的三个命令。此外，这些游戏中的大多数都包含在 SassMeister 游乐场中，所以你也可以在线尝试。

## 现代混合

Modernizr Mixin 是由[丹尼尔·吉兰](http://danielguillan.com/)构建的 Modernizr 类 API 的包装器。这个扩展提供了一个简洁的 API 来简化和规范 Modernizr 的类在样式表中的使用。

假设你构建了一个依赖于 CSS 变换和不透明度的东西。您可以在样式表中编写如下内容:

```
.csstransforms.opacity {
  .element {
    /* Do something if both CSS transfroms and opacity are supported */
  }
}
```

或者，如果您以相反的方式构建它，如果两者中的任何一个都不支持，则提供一个后备:

```
.no-csstransforms, .no-opacity {
  .element {
    /* Do something if either CSS transforms or opacity is not supported */
  }
}
```

虽然读起来不错，但这两种方法之间已经存在不一致。一个用`.a.b .c`，另一个用`.a .c, .b .c`。因此，乍一看，这里发生的事情可能并不明显。

Modernizr Mixin 让事情变得更好:

```
.element {
  @include yep(csstransforms, opacity) {
    // Do something if both CSS transforms and opacity are supported
  }
}
```

或者再一次，使用相反的逻辑:

```
.element {
  @include nope(csstransforms, opacity) {
    // Do something if CSS transforms and opacity are not supported
  }
}
```

在我看来，它看起来相当不错，所以如果你的应用依赖于 Modernizr，你可能想试试这个 mixin。这真的只是语法糖，但天哪，糖是好的！

我有幸让丹尼尔·吉兰在我自己的博客上写下他是如何制作 mixin 的，所以如果你想了解它的制作，一定要看看丹尼尔的文章。

## 引号

[引号](https://github.com/hagenburger/quotation-marks)是来自[尼科·哈根伯格](https://twitter.com/Hagenburger)的指南针扩展，帮助你在多语言环境中处理引号。

我们经常忘记每种语言都有自己处理引号的方式。当在同一个应用程序中处理多种语言时，遵守这些排版规则很重要。谢天谢地，CSS 为此提供了伪元素和`content`属性。

基本上，引号是一个引用的字典。它有一个与语言相关的大 Sass map ，可以通过一个简单的 mixin: `localized-quotation-marks($languages)`来使用。

假设您的站点支持四种语言:英语、法语、意大利语和德语，并使用块引号来显示客户的意见。因为您支持多种语言，并且有来自多个国家的用户，所以您希望引号适应该语言。现在让我们稍微活跃一下气氛。

```
// Importing the Compass extension
@import "quotation-marks";

// Listing all supported languages
$supported-languages: en, fr, it, de;

// Outputing accurate quote marks on `blockquote` element
blockquote {
  @include localized-quotation-marks($supported-languages);
}
```

这编译成:

```
@charset "UTF-8";

blockquote:lang(en):before, 
blockquote:lang(de):after {
  content: "“";
}

blockquote:lang(en):after {
  content: "”";
}

blockquote:lang(fr):before, 
blockquote:lang(it):before {
  content: "«";
}

blockquote:lang(fr):after, 
blockquote:lang(it):after {
  content: "»";
}

blockquote:lang(de):before {
  content: "„";
}
```

如你所见，它不仅完成了工作，而且做得很好！事实上，引号使用 Sass 占位符将相似的引号分组，以减少最终的 CSS 大小。

请注意，`blockquote:lang(de)`将在以下任何场景中匹配:

*   `<html lang="de"> <blockquote>`
*   `<blockquote lang="de">`
*   `<html lang="de"> <blockquote lang="de">`

所以这太棒了！无论你决定如何解决 HTML 前端的问题，CSS 都将保持不变，全部由引号驱动。现在没有理由离开那些丑陋的默认报价！

## 萨斯伊孔斯

SassyIcons 是一个 Sass 工具，旨在帮助处理图标精灵、SVG、PNG 回退、跨浏览器支持和所有这类困难的东西。基本上，它是一个使用 Modernizr 类的[罗盘精灵 API](http://compass-style.org/help/tutorials/spriting/) 的包装器。

它是由 Pascal Duez 设计的，与许多扩展不同，它已经在实际项目中进行了测试，所以你可以肯定它现在已经非常稳定了。

SassyIcons 有几个目标:

*   给你几个“主题”精灵的能力，管理成子文件夹(例如`social`、`ui`、`illustrations`等)。)
*   允许您选择和配置应该使用的文件格式(SVG 或 PNG)
*   帮助您实现跨浏览器支持(回到 Internet Explorer 7)和高 DPI 显示
*   提供定位灵活性，以涵盖各种棘手的情况

现在，让我们来看看代码。

首先，创建一个 sprite 工作表，给它一个名称(如果愿意，还可以加上一个空格)。

```
@include sprite-map-create(social, $spacing: 10px);
```

这就是使用 Compass sprite API 的地方。那里没有输出 CSS，但是生成了两个文件:一个用于常规用途，另一个用于高密度显示。然后生成一组占位符供内部使用。

现在，每当您想要使用图标时:

```
.twitter {
  @include icon(social, twitter);
}
```

第一个参数是 sprite 工作表名称，第二个是图标名称。生成的 CSS 取决于选项，但在大多数情况下，您会希望使用内嵌的 SVG 和 PNG 回退，从而导致:

```
.twitter {
  background-image: url('data:image/svg+xml;base64,...');
  background-repeat: no-repeat;
}

.no-svg .twitter, 
.no-js .twitter/*, 
 other icons as well */ {
  background-image: url('../img/icons/social/png-s62add47933.png');
  background-repeat: no-repeat;
}

.no-svg .twitter, 
.no-js .twitter {
  background-position: 0 -186px;
}
```

最好不要手写这个，嗯？

如果您不想要 SVG 而只想要 PNG，您可以这样做:

```
.twitter {
  @include icon(social, twitter, $format: "png");
}
```

然后:

```
.twitter/*,
 other icons as well */ {
  background-image: url('../img/icons/social/png-s62add47933.png');
  background-repeat: no-repeat;
}

@media (-webkit-min-device-pixel-ratio: 1.3), (min-resolution: 125dpi), (min-resolution: 1.3dppx) {
  .twitter/*,
   other icons as well */ {
    background-image: url('../img/icons/social/png_2x-s177eed3133.png');
    background-size: 32px auto;
  }
}

.twitter {
  background-position: 0 -186px;
}

@media (-webkit-min-device-pixel-ratio: 1.3), (min-resolution: 125dpi), (min-resolution: 1.3dppx) {
  .twitter {
    background-position: 0 -141px;
  }
}
```

由于 PNG 不是一种矢量格式，这里它涵盖了具有特定媒体查询和@2x sprite 的高密度显示。

正如您所看到的，SassyIcons 是插入 Compass sprite 扩展的一个非常强大的工具。如果你已经使用了罗盘精灵，我建议你不要再使用罗盘精灵了。

顺便说一下， [grunt-svg2png](https://github.com/pascalduez/grunt-svg2png) 也是由 Pascal 开发的，所以这两个工具完全兼容。伟大的组合，只处理 SVG，自动化所有的 PNG 的东西。

## 时尚出口

[SassyExport](https://github.com/ezekg/SassyExport) 最初是作为 [SassyJSON](https://github.com/HugoGiraudel/SassyJSON) 的扩展而来，这是一个用 Sass 编写的 JSON 解析器。我建议[Ezekiel Gabrielse](http://ezekielg.com/)(
sassy export 的作者)通过将 JSON 编码/解码转移到 Ruby 来摆脱对 SassyJSON 的依赖。他照做了。

SassyExport 是一个 Compass 扩展，它只做一件事:将 Sass 映射导出到 JSON。因此，它以单个 mixin 的形式提供了一个非常简单的 API，`SassyExport()`。

```
@import "SassyExport";

$map: (
  hello: world,
);

@include SassyExport("/json/hello.json", $map, $pretty: true);
```

第一个参数是指向要创建/更新的 JSON 文件的路径。这里最酷的是它相对于`config.rb` Compass 文件，该文件必须在项目的根级别。所以不管你在哪里调用这个 mixin，它总是会指向同一个文件。

第二个参数是需要转换成 JSON 并导出的地图。第三个问题是它是否应该印刷精美。如果`false`，那么 JSON 被内联。

当您希望在 Sass 和 JavaScript 之间同步断点，但仍然在样式表中定义它们时，SassyExport 会很方便。

```
// _config.scss
$breakpoints: (
  "small": 767px,
  "medium": 992px,
  "large": 1200px
);

// _export.scss
@import "SassyExport";

@include SassyExport("/json/breakpoints.json", $breakpoints);
```

然后，您会得到这样一个`.json`文件:

```
{
  "small": "767px",
  "medium": "992px",
  "large": "1200px"
}
```

从那里，您可以用 Ajax 请求或其他方式请求这个文件。然后，您可以只在到达`small`断点时执行一些 JavaScript。

如果稍后您将这个`767px`更改为，比方说，`750px`，那么您的 JavaScript 将不会不同步——这要感谢 SassyExport。

## Sass 颜色助手

不幸的是，来自 [Voxpelli](https://twitter.com/voxpelli) (SCH)的 [Sass Color Helpers](https://github.com/voxpelli/sass-color-helpers) 还不是指南针的扩展，但我认为它值得一提。顾名思义，SCH 提供了几个 Sass 函数来帮助处理颜色。

除此之外，它还提供了一个函数来计算当覆盖指定的底部颜色时，颜色实现特定目标颜色的准确 alpha 通道。

这在从平面图像中提取颜色时非常有用，在这种情况下，您可以估计渲染在另一种颜色之上的两种颜色，但需要帮助计算出顶部颜色的 alpha 数以实现图像中的目标颜色。

例如，想象在一幅图像上有一个稍微透明的黑盒。当从图像中传递颜色时(颜色选择？)、目标颜色(来自实体模型)和盒子的颜色(在我们的例子中是黑色)传递给`ch-calculate-alpha`函数，它应该返回近似的 alpha 通道以应用于覆盖图，从而获得想要的结果。

让我们用一些代码来说明我们的例子:

```
$overlay-color: black;
$target-color: #16110e; // Color picked from the mock-up
$bottom-color: #5e534f; // Color picked from the mock-up
$alpha: ch-calculate-alpha($overlay-color, $bottom-color, $target-color); // 0.79464
```

因此，为了达到预期的效果，您需要将以下颜色应用到半透明黑色叠加层:

```
.overlay {
  $overlay-color: black;
  $target-color: #16110e;
  $bottom-color: #5e534f;
  $alpha: ch-calculate-alpha($overlay-color, $bottom-color, $target-color);

  // Accurate declaration
  background: $overlay-color; // Fallback IE 8
  background: rgba($overlay-color, $alpha);
}
```

我们得到以下结果:

```
.overlay {
  background: black;
  background: rgba(0, 0, 0, .79464);
}
```

或者，SCH 提供了反函数，根据叠加的 alpha 通道和底部的颜色计算顶部的颜色，所以实际上由您决定使用哪一个。

此外，SCH 还公开了一个返回两种颜色之间对比度的`ch-color-contrast`函数(基于 Lea Verou 的[这个伟大的工具](http://leaverou.github.io/contrast-ratio/)),以及一个`ch-best-color-contrast`，它返回给定颜色列表中具有最佳对比度的颜色，并将第一个参数传递给该函数。

```
$color-contrast: ch-color-contrast(red, purple); // 2.4
$best-color: ch-best-color-contrast(red, purple blue green yellow); // yellow
```

当您有动态背景，并且想要计算最佳的文本颜色来匹配给定的背景时，这可能是有用的。

最后但同样重要的是，SCH 提供了一些 HSV 和 HSB 到 HSL 的转换功能，在处理 Photoshop 中的 HSV 系统时非常完美。

## 最后的想法

还有许多更出色的指南针扩展和 Sass 工具可用，包括流行的网格系统，如[奇点](http://singularity.gs/)和 [Susy](http://susy.oddbird.net/) 。但是我认为我们也看到了一些不为人知的扩展，这很好。

使用中的 [Sache .寻找 Sass 扩展变得比以往任何时候都容易。你那里有吗？:)](http://sache.in)

## 分享这篇文章