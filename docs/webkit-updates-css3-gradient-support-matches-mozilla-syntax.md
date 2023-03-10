# WebKit 更新 CSS3 渐变支持，匹配 Mozilla 语法

> 原文：<https://www.sitepoint.com/webkit-updates-css3-gradient-support-matches-mozilla-syntax/>

周五，WebKit 团队[宣布](http://webkit.org/blog/1424/css3-gradients/)他们正在更新渲染引擎中的 CSS3 渐变支持，使其符合 Mozilla 的实现。除了旧的 webkit-gradient 值之外，每夜的 WebKit 构建现在包括对`-webkit-linear-gradient`和`-webkit-radial-gradient`的支持。这些新值使用与`-moz-linear-gradient`和`-moz-radial-gradient`相同的语法，因此开发人员现在可以使用相同的一组值并简单地复制它们，而不是像以前要求的那样编写两种语法。过去，大多数开发人员依靠使用渐变代码生成器，因为旧的 WebKit 语法很难记住。有了这个新的更简单和一致的语法，在工作网站中使用渐变会变得容易得多。

该语法也包含在 CSS3 图像值的最新[编辑草案中，并替换了内容模块](http://dev.w3.org/csswg/css3-images/#gradients)。

幸运的是，对于已经开始使用旧的`-webkit-gradient`的开发者来说，现有的网站不会崩溃。这是因为新属性的命名不同，现有的`-webkit-gradient`实现不会被移除。

作为新支持的演示，公告博客帖子包括了许多示例渐变。查看一下源代码，您会发现在`-moz-linear-gradient`和`-webkit-linear-gradient`值中的值是相同的。这同样适用于径向渐变，以及线性和径向渐变的重复版本。

对于那些不熟悉语法的人，让我们快速入门。指定简单的线性梯度很简单:

```
 background-image: -webkit-linear-gradient(black, white);
background-image: -moz-linear-gradient(black, white); 
```

如果您希望渐变沿着不同的轴，您可以使用关键字`left`、`right`、`top`、`bottom`、`bottom left`、`bottom right`等指定原点，或者指定渐变将运行的方向的度数。角度值位于颜色停止列表之前，度数从右侧开始顺时针计算。因此，对于从框的右上角开始并在左下角结束的渐变，您可以编写以下内容之一:

```
background-image: -webkit-linear-gradient(top right, black, white);
```

```
background-image: -webkit-linear-gradient(135deg, black, white);
```

您还可以定义色标来调整每种颜色的开始位置，并且可以指定任意多种不同的颜色。

径向渐变的语法非常相似:

```
background-image: -webkit-radial-gradient(black, white);
```

如果您想要定位渐变的中心，只需指定 X 和 Y 值，就像您对背景位置所做的一样:

```
backround-image: -webkit-radial-gradient(10px 10px, black, white);
```

你也可以调整径向渐变的形状和大小，更多细节请看[规范](http://dev.w3.org/csswg/css3-images/#gradients)或[公告](http://webkit.org/blog/1424/css3-gradients/)。

一致的实现和一个草案规范是 CSS3 渐变向我们可以在日常项目中使用的东西迈进的一大步。如果您过去因为它们的复杂性而回避它们，WebKit 的这次更新会让您重新考虑使用它们吗？

**note:**Want more?

如果你想阅读更多关于 Louis 的文章，请订阅我们每周的科技极客时事通讯， *[《科技时报》](https://www.sitepoint.com/newsletter/)* 。

## 分享这篇文章