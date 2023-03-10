# 使用 AIR 和 CSS3 享受未来的网络

> 原文：<https://www.sitepoint.com/tomorrows-web-today-air-css3/>

**我们中的许多人都哀叹在最常用的浏览器中缺乏统一实现的 CSS3 特性。我们梦想用精简的 CSS 实现最小化的语义标记的乌托邦，提供我们所能想象的最棒的网站。或者，见鬼，我们甚至只是思考使用一个简单的`opacity`属性会是什么样子，而不用担心添加所有浏览器特定的扩展来完成跨浏览器的壮举。**

在 Adobe AIR 中开发 web 应用程序可能会是一种令人扫兴的体验。您已经获得了 WebKit 渲染引擎的强大功能——它也支持 Safari——触手可及。AIR 版本的 WebKit 引擎与最新版本的 Safari 略有不同，但仍有大量 CSS 技巧隐藏其中；它不仅使构建 web 应用程序更容易，而且会让您渴望看到这些特性在所有主流浏览器中实现。

在本文中，我们将介绍这些可爱的属性，以及如何在自己的 AIR 项目中使用它们。查看本文的[代码档案，为自己编译。](https://i2.sitepoint.com/examples/AdobeAIR/css3-examples.air#lt#/demo application by running the AIR installer/#el#/, or downloading the /#l#/https://i2.sitepoint.com/examples/AdobeAIR/css3-examples.zip)

最后会有一个[测验](https://www.sitepoint.com/quiz/air/tomorrows-web-today-air-css3)，这样你就可以测试你的注意力有多集中了！前 100 名完成测验的人将赢得一本口袋指南*Adobe AIR For JavaScript Developers*，免费送到他们家门口，这要感谢 Adobe。你也可以[在限定时间内免费下载这本书的 PDF 格式](https://www.sitepoint.com/quiz/air/tomorrows-web-today-air-css3)，开始破解吧！

##### 不透明

从定制的工具提示到花哨的灯箱，半透明的元素非常符合 Web 2.0。在灯箱的情况下，有许多方法来创建屏幕。

当前浏览器的一种流行方法是获取一个元素，将其绝对定位，从屏幕的一个角拉伸到另一个角，并使用 CSS3 的不透明度和 Internet Explorer 过滤器设置不透明度。

wild Web 中的另一种选择是使用带有半透明 alpha 通道的 PNG 背景图像。这是一个不错的解决方案，因为它允许元素中嵌入内容，但是对于我们当中的纯粹主义者来说，额外的图像确实有点脏。

幸运的是，大多数浏览器(目前除了 Internet Explorer)都支持使用`rgba()`设置颜色，这是我推荐在 AIR 应用程序中使用的技术:

```
background-color: rgba(255, 0, 0, 0.7);
```

`rgba`属性允许指定`red`、`green`和`blue`——以及用于设置不透明度的第四个参数`alpha`——而不需要子元素继承它。在这种情况下，背景应该是 70%不透明度的红色。这是一行代码，没有图像。在空气中不成问题。

因为这是一种指定颜色的方法，所以您也可以使用它以完全相同的方式设置文本或边框颜色。对于边框颜色，你必须特别小心。当元素有背景色或图像时，它会延伸到边框的边缘。如果您希望背景只延伸到填充的边缘，您可以使用`-webkit-background-clip`属性。

```
border: 5px solid rgba(255,0,0,.6); 
-webkit-background-clip: padding;
```

![A white border with opacity can automatically adjust to different backgrounds.](img/8f6f08e1b090055b776f9645a48403ab.png)

完美的获得一个元素周围的玻璃边界！

##### 背景

正如您刚才看到的，背景颜色可以很容易地设置为包括一定程度的透明度。更酷的是支持多种背景。你不再需要做四个单独的`div`来将图像锁定到每个角落，以实现你所有的圆角优点。

```
background: url(assets/top-left.png) no-repeat 0 0, 
         url(assets/bottom-left.png) no-repeat 100% 100%;
```

![A single background image placed into the same element twice by repeating the background position.](img/24c3342f9d7088d0f2a77ee1cd0c75d2.png)

通常情况下，你只能使用默认的图像大小，或者只能向一个方向或另一个方向平铺，或者两个方向都平铺，或者根本不平铺。在 AIR 的 WebKit 中，您可以指定背景图像的大小，这使您可以非常灵活地重用图像，如渐变，并使它们伸展到可用空间，而不是重复。

```
-webkit-background-size: 100%; 
-webkit-background-size: 100% 100%;
```

背景大小可以取一个或两个值。如果有两个值，第一个是宽度，第二个是高度。如果对不同的图像应用不同的背景尺寸，您需要为每个图像指定宽度和高度。

```
-webkit-background-size: 100% 50%, 50% 100%;
```

##### 边界

让我们把“哦！”回到 Web 2.0。当然，我们可能有半透明的彩色边框，但它们需要是圆形的！

```
-webkit-border-radius: 5px;
```

就像这样，我们已经得到了你需要的所有圆角，而不必使用一个单一的图像。

随着 Internet Explorer 的退出，`border-radius`属性正被越来越多的主流用户使用。(不过，如果你富有冒险精神，你可以指望 VML 来解决你对圆角的迷恋。(或者查看雷米兹的[网络浏览器行为。)](http://www.htmlremix.com/curved-corner-border-radius-cross-browser/)

我们现在离 CSS3 涅槃越来越近了，但是如果你想要有渐变和特殊角点的真正花哨的边框呢？破解开放边界图像！边框图像的工作原理是获取一幅图像，并根据所提供的值将其分割成九个部分。边框图像的简写有点混乱，所以我将详细描述每个属性:

```
-webkit-border-image: uri top right bottom left x_repeat y_repeat
```

URI 是您的图像所在的位置，然后`top`、`right`、`bottom`和`left`的值决定了应该使用边缘的多少像素。`x_repeat`和`y_repeat`允许您定义如何处理水平和垂直切片。它们可以是重复、拉伸或循环(拉伸所有九个部分)。这里有一个例子:

```
-webkit-border-image: url(example.gif) 5px 5px 5px 5px repeat stretch; 
```

Snitter 中默认主题上使用的阴影正是利用了这种技术，通过使用带有 alpha 通道的 PNG。

![A single image with a limited colour palette is used to create this shadow effect.](img/e9dec694c2913d4b99b44a0eaf544fb8.png)

##### 列

虽然多栏布局没有得到很多人的喜爱——可能是因为在灵活的设计环境中很难有效地实现它们——但你可能会发现有必要将内容分解成报纸风格的多栏方法。例如，您可能希望创建一个基于 Adobe AIR 的报纸或 RSS 客户端，它可以将内容加载到多栏布局的下行长度中。

要让一个元素扩展到多个列，请使用 column-count。它将获取父元素的现有宽度，并在各列之间平均分配。

指定每列之间应该出现的间距也是一个好主意。这与填充类似，只是最左侧和最右侧的列仍将流向容器的最外边缘。

```
 -webkit-column-count: 3; 
  -webkit-column-gap: 20px;
```

您还可以定义一个列标尺，它将包括一条与列间距中间对齐的水平线。

```
-webkit-column-rule: 1px solid #333;
```

或者，您可以使用固定的列宽来代替`-webkit-column-count`。调整窗口大小时，列宽保持一致；随着窗口变宽，会添加更多的列。

```
 -webkit-column-width: 200px;
```

![A multi-column layout using -webkit-column-width.](img/ad37b7b60ca69bcfe09c54b8627d7e82.png)

##### 转换

我们都听说过 Safari 中的 CSS 转换。变换允许对 HTML 元素进行缩放、倾斜和旋转。想象一下[在空中轻松实现 Coverflow 风格的功能](http://paulbakaus.com/lab/js/coverflow/)。

虽然它们不在 AIR 1.1 版本中，但随着 Adobe AIR 中的 WebKit 引擎得到更新，我们将开始看到一些功能在 1.5 版本中出现。预计 1.5 版本将很快推出。

##### 以及其他

Adobe 做出了明智的选择，使用 WebKit 作为 AIR 的核心引擎。WebKit 团队添加了大量 CSS3 功能，Adobe AIR 开发人员将从中获益。例如，在 1.5 版本中进行转换将有助于提升基于 JavaScript 的 AIR 开发，因为使用 Flash 制作效果的理由将会减少。现在，去试验一下，看看几年后网络的其他部分会是什么样子。

##### 自我测验

**通过一个[小测验](https://www.sitepoint.com/quiz/air/tomorrows-web-today-air-css3)来测试你对本文的理解，并获得免费的袖珍指南*Adobe AIR For JavaScript Developers*，感谢 Adobe Systems 免费送货上门。此优惠仅限前 100 人，所以赶快加入吧(如果你错过了，你还可以免费下载 PDF 格式的书)。**

**[参加小测验！](https://www.sitepoint.com/quiz/air/tomorrows-web-today-air-css3)**

## 分享这篇文章