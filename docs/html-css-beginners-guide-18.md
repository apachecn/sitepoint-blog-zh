# 学习 HTML 和 CSS:绝对的初学者指南文章

> 原文：<https://www.sitepoint.com/html-css-beginners-guide-18/>

##### 哪条规则赢了？

当我们为标题添加分组声明时，我们改变了之前设置的一些样式。查看源代码可以发现，在我们的样式表中，二级标题`h2`在不同的地方被设置为蓝色*和白色*:

h2 {
**颜色:蓝色；**T3:font-size:medium；
font-weight:正常；
}

……

h1，h2，h3 {
font-family:“投石机 MS”，Helvetica，Arial，sans-serif；
背景色:藏青色；
**颜色:白色；**}

因为指定`h2`应该是白色的声明在后面出现，所以它覆盖了前面的声明。如果您已经通过样式表将一个`h2`定义为蓝色 100 次，也没有关系；如果最后一个定义说它应该是白色的，那么它就是白色的！

##### 概述我们的进展

是时候休息了。我们学到了什么？好了，我们已经学习了更多可以在 CSS 中应用的样式，我们已经看到了如何根据上下文来设计某些元素的样式，最近，我们还讨论了如何对需要以相同方式设计的元素进行分组。有一件事我们只是简单地谈了一下，但它需要更多的关注，因为它对网络的运行方式非常重要。这个主题就是链接。

##### 样式链接

链接在网络上无处不在:它们确实是你在网上看到的一切的基础。如今，我们已经习惯于看到由大量不同的图片和功能装饰的极具装饰性的网页。然而，如果时间倒退一步，你会发现万维网只不过是链接文档的集合。回到最早的浏览器，你会看到那些链接是带下划线的，这种情况一直持续到今天。默认情况下，浏览器对链接使用以下配色方案:

*   **蓝色**:未访问的链接
*   **紫色**:链接到你以前访问过的网页
*   **红色**:活动链接(你正在点击的链接；您可能已经注意到，当您最初点击链接时，链接会短暂地闪烁红色)

这种配色方案并不符合每个人的口味，但它是我们目前坚持的。至少，如果我们不能使用 CSS 来重新定义这些颜色，这就是我们*将会遇到的情况。*

在最基本的情况下，链接的 CSS 样式可能如下所示:

a {
font-weight:bold；
颜色:黑色；
}

现在，你的链接不再是蓝色和正常的字体粗细，而是以黑色粗体显示。试着把它添加到你的`style1.css`文件中，然后保存它，看看它如何影响你的网页——图 3.13，“把我们导航中的所有链接都设为粗体和黑色”说明了这一点。

![Styling all the links in our navigation to bold and black](img/b5bdfc51596d8ad54deb105c1e43ea2e.png)

##### 链接状态

正如我之前提到的，你会在网页上遇到不同类型的链接(未访问的，已访问的，活动的)。还有一种状态我没有提到，但你可能很熟悉:悬停状态(当你把光标放在链接上时发生)。在 CSS 中，你可以使用*伪类*改变所有这些链接状态的样式，这听起来很复杂，但实际上相当简单。你可以把一个伪类想象成一个内部类，当它处于某种状态时，浏览器会自动应用到链接上。下面是一些显示不同链接状态的颜色/样式方案的 CSS:

a {
font-weight:bold；
}

**答:链接** {
颜色:黑色；
}

**答:参观过的** {
颜色:灰色；
}

**a:悬停** {
text-decoration:无；
颜色:白色；
背景色:藏青色；
}

**a:活性** {
颜色:水绿色；
背景色:藏青色；
}

通过使用`a`元素选择器，并通过添加冒号(`:`)和伪类`link`、`visited`、`hover`和`active`，在 CSS 中处理不同的状态。将伪类添加到样式表意味着当元素处于伪类指定的状态时，浏览器会应用规则。

*提示:让你的链接状态井然有序*

*浏览器通常不在乎你在 CSS 文件中指定规则的顺序，但是链接应该总是按照上面显示的顺序来指定:`link`、`visited`、`hover`和`active`。努力记住 LVHA 这几个字母。更愤世嫉俗的用户可能会发现用“爱”这个短语更容易记住这个助记符。哈！”我们应该感谢杰弗里·泽尔德曼给了我们这颗小宝石。(用网络标准设计，杰弗里·泽尔德曼，新骑手。)*

让我们更改项目站点中不同链接状态的样式:

*   打开项目站点的 CSS 文件(`style1.css`)，在文件底部添加上面的 CSS。
*   保存 CSS 文件。
*   在浏览器中打开三个网页中的任何一个(或点击重新加载)来查看样式化的链接是如何显示的。

下图显示了三种不同的链接状态:主页链接未被访问，指向“关于我们”页面的链接显示其之前已被访问过(以灰色显示)，指向“联系我们”页面的链接正被用户光标悬停。

请随意在 CSS 文件中试验不同的前景色和背景色，以及本章前面表格中详细介绍的其他文本格式样式。

![Styling three different link states using CSS](img/c487d02d2f543ece60b613afc8281d39.png)

*提示:清除您的历史记录*

您的浏览器会自动存储一定量的浏览历史记录，并使用这些信息来决定某个链接是否被访问过(以及如何显示该链接)。如果你正在建立一个站点并测试链接，你可能想要检查一个未被访问的链接看起来如何，但是，因为你的浏览历史，它们可能都显示为已经被访问过。我们的三页项目网站几乎肯定是这种情况——导航列表中的链接可能都是灰色的。若要重置，您可以清除浏览器的历史记录。在 IE 中，选择工具>互联网选项。你会在浏览历史下看到一个按钮，上面写着删除。点击这个会弹出一个删除浏览历史的对话框，有更多的选项，如图 3.15，“清除 IE 中的历史再次显示未访问的链接样式”；确保选中了历史复选框，然后单击对话框中的删除按钮。之后，重新加载网页。您可能访问过的任何链接现在都将显示为未访问过。(在旧版本中，清除历史记录的过程要简单一些，只需在互联网选项对话框中寻找“清除历史记录”按钮即可)。

其他浏览器也有类似的选项，可以在工具>选项或首选项>隐私等位置找到。我不会在这里列出各种浏览器删除你的历史记录的所有不同方法，但如果你四处翻找，应该可以找到它们，没有太大的困难。

**Go to page:** [1](https://sitepoint.com/html-css-beginners-guide) | [2](https://sitepoint.com/html-css-beginners-guide-2/) | [3](https://sitepoint.com/html-css-beginners-guide-3/) | [4](https://sitepoint.com/html-css-beginners-guide-4/) | [5](https://sitepoint.com/html-css-beginners-guide-5/) | [6](https://sitepoint.com/html-css-beginners-guide-6/) | [7](https://sitepoint.com/html-css-beginners-guide-7/) | [8](https://sitepoint.com/html-css-beginners-guide-8/) | [9](https://sitepoint.com/html-css-beginners-guide-9/) | [10](https://sitepoint.com/html-css-beginners-guide-10/) | [11](https://sitepoint.com/html-css-beginners-guide-11/) | [12](https://sitepoint.com/html-css-beginners-guide-12/) | [13](https://sitepoint.com/html-css-beginners-guide-13/) | [14](https://sitepoint.com/html-css-beginners-guide-14/) | [15](https://sitepoint.com/html-css-beginners-guide-15/) | [16](https://sitepoint.com/html-css-beginners-guide-16/) | [17](https://sitepoint.com/html-css-beginners-guide-17/) | [18](https://sitepoint.com/html-css-beginners-guide-18/) | [19](https://sitepoint.com/html-css-beginners-guide-19/)

## 分享这篇文章