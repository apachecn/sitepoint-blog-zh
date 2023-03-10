# 使用 Stellar.js 的视差滚动介绍

> 原文：<https://www.sitepoint.com/introduction-parallax-scrolling-using-stellar-js/>

过去几年中讨论最多的网页设计趋势之一是视差滚动效果。不管你喜不喜欢，它被很多网站使用。在本教程中，我将简要介绍视差滚动，并展示我们如何使用一个名为 Stellar.js 的 jQuery 插件在网站中再现它。

## 什么是视差滚动？

视差滚动是指背景以不同于前景的速度移动，当您向下滚动页面时会产生 3D 效果。这种效果对任何网站来说都是一个很好的补充，但不幸的是，如果被滥用，它会很烦人。有时你会发现网站完全被这种效果所驱动，你的体验不会是优雅的。因为这种效果通常在背景中动画化图像，所以网站的总权重大大增加，导致加载非常慢。

在我看来，这种滥用的一些例子是[展示 Kinvara 3](http://community.saucony.com/kinvara3/) 的 Saukoni 网站，以及众所周知的[Oakley–我是无敌的网站](http://moto.oakley.com/)，其**权重约为 20Mb** (之前约为 50Mb！).

现在你对这个效果有了一个概念，让我们看看如何使用 Stellar.js 来创建它。

## Stellar.js 是什么？

[Stellar.js](https://github.com/markdalgleish/stellar.js/) 是一个 jQuery 插件，可以让你轻松地给网站添加视差滚动效果。尽管它不再被维护，但它仍然非常稳定，与最新版本的 jQuery 兼容，许多开发人员在他们的网站上使用它。这个 jQuery 插件在 [jQuery 插件注册表](http://plugins.jquery.com/)上很流行，所以你可能已经听说过了。

现在我已经描述了这个插件的功能，让我们看看如何在你的网站中使用它。

## Stellar.js 入门

Stellar.js 的入门非常简单。第一步是下载插件并将其包含在您的页面中。你可以通过访问[它的 GitHub 库](https://github.com/markdalgleish/stellar.js/)或者通过 Bower 获得 Stellar.js。如果您想使用 Bower，您必须键入以下命令:

```
bower install jquery.stellar
```

一旦你下载了它，在你包含了 jQuery 之后，你必须使用通常的`script`元素**在你的页面中包含 Stellar.js，如下所示:**

```
<script src="path/to/jquery/jquery.min.js"></script>
<script src="path/to/jquery.stellar.min.js"></script>
```

完成这一步后，你就可以在页面中采用视差滚动效果了。这个插件允许你将效果应用到任何滚动元素，例如`window`对象或你可能有的任何其他元素。为此，您必须使用 jQuery 的构造函数选择元素，然后对其调用`stellar()`方法。

下面是一个在`window`对象上使用这个库的最小例子:

```
$('#someElement').stellar();
```

对于`window`对象，该库也提供了一个很好的简写，如下所示:

```
$.stellar();
```

此时，库将搜索指定元素中的任何视差背景或元素，并在元素滚动时重新定位它们。

如果你想要一个使用 Stellar.js 创建视差滚动效果的页面示例，你可以[看看这个](http://markdalgleish.com/projects/stellar.js/demos/backgrounds.html)。

## 选择

[Stellar.js](https://github.com/markdalgleish/stellar.js/) ，像其他许多插件一样，有一定程度的灵活性。事实上，它允许你设置几个参数，调整插件的工作方式来满足你的需求。Stellar.js 允许您定义通用选项，即用于每个元素的选项，以及每个元素的选项。您想要设置的一般配置必须在调用时传递给`stellar()`方法，而基于每个元素的选项是通过使用一些`data-*`属性来设置的。在这一节中，我不会涵盖所有可用的选项，所以如果你想进一步研究，你可以阅读[资源库](https://github.com/markdalgleish/stellar.js#configuring-everything)的这一特定部分。

您可以定义的第一个常规选项是决定您希望效果允许的方向。经典的滚动效果是从上到下，反之亦然，但是您也可以指定从左到右的效果，反之亦然，或者两者都指定。为此，您可以调整两个布尔属性`horizontalScrolling`和`verticalScrolling`。它们的默认值是`true`。

另一个有趣的选项是`responsive`。它用于指定当`load`和`resize`事件在`window`对象上触发时，是否必须刷新视差内容。其默认值为`false`。

我要讨论的最后一个通用选项是`hideDistantElements`。该属性的默认值是`true`，并指定您想要隐藏移动到视口之外的视差元素。如果你不想要这样的行为，你必须设置这个选项为`false`。

我想介绍的唯一一个基于元素的选项是`data-stellar-background-ratio`，因为它经常被使用。后者接受一个正数作为它的值，并允许您更改它所应用到的元素的效果速度。例如`data-stellar-background-ratio="0.5"`表示将速度改为自然滚动速度的一半。如果您想使用值小于 1 的属性，文档建议在元素的样式中设置`background-attachment: fixed;`。

既然您已经知道了这个插件是做什么的，以及如何配置它，那么是时候再次看看它的运行了。

## 演示

在这一节中，我们将开发一个使用 Stellar.js 插件和一些我们在前一节中讨论过的选项的演示。首先，我们需要设置标记。在下面的代码中，我们将创建六个包含一些文本的`div`:

```
<div class="content" id="content1">
    <p>TEXT HERE</p>
</div>
<div class="content" id="content2">
    <p>TEXT HERE</p>
</div>
<div class="content" id="content3" data-stellar-background-ratio="0.5">
    <p>TEXT HERE</p>
</div>
<div class="content" id="content4" data-stellar-background-ratio="0.5">
    <p>TEXT HERE</p>
</div>
<div class="content" id="content5" data-stellar-background-ratio="0.5">
    <p>TEXT HERE</p>
</div>
<div class="content" id="content6" data-stellar-background-ratio="0.5">
    <p>TEXT HERE</p>
</div>
```

有了这个 HTML，我们需要写一些 CSS 来定义使用的背景图片。在我们的演示中，我们将使用三张图片，每张图片重复两次。因为我们将在最后三个`div`上使用`data-stellar-background-ratio`属性，我们还将在 CSS 中添加`background-attachment: fixed;`。

最终的 CSS 代码如下所示:

```
body {
    font-size: 20px;
    color: white;
    text-shadow: 0 1px 0 black, 0 0 5px black;
}
p {
    padding: 0 0.5em;
    margin: 0;
}
.content {
    background-attachment: fixed;
    height: 400px;
}
#content1 {
    background-image: url("http://www.tamperlock.com/blog/wp-content/uploads/2014/08/london-england.jpg");
}
#content2 {
    background-image: url("http://ocdn.img/pulscms/ZjU7MDQsMCwzMiwzODQsMWZhOzA2LDMyMCwxYzI_/1eb29a70dabd0994cdefaad01ca3c884.jpg");
}
#content3 {
    background-image: url("http://www.zeus.aegee.org/magazine/wp-content/uploads/napoli-golfo-vesuvio.jpg");
}
#content4 {
    background-image: url("http://www.tamperlock.com/blog/wp-content/uploads/2014/08/london-england.jpg");
}
#content5 {
    background-image: url("http://ocdn.img/pulscms/ZjU7MDQsMCwzMiwzODQsMWZhOzA2LDMyMCwxYzI_/1eb29a70dabd0994cdefaad01ca3c884.jpg");
}
#content6 {
    background-image: url("http://www.zeus.aegee.org/magazine/wp-content/uploads/napoli-golfo-vesuvio.jpg");
}
```

最后，我们需要通过调用`stellar()`来启动效果。在本演示中，我们还将设置一些常规选项:

```
$.stellar({
    horizontalScrolling: false,
    responsive: true
});
```

前面代码的现场演示如下所示，也可以作为 JSFiddle 获得[。](https://jsfiddle.net/fb301gve/)

[https://jsfiddle.net/fb301gve/embedded/](https://jsfiddle.net/fb301gve/embedded/)

## 结论

在本文中，我向您介绍了 Stellar.js，这是一个 jQuery 插件，允许您在网站页面上创建视差滚动效果。我还没有介绍所有的选项和可用的效果，但是这篇教程应该会让你有足够的兴趣去了解更多。

你对 Stellar.js 有什么看法？你听说过或者用过吗？

## 分享这篇文章