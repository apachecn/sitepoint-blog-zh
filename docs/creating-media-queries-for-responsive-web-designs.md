# 为响应式网页设计创建媒体查询

> 原文：<https://www.sitepoint.com/creating-media-queries-for-responsive-web-designs/>

以下是我的新书的独家摘录，[快速响应网页设计，第二版](https://www.sitepoint.com/premium/books/responsive2/)。世界各地的商店都有出售，或者你可以在这里买到电子书。

响应式 web 设计的核心有两个主要的构件:媒体查询和视口。在本帖中，我们将看看在构建响应式网站时媒体的询问。

**媒体查询**代表了将许多其他响应概念和工具结合在一起的粘合剂。它们是一个简单但强大的概念，允许你检测设备属性，定义规则，并基于它们加载不同的 CSS 属性。例如，您可以为不同的设备优化导航菜单，将桌面浏览器上的全水平菜单转换为移动设备上常见的“汉堡包”菜单。

## 创建查询

让我们试一个更实际的例子。RWDflix 中主要内容的`aside`是用来显示公告和新闻的。移动设备和平板电脑的访问者可能想专注于观看节目，而不是别的，所以让我们为小屏幕设备的用户隐藏这个元素。

对于本章的剩余部分，我将使用前面显示的用于媒体查询的`@media`方法。这是个人喜好，作为一个实验，你可能想尝试用其他方法重写例子。

删除当前的`aside` CSS 类，并替换为以下媒体查询:

```
@media screen and (min-width: 680px) {
    aside {
        width: 33%;
    }
}

@media screen and (max-width: 680px) {
    aside {
        display: none;
    }
}
```

这对媒体查询如果屏幕宽于 680px(通过用`min-width`询问屏幕是否*至少* 680px 宽)，则将`aside`元素宽度设置为 33%，如果屏幕窄于 680px，则将其隐藏(用`max-width`询问屏幕是否*最多* 680px 宽)。

![media query example 1](img/9a9b6594c3bc6dfbe843e590521077f6.png)

接下来，当`aside`不显示时，让电视列表填满屏幕宽度:

```
section.showslisting {
    margin-bottom: 25px;
}

@media screen and (min-width: 680px) {
    section.showslisting {
        width: 66%;
    }
}

@media screen and (max-width: 680px) {
    section.showslisting {
        width: 100%;
    }
}
```

![media query example 2](img/70bf5fd6335fee62e17a720152393b17.png)

如果您在桌面浏览器中调整页面大小，您会看到`aside`随着您扩大和缩小浏览器窗口而出现和消失，主要内容也会相应调整。

您可以在媒体查询中使用基本逻辑。这允许您组合多个条件。例如，您可以使用逻辑 AND:

```
@media only screen and (min-width: 640px) and (max-width: 1136px) {
    …
}
```

如果设备的屏幕宽度在 640px *和* 1136px 之间，上面的代码将调用查询中的 CSS 样式。`min-width`属性是屏幕的最小宽度，`max-width`是最大宽度。

要匹配此媒体查询，两个条件都需要为真。如果只有一个条件为真，也可以用逻辑 OR 匹配查询，逻辑 OR 用逗号表示(令人困惑)。以下查询将应用于`screen`或`print`设备:

```
@media only screen, print {
    …
}
```

您还可以将 and 和 ORs 结合起来进行更复杂的查询。这是在较大尺寸的手机上呈现的当前页面，但在横向模式下:

![media query example 3](img/85709a321b80b484d6fc134cc127790d.png)

在上面的截图中，屏幕为 732px 宽，在上面设置的 640px 之上。尽管如此，`aside`仍然不太适合窗口，所以如果屏幕尺寸低于 640px，或者如果设备处于横向模式，我们就隐藏它:

```
@media screen and (max-width: 680px), screen and (orientation: landscape) and (max-width: 750px) {
    aside {
        display: none;
    }
}
```

以及列表部分的相应媒体查询:

```
@media screen and (max-width: 680px), screen and (orientation: landscape) and (max-width: 750px) {
    section.showslisting {
        width: 100%;
    }
}
```

你会看到我添加了一个`max-width`检查，以横向模式在平板电脑上显示`aside`，同时也展示了如何将逻辑检查结合在一起。

![media query example 4](img/227dd454aaf70702b2d2a505b9375c0c.png)

### 只有 and 没有的逻辑查询

您还可以使用`only`和`not`进行精确的媒体查询。例如，此媒体查询将匹配宽度至少为`640px`且*不包括*的设备。打印媒体类型:

```
@media not print and (min-width: 640px) {
    …
}
```

相比之下，该查询将*仅*应用于至少`640px`宽的屏幕设备:

```
@media only screen and (min-width: 640px) {
    …
}
```

### 查询功能

到目前为止，我们看到的`width`和`height`查询是一些使用最广泛的查询，但是还有其他几个**媒体特性**对于检查设备特性也很有用。这些特性包括基于 Mozilla 和 WebKit 的浏览器的颜色功能、纵横比、方向、分辨率和特定于供应商的特性。其中大多数接受前缀`min-`和`max-`，其工作方式与上面的维度查询类似。

#### `aspect-ratio`

`aspect-ratio`功能允许您检查水平像素与垂直像素的比例，用斜线分隔。例如:

```
@media screen and (min-aspect-ratio: 1/1) {
    …
}
```

上述规则将匹配屏幕宽高比为 1:1 或更高的设备，如方形或横向。

这通常用于检测更宽的屏幕，在显示视频时非常有用:

```
@media screen and (min-aspect-ratio: 16/9) {
    …
}
```

#### `orientation`

如前所述，该功能检查设备是处于横向模式还是纵向模式。例如:

```
@media all and (orientation: landscape) {
    …
}
```

并且:

```
@media all and (orientation: portrait) {
    …
}
```

#### `color`

此功能检查设备是否支持特定的颜色位级。例如，这是如何检查设备是否支持至少 8 位颜色(即 256 种颜色):

```
@media all and (min-color: 8) {
    …
}
```

#### `color-index`

这起到了类似于`color`的作用，但是让你检查颜色的数量而不是位级:

```
@media all and (min-color-index: 256) {
    …
}
```

#### `monochrome`

同样，类似于`color`，`monochrome`让您检查设备中的灰度等级:

```
@media all and (min-monochrome: 8) {
    …
}
```

#### `resolution`

此功能针对具有高分辨率屏幕的设备:

```
@media all and (min-resolution: 120dpi) {
    …
}
```

#### `scan`

`scan`媒体功能可让您检查电视的扫描过程，选项有`interlace`或`progressive`:

```
@media all and (scan: progressive) {
    …
}
```

#### `grid`

`grid`用于检查设备是否为类似终端的设备。这也包括老式手机(即非智能手机)、无障碍手机(针对视力不好的人)和盲文设备。如果为真，则返回一个布尔值:

```
@media grid {
    …
}
```

正如您所看到的，通过组合基于维度和基于特性的查询，您可以构建复杂的样式表，以最有效的方式针对大量设备。这是媒体查询的要点:它们允许你有选择地为特定的设备配置加载 CSS 样式。

## 简化示例应用程序

现在您已经有了一个基本的理解，让我们继续构建之前添加的媒体查询，使示例站点更加整洁。

首先，您可以合并已经编写的查询，将类移动到两组媒体查询中:

```
/* Media Queries */
/* For example, desktop devices */
@media screen and (min-width: 680px) {
    aside {
        width: 33%;
    }

    section.showslisting {
        width: 66%;
    }
}

/* For example, medium-width screens or smaller screens in landscape */
@media screen and (max-width: 680px), screen and (orientation: landscape) and (max-width: 750px) {
    aside {
        display: none;
    }

    section.showslisting {
        width: 100%;
    }
}
```

整洁多了！您可以在这些部分中为每个查询添加新的样式。

让我们回头看看第一章中概述的演示站点规范，看看每个屏幕尺寸还需要优化什么。下一步将是在小屏幕上隐藏电视节目描述，并在中等大小的屏幕上缩短它。

如果描述文本有一个类，那么做起来会容易得多，所以给包含显示描述的每个`p`标记实例添加一个类:

```
<section class="tvshow">
    <h3>Show</h3>
    <img src="http://placehold.it/350x150" 
    class="thumbnail">
    <p class="showdescription">Lorem ipsum dolor sit amet, 
    consectetur adipiscing elit.</p>
</section>
```

让我们添加新的媒体查询，以适应我们要支持的各种大小，并在这样做的同时，整理现有的媒体查询。

我们希望设计回流的具体宽度称为**断点**。我们将添加的断点并不详尽:设备大小的范围很大，并且一直在出现更多的断点，所以试图用单独的查询来定位每个设备是徒劳的。更好的方法是评估设计需要回流的点，然后为这些断点创建媒体查询。这就是响应式网页设计的美妙之处:你可以确保每个设备都得到一个合适的布局，而不必为每个设备分别设计。

添加以下断点，并在其中重构我们现有的样式:

```
/* Media Queries */

/* For example, older phones */
@media only screen and (min-width: 320px) {
    aside {
        display: none;
    }

    section.showslisting {
        width: 100%;
    }

    .showdescription {
        display: none;
    }
}

/* For example, newer phones */
@media only screen and (min-width: 480px) {
}

/* For example, small computer screens and larger tablets */
@media only screen and (min-width: 768px) {
    .showdescription {
        text-overflow: ellipsis;
        display: block;
        white-space: nowrap;
        width: 100px;
        overflow: hidden;
    }
}

/* For example, typical desktop monitors or larger tablet devices */
@media only screen and (min-width: 992px) {
    aside {
        width: 33%;
        display: block;
    }

    section.showslisting {
        width: 66%;
    }

    .showdescription {
        white-space: normal;
        width: 125px;
    }
}

/* Large Devices, for example large monitors and TVs */
@media only screen and (min-width: 1200px) {
}
```

媒体的询问现在应该不言自明了。它们为使用屏幕尺寸定义的设备选择定义查询。正如 CSS 中的 C 所暗示的，媒体查询**级联**:您可以在断点中定义的样式基础上构建更小的设备，并更改不同的属性。

注意，在小型设备的`@media only screen and (min-width: 320px)`断点和中型设备的`@media only screen and (min-width: 768px)`断点之间，所有改变的是`showdescription`类，显示文本，但是截断它。然后在大屏幕的`@media only screen and (min-width: 992px)`断点中，所有元素都被显示出来。

小屏幕断点生成的布局:

![](img/5758d8498e9a4cc2201b74bba5d907a0.png)

由中间断点生成的布局:

![](img/63b3cdba82f3a26b696b3b780ca5c25a.png)

由中到大断点生成的布局:

![](img/47964b7bf182a11502798b4dc788217a.png)

我最近出版的书[Jump Start Responsive Web Design](https://www.sitepoint.com/premium/books/jump-start-responsive-web-design-2nd-edition/)，现在可以在 SitePoint 上买到。它的目的是让你开始理解和使用 CSS 和 HTML 工具套件，以应对这个新的设备世界。它非常实用，通篇有大量的例子，涵盖:

[![Jump Start Responsive Web Design book](img/8673bc45cc9ed5e0710407f81d6e3073.png)](https://www.sitepoint.com/premium/books/jump-start-responsive-web-design-2nd-edition)

*   *响应式网页设计的意义和目的*
*   *响应式设计的构建模块*
*   *具有电网系统的更好响应结构*
*   *响应文本*
*   *响应图像和视频*
*   *响应用户上下文*

我的书适合对 HTML 和 CSS 有所了解的初级设计者和开发者。一些 JavaScript 经验对后面的章节很有用。

## 分享这篇文章