# 带有 CSS 和 XHTML 文章的可访问标题图像

> 原文：<https://www.sitepoint.com/header-images-css-xhtml/>

在 CSS 出现之前，图片总是用图片标签放入网页中。我们给他们替换文字，这样当图像关闭时他们仍然有意义——这是唯一的方法。

今天，我们有级联样式表。使用 CSS 给网页添加图片被称为“所有未来网页设计的重要基石”。它给了我们新的力量来控制外观，不仅仅是像边框这样的基本样式，而是整个网站的所有图片。

Fahrner 图像替换(FIR)是第一个广泛使用的方法，也是最广为人知的方法。但它并不完美。今天我将向你展示两种克服这些问题的方法。你可以在文章的最后看到最终的代码。

在我们开始之前，我们要问的第一个问题是:“这个图像是内容的一部分吗？”

##### 作为内容的图像

想象一下，你的网站上有一本相册。你的访问者已经到你的网站来看这些照片。图像必须是内容。

对于这些类型的图像，我们仍然应该使用久经考验的图像标签:

`<img src="sunset.jpg" alt="The sunset burning red" title="Our first night" />`

这是内容图像的完美解决方案，因为:

*   打开图像后，每个人都会得到照片:“sunset.jpg”
*   关闭图像时，图像的描述显示为:“燃烧的红色夕阳”
*   图像将像文本内容的其余部分一样打印出来

但是如果图片不是内容的一部分呢？

例如，考虑一个装饰性的标题。内容是页眉中的文本，而不是该文本的图像。

这就是图像替换的用武之地。

##### 图像替换

图像替换是一个简单的想法:取一个标准的 header 标签(`<h1>`)，隐藏它的文本，并显示一个图像。

对于这些示例，我将使用以下代码:

`<h1 id="album">My photo album</h1>`

在理想情况下，这将是您需要的所有 CSS3:

`#album { content: url(header.gif), contents; }`

只用一行，内容就被替换成了图像。找不到图像？它显示原始内容——文本。完美！

然后现实来了…没有浏览器支持 CSS3。

[W3C](https://www.w3.org/Style/CSS/current-work)在案件上。一两年后，我们可能会看到浏览器首次支持 CSS3。

今天，我们必须有创造力。

##### 这项技术

裸头代码的作用如下:

![1221_image1](img/37edd3265d3e7c513725fb39a1e5401a.png)

为了进行替换，我们必须做三件事:

1.  调整标题大小以适合图像
2.  显示图像
3.  隐藏文本

这是我们将在教程中使用的图像:

![1221_image2](img/62aa7eec19f0995d77da4936e7f736d0.png)

右键单击并选择“保存图片为…”或“保存图像…”如果你想通过我们进行的技术工作。称之为“header.gif”(无引号)。

***调整页眉尺寸***

这很简单——只需设置高度和宽度。这可以从一个头改变到另一个头，所以我们将使用头的 ID。

`<h1 id="album">My photo album</h1>
#album { width: 300px; height: 75px; }`

记住:用一个数，定一个单位。图像是以像素为单位设置的，所以我们也将以像素(px)为单位设置标题的大小。

***显示图像***

我们将为此使用背景属性:它具有良好的浏览器支持，并且包含简单的代码。

`<h1 id="album">My photo album</h1>
#album {
width: 300px; height: 75px;
background-image: url(header.gif);
background-repeat: no-repeat;
}`

现在图像看起来像这样:

![1221_image3](img/d2dd5a6704f02677ffd27d8fa732d7ef.png)

***隐藏正文***

这是决定成败的一步。我们必须隐藏文本，但仍然可以访问它。

最初的 FIR 技术使用 display: none 从页面中完全删除了文本。这未通过可访问性测试:

*   屏幕阅读器无法“看到”文本(盲人访问者无法访问)，
*   关闭图像，就没有任何内容(所以我们失去了许多拨号访问者)。

[Mike Rundle](http://phark.typepad.com/phark/2003/08/accessible_imag.html) 找到了第一个问题的聪明解决方案:不要删除文字；把它从侧面滑出就行了。

Mike 使用了文本缩进属性，但使用了负值。他有效地击败了页面上的标题。他的代码看起来像这样:

`h1 { text-indent: -100em; }`

注意这个值是以 ems 为单位的:它们随着文本的大小而变化。如果浏览器使用更大的字体，页眉会离得更远。它将永远不会回到页面上。

只有一个问题。第二点呢？如果关闭图像会发生什么？

我们的情况并不比冷杉好多少:一个空盒子。是时候采用新方法了。

***给我们看看你的文字！***

具有讽刺意味的是，在最终找到删除文本的方法后，我们又继续尝试把它找回来。

为此，我们不得不把文本放在图形之下，而不是窗口之外。这是我们想要的图片:标题图像在上面，文字在下面:

![1221_image4](img/cdff2d3f51411c5d9fc080478ff5da90.png)

因为背景图片会在文本的后面，所以我们不能在 header 标签上设置它。是时候为网速慢的用户做出牺牲了:我们需要一个额外的跨度。

下面是新的 XHTML:

`<h1 id="album"><span></span>My photo album</h1>`

我们所要做的就是我们之前所做的，除了涉及到跨度的时候。要设置 span 的高度和宽度，我们还必须设置它的 display: block 属性。

负的边距-底部将向上移动文本，但是在大多数浏览器中，这将使文本出现在跨度的顶部。我们需要在 span 上设置一个 z 索引，将其放置在文本上方。

我喜欢图片，所以这里还有一张。这一次，我添加了一个绿色边框和一个小的负边距-底部:

![1221_image5](img/61eb4f53ead7d1d2bb547f2d58a4ac94.png)

下面是将文本完全放在 span 下面的代码:

`/* repeated code per header */
#album, #album span {
width: 300px; height: 75px;
background-image: url(header.gif);
background-repeat: no-repeat;
}
/* This is what moves the text under the span */
#album span { margin-bottom: -75px; }
/* code for all styled headers */
h1 span {
display: block;
/* place the span on top */
position: relative;
z-index: 1;
}`

***溢出***

到目前为止，只要标题文本足够短，能够放在标题图像的下面，代码就能很好地工作。

当文本太长而不适合它的容器时，就称为“溢出”。我们不想看到这个文本，所以我们必须隐藏它。事实上，这正是我们需要的 CSS:

`h1 { overflow: hidden }`

如果你只担心 Windows 浏览器，故事到此结束。其他人都还有一项罪行要犯。有了这些赤裸裸的代码，Mac 版的 Internet Explorer 5 将会隐藏所有的东西。不仅仅是溢出，整个东西都不见了。噗！

这是针对 Mac 版 IE5 的问题。值得庆幸的是，一个简单的黑客技术可以让我们隐藏上面的代码行，让其他人也能看到。

我们应该给那些 Mac 用户什么呢？简单:我们将使用以前 Mike 的解决方案。以下是分为两部分的方法:

`/* IE5 Mac Hack */
h1 { overflow: hidden; }
/*/
h1 { text-indent: -100em; }
/* End Hack */`

第一行给除了 Mac 版 IE5 以外的所有浏览器。另一方面，IE5 for Mac 只看到第二行。在山姆·福斯特的网站上了解更多关于黑客的信息。

##### 总结

我们从完美的 XHTML 和完美的 CSS 开始。然后我们在浏览器支持上做了让步。

现在，我们有两种完全使用 CSS 来设计标题样式的方法，一种是使用干净的 XHTML，另一种没有图像也能工作。

我会让你来决定哪个对你的网站更重要。

***以 XHTML 为中心的标题***

`<h1 id="album">My photo album</h1>
#album {
width: 300px; height: 75px;
background-image: url(header.gif);
background-repeat: no-repeat;
}
h1 { text-indent: -100em; }`

 **`<h1 id="album"><span></span>My photo album</h1>
/* repeated code per header */
#album, #album span {
width: 300px; height: 75px;
background-image: url(header.gif);
background-repeat: no-repeat;
}
#album span { margin-bottom: -75px; }
/* non-repeated code */
h1 span {
display: block;
position: relative;
z-index: 1;
}
/* IE5 Mac Hack */
h1 { overflow: hidden; }
/*/
h1 { text-indent: -100em; }
/* End Hack */`

##### 了解更多信息

*   负文本缩进方法:迈克·朗德尔
    [http://phark.typepad.com/phark/2003/08/accessible_imag.html](http://phark.typepad.com/phark/2003/08/accessible_imag.html)
*   最初的飞行情报区:道格·鲍曼[http://www.stopdesign.com/also/articles/replace_text/](http://www.stopdesign.com/also/articles/replace_text/)
*   更多方法:
    *   谢默斯·莱希[http://www . moronicbajebus . com/playground/CSS play/image-replacement/](http://www.moronicbajebus.com/playground/cssplay/image-replacement/)
    *   斯图尔特·朗里奇[http://www.kryogenix.org/code/browser/lir/](http://www.kryogenix.org/code/browser/lir/)
*   其他有用的链接:
    *   山姆·福斯特的 IE5 Mac 黑客
        [http://www.sam-i-am.com/testsuite/css/mac_ie5_hack.html](http://www.sam-i-am.com/testsuite/css/mac_ie5_hack.html)
    *   CSS 上的 W3C
        [https://www.w3.org/Style/CSS/](https://www.w3.org/Style/CSS/)
    *   W3C CSS 验证器
        [http://jigsaw.w3.org/css-validator/](http://jigsaw.w3.org/css-validator/)
    *   通往 CSS 的五十万条道路
        [http://www.google.com/search?q=css](http://www.google.com/search?q=css)** 

## **分享这篇文章**