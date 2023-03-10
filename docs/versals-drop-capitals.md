# 范思哲和首字下沉

> 原文：<https://www.sitepoint.com/versals-drop-capitals/>

versal、 *drop capitals* 或 *raised capitals* 是大的且经常装饰华丽的字母(特别是在 versal 的情况下),标志着一段文字或一首诗的开始:

|ˈvərsəl|
*形容词*
属于或关于诗歌、段落等开头的华丽大写字母的。在手稿中，通常是用长而平的衬线在笔画之间涂墨。

名词
一封普通信件。

不久前，我在一本未命名的旧素描本的背面发现了一些精美的手绘范思哲。

## 一点历史

范思哲可以追溯到古腾堡和西方印刷术出现之前，是由抄写员手工上色的。事实上，早期的印刷术试图模仿当时的手写风格(我们将那个时代与[【黑体】](https://secure.wikimedia.org/wikipedia/en/wiki/Blackletter)风格联系在一起)，即使页面的大部分其余部分是通过凸版印刷印刷的，versal 字母仍然继续使用手写。

这种手动着墨的过程一直持续着，因为这种方法被很好地实践了，而且范思哲非常华丽，很难用早期的印刷机印刷。手写也使得 versal 更容易使用多种颜色。

现代印刷术(如果我们可以称之为现代印刷术的话——遗憾的是，大多数字体没有太多的印刷思想)已经失去了这些经典的装饰，很大程度上有利于更客观和中性的页面。我觉得许多设计师漫无目的地试图通过复制过度反光和光滑元素的趋势(将“web 2.0”视为一种设计美学)来实现一些印刷上的繁荣。

## 网络上的 verslas

在网上设置范思哲并不是特别难。快速和肮脏的方法是用图像和手动添加一个 span 标签和你的风格类到开头的信中，但是有更多标准友好和有效的方法来增加你的开头信的味道。

有一个有用的 CSS 伪选择器，我们可以用它来引用元素的第一个字母: [`:first-letter`](https://reference.sitepoint.com/css/pseudoelement-firstletter "SitePoint.com CSS Reference Guide: :first-letter pseudo-selector") 。您可以将它与其他选择器结合使用，以引用页面第一段的第一个字母、标题或任何您感兴趣的内容(正确使用 [<acronym title="Document Object Model">DOM</acronym>](https://secure.wikimedia.org/wikipedia/en/wiki/Document_Object_Model) 结构有助于使其正常工作)。

注意: Internet Explorer 在 CSS 2.1 中的选择器和伪选择器方面一直有很多问题，但它能很好地理解:首字母，并且 IE8 完全支持。

CSS 样式可能如下所示:

```
div#content h2.entry-title + p:first-letter {
    font-size: 4.2em;
    float: left;
    display: in-line;
    text-transform: uppercase;
    }
```

versal 的定位完全由你决定。您可以使用`line-height`和`margin`属性随意放置它，并使用 float 让文本环绕 versal 如果你想让 versal 远离文本并避免换行，就不要设置浮动。

更多变体见乔恩·谭的段落样式示例 [#9](http://jontangerine.com/silo/typography/p/#elevated-cap "JonTangerine.com: Paragraph Style samples-—-Elevated cap") 、 [#10](http://jontangerine.com/silo/typography/p/#drop-cap) 和 [#11](http://jontangerine.com/silo/typography/p/#outdent-cap) 。

针对第一个字母和样式很简单；浏览器一致性是最让人头疼的部分。Internet Explorer 尤其复杂，因为它不理解前面的选择器(元素+第一个子元素)。如果你的范型对页面至关重要，请参阅 Bill Weaver 的文章， *[争论首字下沉](http://bweaver.net/wrangling-drop-caps)* ，其中更详细地讨论了这个主题，包括 JavaScript 方法。

注意:当在 Internet Explorer 中打印一个使用伪选择器`:first-letter`指定的 versal 的页面时，IE 奇怪地替换了该段落附近的其他浮动元素。参见 Nicholas Rougeux 的文章 *[野外摘帽](http://www.c82.net/posts.php?id=37)* 举例。

在网上找到除了尺寸和位置之外还做了进一步修饰的通用字母是罕见的。用图形完全替换第一个字母并不是不可能的，但是在使用图像替换的过程中，你肯定会遇到许多可访问性问题(并且得到正确的字母——这意味着设计 24 个 versals 和一些脚本)。然而，我很喜欢 Piotr Fedorczyk 的简单静态方法:

![Screenshot of the versals from designr.it.](img/76b7584fca99175be3a420a7d752f536.png)

Piotr 绘制了华丽的背景图片，并把它们放在 versal 的后面，给了它们额外的爱。这里唯一的问题是背景图像的可见性，这取决于字形大小——“I”的宽度小于“R”或“Q ”,当然还有当用户增大字体大小时会发生什么。CSS 现在看起来像这样:

```
div#content h2.entry-title + p:first-letter {
    font-size: 4.2em;
    float: left;
    text-transform: uppercase;
    background-image: url(img/versal-bg.png);
    background-repeat: no-repeat;
    background-position: center center;
    }
```

你可以在彼得亚雷的网站上看到他们的行动。

找到或想出一些你自己的范型(例如，使用 Piotr 的方法)并不难。在 FontSquirrel.com 上快速搜索“blackletter”字体，得到了几个可行的面孔: *[【罗森堡装饰](https://www.fontsquirrel.com/fonts/Rothenburg-Decorative)**[【最亲爱的](https://www.fontsquirrel.com/fonts/Dearest)**[曾达](https://www.fontsquirrel.com/fonts/Zenda)* 。当然，你并不局限于使用黑色字体——你可以使用任何你喜欢的字体。

如果你发现了范思哲的任何好的用法，请在下面分享。

## 分享这篇文章