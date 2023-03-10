# 在网页上实现良好的易读性和可读性

> 原文：<https://www.sitepoint.com/achieving-good-legibility-and-readability-on-the-web/>

从我的[上一篇文章](https://www.sitepoint.com/setting-type-on-the-web-an-introduction/ "Setting Type on the Web: An Introduction")继续，这篇文章将通过探索影响易读性和可读性的基本排版因素来更仔细地检查易读性和可读性。

印刷术在印刷世界获得了它的突出地位。因此，良好的易读性和可读性的基础是很好理解的，但在数字媒体上，我们需要考虑一些额外的因素。

## 字体

如前所述，字体对它们设置的文本有很大的影响。选择一个好的和适用的字体，尊重副本，并迎合其要求(例如，如果你知道你将设置数学符号，确保字体有字形)是至关重要的。

很有可能，您正在设置更大的文本块。你会想要选择一个好的文本字体——一个为设置较长的文本块而设计的字体。测试字体作为文本字体的最佳方式是在基本罗马体中设置一段 [Lorem Ipsum](http://lipsum.lipsum.com/ "Lorem Ipsum: a placeholder text") ，大小为 12px 到 14px，行距为 1 比 1.5(见下面的*行距*)，然后看看它读起来如何。文本字体可以是衬线字体(如乔治亚字体)或 sanserif 字体(如 Arial 字体)。

字体在 <acronym title="Cascading Style Sheets">CSS</acronym> 中用`font-family`属性声明，并接受描述性值——一个通用系列或特定字体系列。例如，这里有一个[过渡衬线](https://secure.wikimedia.org/wikipedia/en/wiki/VOX-ATypI_classification#Transitional "Wikipedia (EN): VOX-ATypI classification: Transitional")字体堆栈:

```
p {
    font-family:
        Baskerville,
        Times
        'Times New Roman'
        serif;
    }
```

## 胶料

设置字体时，选择一个合适的大小:14 像素或更大是大多数屏幕文本字体的经验法则。我们中没有多少人有 20-20 的视力，所以最好把你的文本显示得大一点，而不是太小。

**注意:** JavaScript 支持的文本大小调整小部件≠可访问性。

不要随意调整文本大小；试着坚持一个尺度:

[![The classical scale.](img/463a82444e38e61376f1fac777603431.png)](https://www.sitepoint.com/wp-content/uploads/2011/02/classical-scale_4.png) 
*【古典音阶】。*

[![Another scale.](img/a1b0478abf5fe1df888db51eaceb65c0.png)](https://www.sitepoint.com/wp-content/uploads/2011/02/another-scale.png) 
*另一种尺度。*

[![A scale based on the Fibonacci sequence.](img/8a516dad51fc99a331388599bcf33974.png)](https://www.sitepoint.com/wp-content/uploads/2011/02/fibonacci-scale_1.png) 
*基于斐波那契数列的一种标度。*

类型最好是相对大小，使用 ems。em 是在水平方向上相当于字体大小的距离，以磅为单位(例如，12pt 字体的 1em 是 12pt；16pt 类型的 1em 为 16pt)。我们使用 [`font-size`属性](https://reference.sitepoint.com/css/font-size "SitePoint.com CSS Reference Guide: font-size property")在 <acronym title="Cascading Style Sheets">CSS</acronym> 中设置字体大小:

```
p { font-size: 1.2em; }
```

请记住，字体大小在 <acronym title="Document Object Model">DOM</acronym> 中是由子元素从其父元素继承而来的。这使得嵌套元素的 em 大小计算变得困难。一个好主意是先以像素为单位确定所有东西的大小，然后将测量值转换为 ems。像素很容易使用，但作为一个固定的单位来说是不够的——特别是当你扩展一个网站时(见下面的*度量*)。

要在 ems 中计算所需的值，请在 ems 中找到 1 个像素的值，然后乘以所需的字体大小(以像素为单位):

1 ÷父字体大小×所需像素值= em 值

例如，如果父字体大小(比如由 body 元素定义)是 16 像素，但是我们想要将一个段落(body 元素的子元素)的大小调整为 12 像素，我们计算:1 ÷ 16 × 12，这样我们得到 0.75em。

### 62.5%的诡计

有一个简单的方法可以简化这些计算。考虑以下 CSS:

```
p { font-size: 80%; }
blockquote { font-size: 80%; }
```

样式化这个标记:

这是一小段，后面是一段引文:

> 块引用是引用材料的块，可以包含
> 范围的内容，包括段落、列表，当然甚至还有
> 标题。

[/XML]
16px 的 80%是 12.8px，所以`p`和`blockquote`元素会是那个大小，但是当我们把一个`p`元素放在一个`blockquote`元素里面会发生什么呢？父级(`blockquote`)是 12.8 像素，所以`p`将以 80%的像素渲染:10.42 像素

啊！这可能会造成相当大的混乱。Richard Rutter 开发了一个简洁的技巧来简化嵌套元素的大小计算。考虑:

*   浏览器通常默认的文本大小是 16px。
*   将`body`的字体大小设置为 62.5%，将所有内容重置为 10px。

从这一点来说，对于身体的*直接后代*的计算是相似的，例如，12px = 1.2em8px = 0.8em 诸如此类。当然，嵌套更深的元素(仍然)是相对的。

## 措施

度量标准是线路长度。保持合适的长度很重要:不要太长，也不要太短。

当小节太长时，眼睛很难看到下一行。维基百科是一个在这方面可以做得更好的网站，这是一个宏大但几乎臭名昭著的例子，它的衡量标准是相对于浏览器窗口的长度；在宽屏显示器上将窗口扩展到全屏幕，您会发现，原来每行 40 个字符的地方突然变成了 100 个或更多的字符。

相反，确保行不要太短，以至于眼睛不得不每隔几个字就掉一行。有一些出版物风格流行短小精悍，例如期刊，但是在其他地方如此短小精悍的副本开始看起来廉价，就好像一旦被阅读，就可以像报纸一样被扔掉。

用 [`width`属性](https://reference.sitepoint.com/css/width "SitePoint.com CSS Reference Guide: width property")在 <acronym title="Cascading Style Sheets">CSS</acronym> 中设置度量。理想情况下，[以 ems 为单位设置设计或总页面宽度，以百分比为单位设置列](http://www.456bereastreet.com/archive/201101/only_use_ems_for_the_total_width_of_em-based_layouts/ "456bereastreet.com: Only use ems for the total width of em-based layouts")，以便列、网格和整个页面设计按比例缩放。例如:

```
body {
    font-size: 62.5%;
    width: 96em;
   margin: 0 auto 0 auto;
    }
    div#content {
        width: 75%;
        float: left;
        }
    div#sidebar {
        width: 25%;
        float: right;
        }
```

在本例中，我们使用了 62.5%的技巧将`body`中的基本字体大小重置为 10 像素，并定义了 960 像素的居中总设计宽度。同时，我们定义了两个`div`元素:一个是宽度为 240 像素(960 的 25% = 240 像素)的侧边栏，另一个是宽度为 720 像素(960 的 75% = 720 像素)的内容容器。即使只有纯文本缩放可用，这种设计也能完美缩放。

## 主要的

重要的是要在字里行间留有足够的空间，这样眼睛就可以舒适轻松地沿着字里行间阅读。一个好的规则是抄写时短小节不要太靠前，长小节要靠前。

使用`line-height`属性在 <acronym title="Cascading Style Sheets">CSS</acronym> 中控制行距。您可以使用它来设置无单位的数值(例如 1.5)，从而作为字体大小的倍数:

```
p { line-height: 1.5; }
```

这意味着行距将是`font-size`的一倍半。为派生元素设置行距时，无单位值更容易跟踪和处理。它们的伸缩性也很好。

## 对齐

对齐是指文本的放置和排列。当设置文本块时，将文本与左边距或“装订线”对齐，不要害怕出现参差不齐的边缘(即“左对齐”、“左对齐”或“右对齐”)。如果有足够的措施来适应字间距的调整，并且理想情况下，如果可以使用自动断字，那么对齐就非常好。避免在窄栏文本中对齐。

使用 [`text-align`属性](https://reference.sitepoint.com/css/text-align "SitePoint.com CSS Reference Guide: text-align property")在 <acronym title="Cascading Style Sheets">CSS</acronym> 中控制对齐，并取描述值，例如:

```
body { text-align: left; }
    div#content p { text-align: justify; }
    div#content p.verse { text-align: center; }
```

## 高潮(对比)

清晰易读的文本与周围环境有很高的对比度，不会让人觉得不舒服。好的对比度是通过在设置文本时考虑上述因素，并考虑字体的颜色和背景来实现的。一个好的指导原则是浅色背景下的深色文本，反之亦然。避免颜色冲突或白色背景上几乎看不见的灰色。

在 <acronym title="Cascading Style Sheets">CSS 中，</acronym>文本颜色由 [`color`属性](https://reference.sitepoint.com/css/color "SitePoint.com CSS Reference Guide: color property")控制，而背景由 [`background-color`属性](https://reference.sitepoint.com/css/background-color "SitePoint.com CSS Reference Guide: background-color property")控制，取数值和描述值。这里有一个例子:

```
div#content p {
    color: #111;
    background-color: white;
    }
```

在深色背景上处理浅色文本时，请注意对比度。浅色背景上的深色文本通常比深色背景上的浅色文本具有更高的对比度。因此，当浅色文本停留在深色背景上时，检查其对比度——适当增加行距和减少`font-weight`。

```
div#footer p {
    color: white;
    background-color: #333;
    line-height: 1.8;
    font-weight: lighter;
    }
```

### 关闭

就是这样。应用这些原则应该为你的文本提供基本的排版质量，以及更好的易读性和可读性。

## 分享这篇文章