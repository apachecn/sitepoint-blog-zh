# 学习 HTML 和 CSS:绝对初学者指南

> 原文：<https://www.sitepoint.com/html-css-beginners-guide-15/>

## 新风格的混合

让我们再改变一下网站的外观——我们将在正文中添加更多的样式，并改变导航的外观。将下面的 CSS 复制到您的`style1.css`文件中(或者从本书的代码档案中复制):

/*
CSS for Bubble Under site
*/

body {
font-family:宋体、宋体、宋体、无衬线；
**背景色:# e2edff
行高:125%；
填充:15px**
}

h1 {
font-family:“投石机 MS”，Helvetica，Arial，sans-serif；
font-size:x-large；
}

**李{
font-size:
}

h2 {
颜色:蓝色；
font-size:中等；
font-weight:正常；
}
p {
font-size:small；
颜色:藏青色；
}**

保存 CSS 文件，然后在浏览器中点击**重新加载**(或**刷新**)。希望你看到的是如图 3.7 所示的页面，“对影响字体显示的 CSS 应用微妙的变化”。

![Applying subtle changes to the CSS that affects the display of the font](img/7be892498be716638828e2a351acca52.png)

## 一瞬间焕然一新！

我们在这里引入了不少新的样式声明。让我们按照它们在 CSS 文件中出现的顺序来检查其中的几个:

字体系列:Helvetica，宋体，无衬线字体；
**背景色:# e2edff**
**行高:125%；**
**填充:15px**
}

属性可以应用于网页上的大多数元素，并且有许多不同的方式来指定颜色本身。一种是使用公认的颜色名称，如`navy`、`blue`、`red`、`yellow`等。这些容易记忆和拼写，但你可能会受到范围的限制。引用颜色的另一种方式是使用十六进制的*颜色规范。是的，你说得对:那个*确实*听起来有点吓人。我的意思是，看看它的代码:*

背景色:**# e2edff**；

这不太直观，是吧？这个看起来模糊的参考(`#e2edff`)翻译成一种浅蓝色。作为一个初学者，你不能开始猜测这将是一个浅蓝色。谢天谢地，网上有很多工具可以让你从图表中选择一种颜色(通常被称为*颜色选择器*)，然后给你匹配的代码。看一看这些工具，[5]你很快就能找到创建理想配色方案所需的十六进制数。

### 注意:到底是什么妖术？

HTML 中的颜色通常被写成十六进制的颜色规范。你可能还记得高中数学课上的十六进制计数系统。或者你在房间后面睡着了。没关系。十六进制是那种奇怪的计数系统，它会上升到 16 而不是 10；你认为你永远不会有任何实际用途的那个。好吧，你现在知道了！

没错:十六进制数的时候，不是十，而是 **16 位**。十六进制序列如下所示:

0，1，2，3，4，5，6，7，8，9，A，B，C，D，E，F，10，11，12…

呃？这里发生了什么事？正如你所看到的，在我们到达 9 之后，我们不是直接到达 10(就像我们用十进制计数时那样)，而是经过 A、B、C、D、E 和 F，最后到达 10。这给了我们六个额外的数字来计数。听起来很困惑？嗯，碰巧的是，计算机在计算十六进制数方面比人类好得多！

这里的关键是，所有我们知道并喜爱的十进制数字，如 2，748，15，000，000 和 42，都可以用十六进制表示。而表 3.1，“十进制到十六进制的转换”就证明了这一点！

**十进制到十六进制的转换**

| **Decimal** | **Hexadecimal** |
| seven | seven |
| Fifteen | F |
| Two thousand seven hundred and forty-eight | 字母表 |
| Fifteen million | E4E1C0 |
| sixty-nine | Forty-five |

当一种颜色被表示为十六进制数时，比如`ff0000`，这个数实际上由三个连接在一起的值组成。这些值表示混合在一起以创建指定颜色的红色(`ff`部分)、绿色(前两个零)和蓝色(后两个零)的比例。这三种原色可以组合起来在屏幕上显示任何颜色，类似于电视机使用不同数量的红色、绿色和蓝色在屏幕上创建一个点的方式。[6]在这个例子中，`ff`是红色的值，而绿色和蓝色的值为零。那么，当你得知`#ff0000`会给你红色时，你可能不会感到惊讶。

行高属性是一个有趣的属性。通过增加这个值(在我们的例子中我们使用了 125%)，您可以增加文本行之间的间距——这可以大大提高可读性。尝试调整这个值，保存您的 CSS 文件，并查看新值如何影响您的网页上的文本。

padding 属性用于在相关元素的外部边缘和位于其中的内容之间提供空间。因为我们指的是`body`元素，所以你可以把外部边缘想象成浏览器的*视窗*的顶部、底部和侧面(这是浏览器的一部分，在这里可以看到网页，不包括浏览器的任何工具栏、菜单或滚动条)。我们将在《第 4 章，使用 CSS 塑造》中更详细地了解填充。

我们赋予该属性的值指定了在视窗边缘和内容之间必须存在多少空间。在这种情况下，我们指定了`15px`，即 15 个像素。我们之前提到过像素，当我们指定图像的大小时，但是什么是像素*？基本上，一个像素是组成你在计算机屏幕上看到的东西的微小点之一。屏幕本身就是由成千上万的这些像素组成的，所以 15 像素的边框不会占用你屏幕太多的空间！*

 *现在，关于段落样式:

p {
font-size:small；
**颜色:藏青色；**
}

我们已经展示了改变段落中文本的颜色是可能的；现在，我们要决定合适的藏青色。

让我们看看列表项样式有什么变化:

李{
**字号:小；**
}

通过应用`font-size`属性，列表项的大小发生了微小的变化。这里，我们决定使用关键字`small`设置字体大小，但是我们也可以使用百分比或像素方法。正如我们已经看到的——使用 CSS 有很多方法可以剥一只猫的皮！字体大小关键字的范围从`xx-small`到`xx-large`，并提供了一种快速设置文本样式的方法。不幸的是，不同的浏览器实现字体大小的关键字略有不同，不幸的是，你不能保证一个`xx-large`字体将在所有浏览器中以相同的大小呈现。但是，除非您非常担心精确的大小，否则这些关键字是一个很好的起点。

我们还为`h1`元素(网页上的主要标题，显示网站名称)引入了一个新规则，并且再次使用了一个`font-size`属性来指定文本的大小(特大！).

h1 {
font-family:“投石机 MS”，Helvetica，Arial，无衬线；
**font-size:x-large；**
}

`h2`元素也进行了小改:

h2 {
颜色:蓝色；
font-size:medium；
font-weight:正常；
}

浏览器通常以粗体显示标题，但是我们可以通过给`font-weight`属性一个值`normal`来让它们以标准字体显示。

**Go to page:** [1](https://sitepoint.com/html-css-beginners-guide) | [2](https://sitepoint.com/html-css-beginners-guide-2/) | [3](https://sitepoint.com/html-css-beginners-guide-3/) | [4](https://sitepoint.com/html-css-beginners-guide-4/) | [5](https://sitepoint.com/html-css-beginners-guide-5/) | [6](https://sitepoint.com/html-css-beginners-guide-6/) | [7](https://sitepoint.com/html-css-beginners-guide-7/) | [8](https://sitepoint.com/html-css-beginners-guide-8/) | [9](https://sitepoint.com/html-css-beginners-guide-9/) | [10](https://sitepoint.com/html-css-beginners-guide-10/) | [11](https://sitepoint.com/html-css-beginners-guide-11/) | [12](https://sitepoint.com/html-css-beginners-guide-12/) | [13](https://sitepoint.com/html-css-beginners-guide-13/) | [14](https://sitepoint.com/html-css-beginners-guide-14/) | [15](https://sitepoint.com/html-css-beginners-guide-15/) | [16](https://sitepoint.com/html-css-beginners-guide-16/) | [17](https://sitepoint.com/html-css-beginners-guide-17/) | [18](https://sitepoint.com/html-css-beginners-guide-18/) | [19](https://sitepoint.com/html-css-beginners-guide-19/)*

## *分享这篇文章*