# 学习 HTML 和 CSS:绝对初学者指南

> 原文：<https://www.sitepoint.com/html-css-beginners-guide-11/>

## 第三章。添加一些风格

在本文的前面，我们逐步完成了设置您的计算机的过程，这样我们就可以开发网站，并一起创建了一个网站，您可以用它来打动您的朋友和家人。问题是，当你来向你最亲近的人炫耀你的新网站时，他们并没有被打动。你做错了什么？

答案是:没有。诚然，这个网站目前看起来可能有点乏味，但它所基于的基础结构是坚如磐石的。回到我们的汽车类比，你现在有一个完美的底盘和一些体面的车身，虽然你的车还没有让人们回头，这只是一个时间问题。让他们看看你能用这滚动的外壳做什么！

在这一章中，我们将开始在你的网站上添加那一点点颜料。这项工作的工具是*层叠样式表* — *CSS* 给那些了解内情的人(或者打字能力有限的人)。让我们来看看 CSS 能为你做什么。

## 什么是 CSS？

因为这一章几乎完全围绕 CSS 展开，所以从什么是 CSS 以及为什么应该使用它的基本讨论开始可能是个好主意。正如我们已经提到的，CSS 代表级联样式表，但对大多数人来说这太拗口了——我们将坚持使用缩写。

CSS 是一种允许您更改页面上元素外观的语言:文本的大小、样式和颜色、背景颜色、边框样式和颜色，甚至元素在页面上的位置。让我们来看看一些正在运行的 CSS 我们将从学习*内联样式*开始。

## 内嵌样式

如果您熟悉 Microsoft Word(或类似的文字处理软件包)，您可能已经创建了相当多的传单、广告或个人通讯(以及给地方当局的更普通的信件等等)。这样做时，您可能已经使用了文本格式选项来为文本的某些部分着色。这很简单，只需突出显示您想要更改的单词，然后在下拉调色板中单击一种颜色。在 XHTML 中使用一点内联 CSS 也可以达到同样的效果。看起来是这样的:

style = " color:red；" >敏捷的棕色狐狸跳过
懒惰的狗。< /p >

在上面的例子中，我们在开始的`<p>`标签中使用了一个`style`属性。以这种方式将样式应用于特定的 XHTML 元素被称为使用“内联样式”。

## 注意:但是等一下:内嵌样式？

如果你以前接触过 CSS，在这个阶段你可能会想，“但是这不是正确的方法”，对此我说“稍等一会儿，很快就会解释清楚的。”我们只需要在找到最好的方法之前先浏览一下这些基础知识。

`style`属性可以在其引号之间包含一个或多个*声明*。声明由两部分组成:一个*属性*，以及该属性的一个*值*。在上面的例子中，声明是`color: red`(颜色是属性，`red`是它的值)。

如果您愿意，可以在上面的例子中添加另一个声明。例如，除了用红色显示文本之外，您可能还希望它用粗体显示。控制这种效果的属性是字体粗细；它可以有一系列不同的值，但大多数情况下您会使用`normal`或`bold`。如您所料，您将使用以下标记来使段落变红并加粗:

style = " color:red；font-weight:bold；" >敏捷的棕色狐狸
跳过懒惰的狗。< /p >

请注意，分号分隔了这两个声明。您可以继续以这种方式添加样式，但是要注意，这种方法可能会很麻烦。有更聪明的方法来应用样式，我们很快就会看到。

**Go to page:** [1](https://sitepoint.com/html-css-beginners-guide) | [2](https://sitepoint.com/html-css-beginners-guide-2/) | [3](https://sitepoint.com/html-css-beginners-guide-3/) | [4](https://sitepoint.com/html-css-beginners-guide-4/) | [5](https://sitepoint.com/html-css-beginners-guide-5/) | [6](https://sitepoint.com/html-css-beginners-guide-6/) | [7](https://sitepoint.com/html-css-beginners-guide-7/) | [8](https://sitepoint.com/html-css-beginners-guide-8/) | [9](https://sitepoint.com/html-css-beginners-guide-9/) | [10](https://sitepoint.com/html-css-beginners-guide-10/) | [11](https://sitepoint.com/html-css-beginners-guide-11/) | [12](https://sitepoint.com/html-css-beginners-guide-12/) | [13](https://sitepoint.com/html-css-beginners-guide-13/) | [14](https://sitepoint.com/html-css-beginners-guide-14/) | [15](https://sitepoint.com/html-css-beginners-guide-15/) | [16](https://sitepoint.com/html-css-beginners-guide-16/) | [17](https://sitepoint.com/html-css-beginners-guide-17/) | [18](https://sitepoint.com/html-css-beginners-guide-18/) | [19](https://sitepoint.com/html-css-beginners-guide-19/)

## 分享这篇文章