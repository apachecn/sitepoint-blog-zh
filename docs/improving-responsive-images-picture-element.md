# 用像素改善响应图像

> 原文：<https://www.sitepoint.com/improving-responsive-images-picture-element/>

不久前，我写了一篇关于 [srcset 属性](https://www.sitepoint.com/responsive-images-part-1-using-srcset/)的文章。这是`img`元素的一个属性，旨在为特定设备提供正确的图像。`srcset`属性允许开发人员指定一个图像源列表，根据用户屏幕的像素密度或大小从中选择一个源并显示给用户。

浏览器提供了一组关于某些类型图像的正确行为的“建议”,从而缩短了页面的加载时间。这种技术降低了小屏幕设备(我们倾向于认为是连接缓慢的设备)的网站权重，从而改善了用户体验。在写那篇文章的时候，这个属性是一个独立的提议，但是后来被合并到一个更大的提议中，即 [`picture`元素](https://html.spec.whatwg.org/multipage/embedded-content.html#the-picture-element)。

在本教程中，我将概述`picture`元素，描述它的主要特性和优点。

## 图片元素是什么？

多年来，为给定设备提供正确的图像(在尺寸和像素密度方面)的主题已经得到了广泛的讨论。最近的文章有[“响应图像:如果你只是改变分辨率，使用 srcset。”](http://css-tricks.com/responsive-images-youre-just-changing-resolutions-use-srcset/)克里斯·科伊尔(Chris Coyier)和[“不要用<图>(大部分时间)</图>](http://blog.cloudfour.com/dont-use-picture-most-of-the-time/)杰森·格雷斯比(Jason Grigsby)。

关于`picture`元素要理解的第一件事是，它本身并不显示任何东西。它只是为插入其中的图像提供一个上下文，这使得浏览器能够从多个源 URL 中进行选择。这个元素不同于其他类似的容器元素，比如众所周知的`video`和`audio`。当`source`元素嵌套在`picture`中并且资源选择算法不同时，`src`属性没有意义。

这可以总结如下。对于`video`和`audio`元素，如果指定了`src`属性，它对`source`元素有绝对的优先权，不考虑。但是在`picture`元素的情况下，因为我们已经看到它只不过是一个容器，所以`source`元素是在任何情况下都要考虑的元素。

如果我们回到 W3C 规范中可用的定义[，我们会发现图片元素是一个容器，它为其包含的`img`元素提供了多个来源。这允许作者基于屏幕像素密度、视口大小、图像格式和其他因素，声明性地控制或提示用户代理使用哪个图像资源。它代表它的孩子。在这个元素中，我们可以放置两个不同的标签，`<img />`，用于为不支持它的浏览器提供向后兼容性，以及`<source />`。](https://html.spec.whatwg.org/multipage/embedded-content.html#the-picture-element)

`source`元素拥有四个属性:

*   使用这个属性，我们可以指定想要显示的图像的 URL。这接受由逗号分隔的多个 URL。与`srcset`属性一样，我们可以将每个 URL 与屏幕分辨率或宽度规格(用空格与 URL 隔开)配对。
*   `media`:这里我们写了一个媒体查询，如果被评估为真，将给出关于在`srcset`属性中指定显示哪个图像的建议。
*   `sizes`:这是我们为`srcset`属性中描述的源指定一组固有大小的属性。它还接受由逗号分隔的多个大小。
*   `type`:给出源集中图像的类型，如果不支持给定类型，允许用户代理跳到下一个`source`元素。

元素可以以多种方式使用。让我们看一下您可以给这个元素的“方向”的快速列表:

**基于艺术方向的选择**:这是`picture`元素在响应式设计中最常见的用法。不是基于视口宽度放大或缩小一个图像，而是有多个图像可以更合适地填充浏览器视口。这很好，因为正如 W3C 规范所建议的。“使用经过裁剪以适应特定屏幕功能的不同图像有助于有效传达信息。”

**基于设备像素比率的选择**:作为设计师，你应该致力于以减少可感知裁剪的方式显示图像。具有不同屏幕密度的设备需要不同最低分辨率的图像。因此，像素密度越高，图像需要的像素就越多。

**基于视口的选择**:作者可能希望显示相同的图像内容，但根据视口的宽度，呈现不同的尺寸。这通常称为“基于视口的选择”。响应式布局中的图像尺寸往往会根据视口的大小而变化。将大尺寸的图像发送到具有窄视窗的浏览器中是很常见的，然后浏览器会调整这些图像的大小以适应设计。理想情况下，您应该提供与用户的视口尺寸相匹配的图像，而不需要向用户发送过多的数据。

**基于图像格式的选择**:作者可能希望显示相同的图像内容，但使用不同的图像格式，这取决于用户代理支持的图像格式。这通常被称为“基于图像格式的选择”。设计师应该总是试图在多个分辨率下提供相同的图像。通过这种方式，高分辨率设备将获得给定分辨率的最佳图像，而低分辨率设备将避免浪费时间和带宽下载过大(无用)的文件。

## 使用图片:实际例子

现在我们已经发现了这个元素的用例，是时候展示一个例子了。想象一下，我们正在用移动优先的方法开发一个网站。在这个假设的网站中，我们将使用我在[上一篇文章](https://www.sitepoint.com/responsive-images-part-1-using-srcset/)中使用过的相同图片`Autumn in Moscow`。在下面的代码中，我们将默认显示图像`autumn-in-moscow-mobile.png`,然后显示如果用户使用平板电脑或从桌面访问网站时我们想要呈现的图像。因此，`autumn-in-moscow-tablet.png`将用于屏幕至少为 680 像素的设备，而`autumn-in-moscow-desktop.png`用于屏幕宽度至少为 1024 像素的设备。因为我们可以预期会有一些浏览器不能识别`picture`元素，所以我们也将提供一个后备图像。使用的回退图像是`autumn-in-moscow.png`。

实现上述内容的代码是:

```
<picture>
   <source media="(min-width: 1024px)" srcset="autumn-in-moscow-desktop.png">
   <source media="(min-width: 680px)" srcset="autumn-in-moscow-tablet.png">
   <source srcset="autumn-in-moscow-mobile.png">
   <img src="autumn-in-moscow.png" alt="Autumn in Moscow">
</picture>
```

到目前为止，一切顺利。但是，如果我们想通过关注`srcset`属性的能力来提高代码片段的特异性，该怎么办呢？考虑到屏幕的分辨率，考虑强调图像的差异。下面的代码将告诉您需要添加什么:

```
<picture>
   <source media="(min-width: 1024px)" srcset="autumn-in-moscow-desktop.png, autumn-in-moscow-desktop-hd.png 2x">
   <source media="(min-width: 480px)" srcset="autumn-in-moscow-tablet.png, autumn-in-moscow-tablet-hd.png 2x">
   <source srcset="autumn-in-moscow-mobile.png, autumn-in-moscow-mobile-hd.png 2x">
   <img src="autumn-in-moscow.png" alt="Autumn in Moscow">
</picture>
```

正如我们在上一节中讨论的那样，`srcset`属性接受多个用逗号分隔的 URL。此外，我们可以将每个 URL 与屏幕分辨率或宽度规格相匹配。在这种情况下，第二个 URL 与字符串 2x 成对出现，由空格分隔，目标用户使用高分辨率显示(像素密度 2x，像视网膜一样)。

浏览器承担的第一个请求是基于指定的媒体查询(如果存在)最适合用户屏幕的`source`元素。第二个要考虑的因素是屏幕分辨率，从`srcset`属性中选择最合适的图像。

为了完成对组成`picture`元素的元素的概述，让我们看看如何使用`sizes`属性。假设我们希望我们的图像覆盖 50%的宽度，而不管它的实际大小和像素密度。为了实现这个目标，我们必须在`srcset`属性中指定我们想要覆盖的尺寸和每个图像的尺寸，如下所示:

```
<picture>
   <source sizes="50vw" srcset="autumn-in-moscow-mobile.png 480w, autumn-in-moscow-small-tablet.png 768w, autumn-in-moscow-tablet.png 968w, autumn-in-moscow-desktop.png 1024w">
   <img src="autumn-in-moscow.png" alt="Autumn in Moscow">
</picture>
```

## 和睦相处

目前只有 Chrome 38+和 Opera 25+默认支持`picture`元素(意思是不需要在你的浏览器中激活任何标志)。从版本 34 开始，Firefox 在标志后面支持这个元素。从 38 版本开始，Firefox 也会默认支持`picture`。Internet Explorer 仍在考虑该元素。幸运的是，Scott Jehl 为这个提议创建了一个 polyfill，叫做 [picturefill](https://github.com/scottjehl/picturefill) ，更新到了最新的规范。你可以下载它，并在这里找到你可能需要的所有信息。

## 结论

在本文中，我们深入研究了作为解决响应图像问题的解决方案的`picture`元素的规范。它允许开发人员解决为多种设备设置正确图像分辨率的问题。正如我们所看到的，这是经过多年讨论后的最新解决方案，它是由过去几年所有其他提议中的最佳提议组成的。我推荐你阅读[规范](https://html.spec.whatwg.org/multipage/embedded-content.html#the-picture-element)来了解更多这个元素的巨大潜力和用例。

## 分享这篇文章