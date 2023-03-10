# 用 OriDomi 创造折纸效果

> 原文：<https://www.sitepoint.com/creating-folded-paper-effect-oridomi/>

许多使用 Bootstrap 等知名框架构建的网站彼此非常相似。我不反对使用它们。相反，我很高兴由于这些框架，很多人能够创建他们自己的网站，即使只有很少的知识。尽管如此，每个网站都需要在某些方面有所不同才能被识别。与众不同并不意味着你必须创造不可思议的布局或惊人的动画，它也可以是一个小效果的添加。

在这篇文章中，我将向你展示如何使用 OriDomi 库创建折叠效果。这种效果可能就是你的网站所需要的那种格调。

## OriDomi 是什么？

OriDomi (一个有趣的词，将“Origami”和“DOM”结合在一起)是一个库，用于<q cite="https://github.com/dmotz/oriDomi">像纸一样折叠 DOM 元素</q>。我发现它的效果很简单，但非常有效，我认为它非常适合餐馆和酒吧的网站。OriDomi 没有任何依赖性，所以你不需要在你的网页上填充很多其他的库来使用它。然而，这个库也可以用作 jQuery 插件。它在 [jQuery 插件注册表](http://plugins.jquery.com/tag/jquery/)上相当流行，所以你可能已经听说过它。

为了让你了解它的功能，这里有一个简单的例子:

[https://jsfiddle.net/jwqecvt3/embedded/](https://jsfiddle.net/jwqecvt3/embedded/)

好奇吗？你应该害怕！这只是你可以在页面上创造的效果之一。让我们发现更多。

## OriDomi 入门

OriDomi 的入门非常简单。您需要执行的第一步是下载库并将其包含在您的页面中。你可以通过访问[它的 GitHub 库](https://github.com/dmotz/oriDomi)或者通过著名的网络依赖管理器[鲍尔](http://bower.io/)来获得 OriDomi，输入命令:

bower install oridomi
[/shell]

一旦你下载了这个库，你必须使用通常的`<script>`标签将它包含在你的页面中，如下所示:

```
<script src="path/to/oridomi/oridomi.min.js"></script>
```

此时，您已经准备好在任何元素上使用它了。元素必须传递给方法`OriDomi()`，该方法必须作为构造函数调用(使用`new`关键字)。下面显示了一个使用该库的最小示例:

```
new OriDomi(document.getElementById('element'));
```

或者，您可以向该方法传递一个选择器，就像我们在本文的第一个演示中看到的那样。在这种情况下，效果将应用于第一个匹配的元素:

```
new OriDomi('.my-class');
```

值得注意的是，不管元素的类型如何，也不管它是否有背景，效果都是有效的，但当使用图像或有背景图像的元素时，库的效果最好。

下面的[示例](https://jsfiddle.net/kc6rujxh/embedded/result/)显示了没有背景图像的`div`元素的效果:

[https://jsfiddle.net/kc6rujxh/embedded/](https://jsfiddle.net/kc6rujxh/embedded/)

请注意元素是如何应用效果的，但它看起来不太好。

现在我们已经看到了 OriDomi 的基础，让我们讨论一下如何调整默认设置。

## 选择

创建新的 OriDomi 组合时，可以传递一个对象文字来指定要作为第二个参数应用的选项。OriDomi 提供了几个选项来配置效果。

当从左边或右边折叠一个元素时，库将该元素分成五个面板。类似地，当从顶部或底部折叠一个元素时，默认情况下 OriDomi 将该元素分为三个面板。我们可以通过分别为`vPanels`和`hPanels`选项传递不同的值来改变这些默认值。

我们可以配置的另一个选项是`speed`,它表示动画执行的速度。默认值为 700 毫秒。

注意:在撰写本文时，文档声称默认速度为 1200 毫秒。然而，看一看代码，您会发现实际速度为 700 毫秒。

如果您想禁用用户与元素交互的可能性，例如，如果您只想以编程方式应用效果，您可以将`touchEnabled`属性设置为`true`。在交互方面，OriDomi 提供了一些钩子来执行给定动作后的回调。例如，您可以在交互开始或完成后运行特定的功能。可以用来运行回调的选项有`touchStartCallback`、`touchMoveCallback`和`touchEndCallback`。

下面的[示例](https://jsfiddle.net/tgrd2x2h/embedded/result/)向您展示了改变`vPanels`、`speed`默认值的效果，以及当用户通过`touchEndCallback`附加一个回调来结束对元素的操作时，如何登录控制台。

[https://jsfiddle.net/tgrd2x2h/embedded/](https://jsfiddle.net/tgrd2x2h/embedded/)

现在让我们看看 OriDomi 还有什么其他的作用。

## 效果

如我所说，OriDomi 通过一系列方法暴露了很多效果。这些方法中的大多数接受折叠角作为它们的第一个参数。我们已经看到了`accordion()`方法的默认效果。另一种方法是`twist()`。使用它，您可以扭曲 DOM 元素本身。下面的[演示](https://jsfiddle.net/96288mwc/embedded/result/)展示了使用`twist()`所达到的效果:

[https://jsfiddle.net/96288mwc/embedded/](https://jsfiddle.net/96288mwc/embedded/)

如果您想完全卷起(读取 hide)元素，`foldUp()`是要使用的方法。相反，如果你想再次展示，你可以调用`unfold()`。使用这两种方法的[演示](https://jsfiddle.net/rh7v0pqp/embedded/result/)如下所示:

[https://jsfiddle.net/rh7v0pqp/embedded/](https://jsfiddle.net/rh7v0pqp/embedded/)

我想提到的最后两个方法是`curl()`和`fracture()`，但是还有更多方法可以尝试。

## 结论

在本文中，我向您介绍了 OriDomi，这是一个允许您在网页元素上创建漂亮的折叠效果的库。我还没有介绍所有的选项和可用的效果，但是这篇教程应该会让你有足够的兴趣去了解更多。此外，该库有非常好的文档，您可以阅读以了解更多关于如何对效果和其他高级用途进行排队的信息。

你对 OriDomi 有什么看法？你听说过或者用过吗？

## 分享这篇文章