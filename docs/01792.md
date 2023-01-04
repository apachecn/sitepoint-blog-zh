# 使用 Fontello 仅加载您需要的图标字体

> 原文：<https://www.sitepoint.com/using-fontello-to-only-load-font-icons-that-you-need/>

![Fontello in newspaper clipping letters](img/868ad0ba275f0ee7b1689d00baae6522.png)

在网络上常见的是一系列简单图形元素使用的图标字体，从网站上共享按钮内的社交媒体图标显示到告诉读者一篇文章评论数量的语音气泡图标。

图标字体有许多非常有用的特性。首先，与常规图像不同，字体图标在高分辨率屏幕上没有像素化。它们可以根据需要任意放大，而不会模糊不清。其次，它们的很多属性可以直接使用 CSS 来控制。你可以改变它们的大小和颜色，或者用几行 CSS 应用文本阴影。它们也比图像精灵更容易使用。

在深入了解图标字体之前，如果你正在从头开始一个新的网页设计，请记住使用 [SVG 图像可能是一个更好的方法](https://css-tricks.com/icon-fonts-vs-svg/)。过去我们已经在 SitePoint 探索过这两种方法的比较[。SVG 图像有一些明显的优势，例如，您可以创建多色图标，并且您的 SVG 图像看起来也比图标字体更清晰。另一方面，如果您的项目需要支持遗留浏览器，或者您的团队还没有采取措施改变其工作流以适应 SVG 图标，那么至少有必要研究优化图标字体使用的方法。](https://www.sitepoint.com/icon-fonts-vs-svg-debate/)

如果你以前用过图标字体，你很可能用过或者听说过[字体牛逼](http://fontawesome.io/)。在 4.7 版本中，Font Awesome 提供了 675 种不同的图标供用户在他们的项目中使用。这种字体提供的图标范围很广，从脸书和 Twitter 图标到像浴缸和温度计这样的对象。唯一的问题？你可能不会在一个项目中使用所有 675 个图标。加载整个字体文件只是为了使用大约 10 或 20 个图标，这会不必要地增加页面加载时间。这个问题不仅仅是字体牛逼的问题，而是大多数图标字体的问题。

在本教程中，您将了解 [Fontello](http://fontello.com/) 。这是一个很棒的服务，可以免费使用，并允许您从矢量图像创建图标字体。你也可以在一个文件中组合多个开源项目的图标，比如 Font Awesome、 [Entypo](http://www.entypo.com/) 和 [Typicons](http://typicons.com/) 。

## 使用内置图标创建字体

创建你自己的字体的第一步是从 Fontello [主页](http://fontello.com/)中选择你需要的所有图标。假设你想在社交媒体按钮上使用字体图标。你可以从选择脸书、Twitter、Pinterest 和 LinkedIn 的图标开始。

一旦你选择了所有的图标，你可以点击“自定义名称”标签来改变图标的名称。例如，脸书图标的名字是`icon-facebook`，但是如果你愿意，你可以把它改成`icon-fb`。

前缀`icon-`也可以通过点击设置按钮进行更改。

![Finding the CSS prefix option in Fontello](img/cd8889d340fb721077900405d0e1579f.png)

更改名称后，您还可以更改每个图标的代码。你看到上面图片右上角写的“社交媒体”了吗？这是生成字体文件时将使用的名称。

一旦你对图标和它们的名字感到满意，你可以点击下载网页字体按钮来下载字体。

## 在项目中使用创建的字体

您可以提取下载 zip 文件，开始使用图标。解压缩后，您应该会看到两个名为“css”和“fonts”的文件夹。还应该有一个“config.json”文件。该文件包含所有字体的配置信息。如果您想对现有项目进行更改，您可以直接将该文件拖至 Fontello 页面，或者通过转至`Settings > Import`导入该文件。

要使用图标，您应该将所有文件复制到您自己的项目目录中。请确保保持文件夹结构完整。这将简化将来对字体进行任何更改的过程。

然后，您可以在项目中包含“social-media.css”文件。通过将`<i class="icon-classname"></i>`添加到标记中，可以在您的网页上使用图标。这里，`icon-classname`是您想要显示的图标的名称。

## 使用自定义图像创建字体

丰泰洛有很多图标供你选择。有各种不同天气的图标、社交网站、图表等等。但是，任何图标字体都可能不提供您正在寻找的图标。

您可以将自定义字体和图像以 SVG 格式导入 Fontello。如果你的字体是 TTF / OTF 或 WOFF 格式，你可以使用类似于 [FontForge](http://fontforge.github.io/en-US/) 的工具将其转换成 SVG。

当导入 SVG 图像时，您需要对您的 SVG 进行一些更改，以便正确转换。你将不得不删除所有的填充和颜色。同样，也不应该有任何复杂的规则，比如`evenodd`填充。您还需要移除粗线属性，并将所有轮廓合并成一个轮廓。[本页](https://github.com/fontello/fontello/wiki/How-to-use-custom-images)详细描述了准备导入图像的过程。

## 这些改进有多大意义？

使用 Fontello 可以显著降低页面重量。如果你使用的是 Font Awesome 版本，浏览器加载的字体文件大小约为 75kb。然而，在我的例子中，用 Fontello 创建的字体文件只有 4kb 大小。

以下是我用过的图标:

在 [CodePen](http://codepen.io) 上通过 SitePoint ( [@SitePoint](http://codepen.io/SitePoint) )查看钢笔 [Fontello 图标字体](http://codepen.io/SitePoint/pen/Bprmyx/)。