# iPhone 开发:让您入门的 12 个技巧文章

> 原文：<https://www.sitepoint.com/iphone-development-12-tips/>

如果你在过去的一年里一直是个优秀的开发者，也许圣诞老人会给你带来一部闪亮的新 iPhone。如果他这样做了，你肯定花了几个小时玩它——浏览、下载应用程序、发微博，总之玩得很开心。

现在，作为一名 web 开发人员，您可能想知道“查看源代码”到底在哪里，以及如何在 iPhone 上创建外观和功能都很棒的网站和应用程序。这里有一些让你开始的提示和技巧——最重要的是，如果你还没有购买大件商品，你可以在模拟器上练习。

##### 1:文档和 iPhone 模拟器

当彼得-保罗·科赫开始玩 iPhone 版的 Safari 时，[宣称“没有文档”——但从那以后情况发生了变化。苹果现在让](http://www.quirksmode.org/blog/archives/2008/08/iphone_events.html) [iPhone 特有的网络开发信息](http://developer.apple.com/safari/library/documentation/AppleApplications/Reference/SafariWebContent/Introduction/chapter_1_section_1.html#//apple_ref/doc/uid/TP40002079-SW1)成为 Safari 文档的一部分。

iPhone SDK(软件开发工具包)是 Mac 专用的，也包含一个 iPhone 模拟器——如果你已经拥有一部 iPhone，这将非常方便，当然，如果你还没有加入 iPhone 大军，这是绝对必要的。要访问它，你需要[注册为 iPhone 开发者，](http://developer.apple.com/iphone/)并安装 iPhone SDK。一旦你安装了它，你会在`/Developer/Platforms/iPhoneSimulator.platform/Developer/Applications`中找到 iPhone 模拟器。

##### 2:小屏幕 CSS

iPhone 和你的 PC 或 Mac 最明显的区别就是 iPhone 的屏幕要小得多。如果你已经有了一个可以在桌面浏览器和屏幕阅读器上运行的页面，一个简单的策略就是再加一个只在 iPhone 上使用的样式表。下面是您要使用的标记:

```
<!--[if !IE]>--> 
 <link  
    rel="stylesheet"  
    href="small-screen.css"  
    type="text/css"  
    media="only screen and (max-device-width: 480px)"  
 /> 
 <!--<![endif]-->
```

这个链接元素使用一个[媒体查询](https://reference.sitepoint.com/css/mediaqueries)来定位最大宽度为 480 像素的设备——这是 iPhone 处于横向时的可用宽度。[IE 5.5 或更早版本需要 IE 条件注释](https://reference.sitepoint.com/css/conditionalcomments)；这些版本缺乏对媒体查询的支持，将加载用于 iPhone 的样式表。让我们试一个例子。

在[示例 1 中，](https://www.sitepoint.com/examples/iphone-development-12tips/small-screen-css.html)我们可以看到一个页面，向 iPhone 查看者显示一个红色框，向其他查看者显示一个蓝色框。一个样式表， [common.css，](https://www.sitepoint.com/examples/iphone-development-12tips/common.css)包含了适用于所有浏览器的样式，包括蓝框的声明。另一个， [small-screen.css，](https://www.sitepoint.com/examples/iphone-development-12tips/small-screen.css)用于手持设备，包含红盒子的声明。当你在 iPhone 中加载这个例子时，你会看到红色的框；在你的桌面上，你会看到蓝色。

如果您喜欢只使用一个样式表，您可以使用一个`@media`块将 iPhone 专用的样式添加到主样式表中，如下所示:

```
@media only screen and (max-device-width: 480px) { 
  #test-block { 
    background: red; 
  } 
 }
```

我们可以在[示例 2 中看到一个使用内联样式表的例子。](https://www.sitepoint.com/examples/iphone-development-12tips/small-screen-css-inline.html)在它使用的样式表中， [onestyleforall.css](https://www.sitepoint.com/examples/iphone-development-12tips/onestyleforall.css) ，你会在`@media`块中看到红色背景的声明。

##### 3:视口元元素

默认情况下，iPhone 上的 Safari 会在大屏幕上呈现您的页面，就像是桌面浏览器一样，网页内容的宽度为 980 像素。然后，它会缩小内容，使其适合小屏幕。结果，用户看到你的页面上的字母很小，难以辨认，必须放大才能看到有趣的部分。这对于你的网页来说可能没问题，但是如果你正在设计一个看起来像本地应用的 web 应用，这是不可接受的。

幸运的是，这很容易使用特殊的视窗`meta`元素来纠正:

```
<meta name="viewport" content="width=device-width" />
```

此元素指示浏览器使用设备的宽度作为视区的宽度，而不是默认的 980 像素。我们可以从两个例子中看出区别。

*   [例 3](https://www.sitepoint.com/examples/iphone-development-12tips/no-viewport.html) 显示了一个简单的段落元素，没有 viewport `meta`元素。字体几乎不可读。

![Text is tiny without a viewport specified](img/b80bba01a88784f267f9e917777afe6e.png)

*   [例 4](https://www.sitepoint.com/examples/iphone-development-12tips/viewport.html) 包含 viewport 元素。现在设备宽度只有 320 像素，字体大小更容易看到。

![This text is much easier to read.](img/f8b50d218c093cf8187ddce51b9d683f.png)

或者，您可以将`device-width`值设置为您喜欢的任何数字。例如，假设您的博客有一个固定宽度为 750 像素的布局，因此它非常适合 800×600 的桌面屏幕。[例 5](https://www.sitepoint.com/examples/iphone-development-12tips/fixed750.html) 展示了这种布局的简化版:如果你把它加载到 iPhone 中，你会看到空白填充了剩下的 980 个像素。

为了消除额外的空间，您可以使用 viewport `meta`元素并将宽度设置为 780px:

```
<meta name="viewport" content="width=780" />
```

[例 6](https://www.sitepoint.com/examples/iphone-development-12tips/fixed750-viewport.html) 显示了我们的固定布局的一个版本，带有这个 viewport 元元素。

`meta` viewport 元素的内容也可以包含多个逗号分隔的值，例如`initial-scale`——用户最初查看页面时的缩放级别——以及界面是否可由用户缩放。对于 web 应用程序，一组常见的值是:

```
<meta name="viewport" content="width=device-width,initial-scale=1,user-scalable=no" />
```

这个元素将宽度设置为最大设备宽度(正如我们之前看到的)，放大到一个缩放级别，并且还禁止用户放大和缩小内容。这对于看起来和感觉上都像本地应用程序的 web 应用程序来说很方便。

**Go to page:** [1](https://sitepoint.com/iphone-development-12-tips) | [2](https://sitepoint.com/iphone-development-12-tips-2/) | [3](https://sitepoint.com/iphone-development-12-tips-3/)

## 分享这篇文章