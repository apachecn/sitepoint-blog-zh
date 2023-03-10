# HTML 中被替换的元素:神话与现实

> 原文：<https://www.sitepoint.com/replaced-elements-html-myths-realities/>

有些 HTML 元素总是让前端开发人员头疼，比如 iframes、applets、嵌入对象或表单控件。人们永远无法完全控制它们的显示，因为从一个浏览器到另一个浏览器，从一个操作系统到另一个操作系统，它们看起来都不一样。

![Replaced Element Myths](img/6617cedb48fc51615a88d50220dcff1e.png)

无数的库和框架创造了替代品，从 jQueryUI 到 Bootstrap 等等。但是你有没有想过为什么这些元素会有这样的表现？一些声音(包括一些知名网站)将矛头指向 W3C 的一个相当模糊的定义，即关于**替换元素**的定义。但是他们 100%正确吗？这是我们今天将共同追求的目标。

## 什么是替换元素？

任何好的探索都必须从研究开始。因此，让我们来看一下官方规格中所说的替换元素是什么:

> 其内容超出 CSS 格式模型范围的元素，如图像、嵌入文档或 applet。例如，HTML IMG 元素的内容通常由它的“src”属性指定的图像替换。被替换的元素通常具有固有尺寸:固有宽度、固有高度和固有比率。例如，位图图像具有以绝对单位指定的固有宽度和固有高度(由此显然可以确定固有比率)。另一方面，其他文档可能没有任何内在维度(例如，一个空白的 HTML 文档)。

*来源: [3。一致性:要求和建议>3.1-定义](https://drafts.csswg.org/css2/conform.html)T3*

到目前为止，一切顺利——我们得到了一个被替换元素的一般描述。在我们深入本规范部分之前，让我们绕个小弯，澄清一下“固有尺寸”部分。

### 什么是内在维度？

现有的内在维度的最佳定义来自于 [CSS 图像值和替换的内容模块级别 3](https://www.w3.org/TR/css3-images/) 文档:

> 术语“固有尺寸”指的是一组**固有高度**、**固有宽度**和**固有纵横比**(宽度和高度之间的比率)，对于给定的对象，它们中的每一个可能存在，也可能不存在。这些固有尺寸代表物体本身的优选或自然尺寸；也就是说，它们不是使用对象的上下文的函数。CSS 通常没有定义如何找到内部维度。

以下是一些例子:

*   正常的图像有三个维度:宽度、高度和长宽比。
*   SVG 图像只能有纵横比。它的缩放特性意味着宽度和高度不限于单一值。
*   通过`iframe`元素插入的空 HTML 页面根本没有维度。

这些性质相互联系的方式意味着不可能有只有二维的物体。知道其中两个就自动确定了第三个。

总的来说，这意味着这样一个对象，当被放入页面时，将为文档提供这些内在的维度，以便它们可以被正确地呈现。这些信息以后会有用，但是现在，让我们回到主路径。

## 替换了现实世界中的元素

要获得被替换元素的完整描述，我们需要访问不同的资源，即 HTML 生活标准文档的[渲染](https://html.spec.whatwg.org/multipage/rendering.html)部分。随着对规范的深入研究，很容易看出这个话题是如何令人困惑的。这是因为一些 HTML 元素总是充当被替换的元素，而另一些元素只在特定的情况下才这样做。

幸运的是,[第 14.4](https://html.spec.whatwg.org/multipage/rendering.html#replaced-elements) 小节为我们提供了正确理解每个案例所需的所有信息。

### 嵌入内容

第一类被替换的元素是嵌入内容。这包括将另一个资源导入到文档中的任何元素，或者插入到文档中的另一个词汇表中的内容。这些外部资源，就其本身的性质而言，具有与定义的要求相匹配的内在维度。

这个类别中的主要元素是`embed`、`iframe`和`video`。这些元素总是被视为替换元素，因为它们总是将外部内容导入到您的文档中。

对于只有在特殊情况下才属于这一类别的元素，情况会稍微复杂一些:

*   `applet`–当它代表一个插件时被视为一个替换元素，否则被视为一个普通元素。
*   `audio`–仅当“[显示用户界面元素](https://html.spec.whatwg.org/multipage/media.html#expose-a-user-interface-to-the-user)时，视为替换元素。将呈现大约一行高，宽度为暴露用户代理的用户界面功能所必需的宽度。
*   `object`–当它表示图像、插件或嵌套浏览上下文时，被视为替换元素(类似于`iframe`)。
*   `canvas`–当代表嵌入内容时，被视为替换元素。也就是说，它包含元素的位图(如果有的话)，或者是与元素具有相同内在尺寸的透明黑色位图。

只有在这些情况下，上述元素才会被视为替换元素(这几乎涵盖了使用这些元素的所有情况)。有关这方面的详细信息，请参见 WHATWG 的嵌入式内容渲染规则。

### 形象

接下来是图片。在绝大多数情况下(当图像正确显示时)，`img`元素被视为具有图像固有尺寸的替换元素。这个类别还包括具有`type="image"`属性的`input`元素。

由于各种原因，当图像没有呈现在页面上时，事情变得有点复杂。浏览器期望它最终呈现的丢失的图像必须被视为替换的元素，其内容是该元素表示的文本(可选地，还可以添加图标)。`<input type="image">`将显示为普通按钮。可以在规格中查看整套规则和条件[。](https://html.spec.whatwg.org/multipage/rendering.html#images-3)

### 被替换元素的默认大小

这就是我们收集的关于内在维度的信息变得有用的地方。我们已经确定，被替换元素的`width`和`height`属性来自它们带入页面的内容的内在维度。如果这些尺寸无法计算(就像在`iframe`加载空白 HTML 页面的情况下)，浏览器必须退回到默认规则，如[可视格式模型细节](https://www.w3.org/TR/CSS21/visudet.html)中所指定的。这套规则本身相当庞大和复杂，但是我们将只关注那些“其他都不起作用”的规则:

> 否则，如果“宽度”的计算值为“自动”，但不满足上述任何条件，则“宽度”的使用值将变为 300 像素。如果 300px 太宽而不适合设备，UAs 应该使用比例为 2:1 并适合设备的最大矩形的宽度。

*来源: [10.3 计算宽度和页边距](https://www.w3.org/TR/CSS21/visudet.html#Computing_widths_and_margins)*

> 否则，如果“height”的计算值为“auto”，但不满足上述任何条件，则“height”的使用值必须设置为最大矩形的高度，该矩形的比例为 2:1，高度不大于 150px，宽度不大于设备宽度。

*来源: [10.6 计算高度和边距](https://www.w3.org/TR/CSS21/visudet.html#Computing_heights_and_margins)*

我们可以将所有这些总结为三个基本规则:

*   如果对象有明确的`width`、`height`和`ratio`值，则使用它们；
*   如果对象只有`ratio`，在保持上述比例的情况下，宽度和高度都使用`auto`；
*   如果这些维度都不可用:
    *   当视口大于 300 像素时，使用`width: 300px; height: 150px`
    *   当视窗小于 300 像素时，宽度和高度使用“自动”,比例为 2:1；

这里有一个说明上述情况的例子。我们有一个位图图像、一个 SVG 图像和一个 iframe。如您所见，图像以正常大小显示，SVG 保持纵横比，但占用所有可用空间，而 iframe 默认最大值为 300×150。如果我们缩小视口，iframe 始终保持 2:1 的纵横比。

在 [CodePen](https://codepen.io) 上看到笔[用 SitePoint (](https://codepen.io/SitePoint/pen/ZJWKGr/) [@SitePoint](https://codepen.io/SitePoint) )替换了元素固有尺寸。