# 带有 Sass 和指南针的 CSS 精灵

> 原文：<https://www.sitepoint.com/css-sprites-sass-compass/>

构建健壮的网站和应用程序不仅仅是编写语义 HTML 和花哨的 CSS。从项目一开始就关注 web 性能也很重要。有许多技术可以帮助做到这一点，从而创造更好的用户体验。创建 CSS 精灵就是其中之一。使用这种技术，你可以将所有的小图片(精灵)组合成一个更大的图片(精灵表),并用 CSS 来定位它。

你可以使用不同的工具来生成你的精灵，但是在这篇文章中我会告诉你如何使用[指南针](http://compass-style.org/)来实现它。在此之前，让我向您介绍一下本文将用到的项目。

## 设置项目

为了更好地展示 Compass 的 sprite builder 是如何工作的，我建立了一个新项目。这是它的结构:

```
Compass-Sprites/
├── config.rb
├── index.html
├── stylesheets/
│     ├── ie.css
│     ├── print.css
│     └── screen.css
├── sass/
│     ├── ie.scss
│     ├── print.scss
│     └── screen.scss
└── images/
    └── icons/
        ├── arrow-down.png
        ├── facebook-active.png
        ├── facebook-hover.png
        ├── facebook.png
        ├── twitter-active.png
        ├── twitter-hover.png
        ├── twitter.png
        └── zuckerberg.png
```

注意，在 images 文件夹中有一个`icons`文件夹，它包含了这个项目将使用的所有图像。

你可以通过[下载这个 zip 文件](https://uploads.sitepoint.com/wp-content/uploads/2014/12/1417073491Compass-Sprites.zip)来抓取这个项目的所有文件。

## 基本脚本

创建一个 sprite 工作表有 4 个步骤:

1.  从相应的模块( [Utilities](http://compass-style.org/reference/compass/utilities/) )导入所需的 Sass 资产(变量、混合、函数)。
2.  导入`icons`文件夹中的所有图像(精灵)。
3.  包括为精灵生成 CSS 类的 Sass mixin。
4.  将生成的类添加到相关的 HTML 元素中。

更具体地说，在`screen.scss`文件中，我们添加了以下指令:

```
@import "compass/utilities/sprites";
@import "icons/*.png";
@include all-icons-sprites;
```

同样，请注意`icons`是包含图像的文件夹的名称。

只要我们保存并编译`screen.scss`文件，sprite 工作表就会在`images`文件夹中创建。sprite 工作表的名称如下所示:

```
icons-s82ea51d311.png
```

它的名字保留了以下约定:`<Images_Folder_Name>-<Cache_Buster>`。每次我们更新 sprite 工作表时，缓存破坏者都会改变。这样，浏览器将始终拥有并使用它的最新版本。

我们项目的初始 sprite 表看起来会像这样:

![default compass sprite compilation](img/b468502e3a28760e2444b0b9a066c1fd.png)

这是 Compass 为我们生成的 CSS 类:

```
.icons-sprite,
.icons-arrow-down,
.icons-facebook,
.icons-twitter,
.icons-zuckerberg {
 background-image: url('img/icons-s82ea51d311.png');
 background-repeat: no-repeat;
}
```

以上类的名字有如下约定:`<Images_Folder_Name>-<Image_Name>`。

对于每个图像，Compass 应用不同的背景位置。例如，下面是`zuckerberg.png`图像的`background-position`:

```
.icons-zuckerberg {
 background-position: 0 -232px;
}
```

此时，我们将生成的类添加到相应的 HTML 元素中。

以下是与`zuckerberg.png`图像相关的元素:

```
<h1 class="icons-zuckerberg">Mark Zuckerberg</h1>
```

请记住，Compass API 只能为 PNG 图像生成一个 sprite 表。

## 魔法选择器

Compass 能够使用 sprite 魔法选择器为不同的状态创建类。

例如，在我们的项目中有`facebook.png`图像。假设我们希望为`hover`和`active`州的图像使用不同的背景颜色。在这种情况下，我们添加了`facebook-hover.png`和`facebook-active.png`图像。这些图像的命名要遵循约定:`<Image_Name>-<State>`。

之后，Compass 生成以下 CSS 类:

```
.icons-facebook:hover, .icons-facebook.facebook-hover {
 background-position: 0 -72px;
}
.icons-facebook:active, .icons-facebook.facebook-active {
 background-position: 0 -40px;
}
```

## 定制生成的类

在前面的章节中，我们看到了 Compass 如何生成 CSS 类。但是，如果我们想要定制这些类的名字呢？希望我们可以使用`<Images_Folder_Name>-sprite(Image_Name)` mixin，而不是`all-<Images_Folder_Name>-sprites` mixin。

为了说明这一点，让我们假设我们想要更改`icons-zurckerberg`类的名称。在`screen.scss`文件中，我们添加了以下类:

```
.creator {
 @include icons-sprite(zuckerberg);
}
```

该类派生如下:

```
<Desired_Class_Name> {
 @include <Images_Folder_Name>-sprite(Image_Name);
}
```

这是创建的 CSS，可以应用于我们想要的元素:

```
.creator {
 background-position: 0 -232px;
}
```

注意，唯一改变的是类属性的名称。CSS 属性具有与第一个 mixin 相同的值。

第二个 mixin 的优点是它为您提供了创建自定义类的选项。但是，如果您的项目有很多图像，这可能不是一个好的解决方案。

## 配置选项

Compass 允许我们使用附加的配置变量来定制 sprite 工作表。以下是其中的一些:

*   `$sprite-selectors`:决定魔术精灵选择器启用的状态。可能的值:`hover`、`target`、`active`、`focus`。默认值:`hover`、`target`、`active`、`focus`。
*   决定精灵是否包含魔法选择器。可能的值:`true`或`false`。默认值:`false`。
*   `$icon-layout`:决定精灵表中精灵的布局。可能的值:`horizontal`、`vertical`、`diagonal`、`smart`。默认值:`horizontal`。
*   `$icon-spacing`:以像素为单位确定每个精灵之间的间距。默认值:`0`。
*   `$sprite-dimensions`:决定生成的 CSS 类是否包含每个 sprite 的尺寸。默认值:`false`。
*   `$default-sprite-separator`:决定精灵的默认分隔符。可能的值:`-`或`_`。默认值:`-`。

上面的变量应该在创建 sprite 工作表之前定义。这意味着，在添加以下指令之前:

```
@import "icons/*.png";
```

现在让我们在我们的项目中用其中的两个进行实验。

作为第一次尝试，我们将 sprite 工作表布局更改为`smart`，添加以下变量:

```
$icons-layout:smart;
```

这将导致以下精灵表:

![compass sprites smart compilation](img/939318a6ebbad7451de0c4facc39c162.png)

值得一提的是，我们不能同时使用`smart`方法和`$icon-spacing`变量。发生这种情况是因为该方法以最佳方式排列精灵，尽可能多地移除精灵之间的空白。

现在让我们看看添加下面的变量会发生什么:

```
$icons-sprite-dimensions:true;
```

编译时，我们会得到一个生成类的 CSS 输出，如下所示:

```
.icons-facebook {
 height: 32px;
 width: 32px;
}
```

发生了什么事？Compass 能够测量每个图像的尺寸，并将其输出到编译后的 CSS 中。这是一个很好的特性，当 sprite 工作表包含不同尺寸的图像时，这个特性会很有用。然而，如果我们有相同尺寸的图像，这会导致 CSS 臃肿。

## 超越基础

正如我们之前所看到的，创建一个 sprite 表需要以下魔法指令:

```
@import "<Images_Folder_Name>/*.png";
@include all-<Images_Folder_Name>-sprites;
@include <Images_Folder_Name>-sprite(Image_Name);
```

除此之外，我们还可以使用一些额外的函数和混合来创建 sprite 工作表。以下是其中的一些:

*   `sprite-map`:创建包含图像的 CSS 精灵贴图的函数。
*   `sprite`:返回图像及其`background-position`的 mixin。
*   `sprite-dimensions`:返回精灵尺寸的 mixin。

为了更好地理解这些是如何工作的，让我们再次假设我们想要创建一个名为`creator`的定制类。我们定义精灵贴图(请求一个`smart`布局)并得到`icons-zurckerberg.png`图像的`background-position`。然后，我们检索这个图像的尺寸。之后，我们准备将这个类分配给所需的 HTML 元素。

下面是调用上述内容的代码:

```
$icons: sprite-map("icons/*.png", $layout: smart);

.creator {
 background: sprite($icons, zuckerberg);
 @include sprite-dimensions($icons, zuckerberg);
}
```

这是 CSS:

```
.creator {
 background: url('img/icons-s855a77086a.png') 0 -232px;
 height: 190px;
 width: 190px;
}
```

## 结论

在本文中，我简要地向您介绍了 CSS sprites 技术背后的概念。然后，我向您展示了指南针的精灵生成器的基础。这里有更多的选项，但是我希望你已经了解了用指南针生成精灵的想法。

## 分享这篇文章