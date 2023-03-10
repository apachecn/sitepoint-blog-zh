# 设计表单布局:间距

> 原文：<https://www.sitepoint.com/designing-form-layout-spacing/>

以下是我们的书《设计 UX:形式 T2》的一小段摘录，作者是杰西卡·恩德斯。这是形式设计的终极指南，是有效 UX 设计的关键部分。SitePoint Premium 会员可以通过他们的会员身份访问，或者您可以在世界各地的商店购买一份。

我们可以通过一些间距的微调与用户进行微妙的交流。

### 接近度

人类将彼此接近的事物视为相关。相反，不相关的事物之间通常会有一些空间。

这些原则告诉我们要把问题的各个部分——标签、问题级帮助和答案字段——放在一起。到目前为止，我们的表单在这方面做得很好，但是让我们将标签向右移动一点，这样它们就更接近它们的字段:

![Flush right labels should be close to their fields](img/174fdf57f5a1ed181afe7721e3566a1d.png)

右对齐标签应该靠近它们的字段。

另一方面，每个问题之间需要有一定的距离:

![Questions should be far enough apart that it’s clear where one ends and the next one starts](img/1b3fb214266cc35d1035895e8a093418.png)

问题应该离得足够远，以便清楚地知道一个问题在哪里结束，下一个问题在哪里开始。

还记得用户视觉的焦点大约有九个字符宽吗？这意味着位于字段右侧的问题级帮助通常是看不到的:

![Their focus on fields means many users won’t even see the question-level help in this form](img/6e0dc7a2e1170efc7b4ed364af663a08.png)

他们对字段的关注意味着许多用户甚至看不到这种形式的问题级帮助。

我们可以将问题级别的帮助移到字段下面，但这意味着用户可能在回答之前看不到它。也比较不容易接近。

![Question-level help below the field is not especially usable or accessible](img/fa05f8efe3e2ef5cbb647854a81f16e5.png)

字段下方的问题级帮助不是特别有用或可访问。

问题级帮助的最佳位置是标签和字段之间的*(如下图所示)。格式说明(如出生日期的 DD MM YYYY)应放在字段上方:*

![Formatting instructions positioned above the field](img/ad6cd637c5e6236a3038f9acc5fe6dca.png)

位于字段上方的格式说明

其他问题级别的帮助应该放在标签下面。有关这方面的示例，请参见 ABN 的员工和营销问题:

![Other question-level help positioned below the label.](img/f23b8dcbc7f34431b851bec99cd13171.png)

其他问题级帮助位于标签下方。

最后，当我们使用复选框和单选按钮时，我们需要确保标签靠近正确的按钮或框。如果它们太远，事情会变得混乱，特别是如果你没有遮挡触摸区域:

![The distance between the labels “Yes” and “No” and their respective radio buttons means the user has to stop and think](img/28241f9e4763fd4818814cc0fbb4fcb4.png)

标签“是”和“否”以及它们各自的单选按钮之间的距离意味着用户必须停下来思考。

下面是单选按钮上间距不佳的标签的一个更极端的例子:

![It’s likely that many users will give an unintended rating, given the poor spacing here](img/e18c7dbbe163e04e4b892da070f07ab4.png)

鉴于这里的间距很小，很可能许多用户会给出一个非预期的评级。

### 文本框宽度

当我们调整间距时，让我们调整文本框的宽度。

目前，除了出生日期文本框之外，所有文本框的宽度都相同:

![Text boxes all the same width](img/832389dd81b12b2ced08d66a720adea0.png)

然而，文本框的宽度告诉用户需要什么样的信息。如果我们使大小与预期信息成比例，用户体验会更好(事实上，我们已经对出生日期做了这样的处理):

![Text boxes adjusted to have width proportional to typical answers](img/73d5aeeb35cb87fa1a31d02f18191c46.png)

注意，我们在这里讨论的是*视觉*宽度，而不是可接受的字符数。电子邮件地址字段可能具有如图所示的可视宽度，但仍应接受最多 256 个字符。

### 空文本框

文本框间距的另一个重要方面是它们在内*的空间。这个空白的地方就是用户知道他们需要在那里输入东西的地方。*

太多表单的文本框*不是*空的。两大元凶是:

1.  背景颜色
2.  占位符文本。

#### 没有背景色

不要给你的字段设置背景色。字段中的背景色使它们看起来像按钮(就像没有背景色的按钮看起来像字段一样):

![On this form, background color makes fields look like buttons, and vice versa](img/3d059409971d45fd1918a162438d2d1c.png)

在这个窗体上，背景色使字段看起来像按钮，反之亦然。

一个简单的四边边框足以告诉用户在哪里输入:

![You don’t need much to show users where their answers go](img/4fd3783f68be9047ca675d92d6e3e5d5.png)

你不需要向用户展示他们的答案。

只要确保您的边框可以被看到:

![The field borders on this form are so light they’re nearly impossible to see](img/ec035b5cf50ad51090a343eb08f9b060.png)

此表单上的字段边框太浅，几乎看不出来。

#### 没有占位符文本

比背景颜色更糟糕的是将文本放入字段中。字段中应该出现的唯一文本是用户的答案！

不幸的是，这种文本，也称为**占位符文本**，太常见了。有时是字段标签:

此表单的标签出现在字段内。

有时是问题级别的帮助:

![question-level help shown inside fields](img/82a7ff921365295dddb94644c0298aac.png)

字段中显示的问题级帮助。

有时它完全没用:

![Pointless information inside fields](img/c8ee8df889fce8b2cf6559ce92c26f16.png)

字段内无意义的信息。

不这么做太重要了，我要再说一遍。**永远不要在你的字段中包含文本**。它极大地恶化了表单填写体验，因为许多用户会:

*   认为问题已经被回答，引发错误
*   把占位符文本留在那里，弄乱你的数据
*   在占位符文本消失之前看不到它(特别是当他们键入时看着键盘)
*   无法看到所有的占位符文本，因为它受到字段可见宽度的限制
*   忘记标签上说的
*   忘记问题级帮助说了什么
*   发现文本太小，无法阅读
*   无法查看他们的答案
*   有更大的纠错困难。

此外，字段内的文本通常[不可访问](http://output.jsbin.com/vonesu/10/)，并且[占位符属性并非在所有浏览器中都得到准确支持](http://caniuse.com/#feat=input-placeholder)。(曾经有一种情况，占位符文本需要*才能访问，因为屏幕阅读器并不总是显示表单字段。现在屏幕阅读器一致使用[元素](https://www.w3.org/TR/html5/forms.html#the-label-element)来表示字段的用途。)*

“但它节省空间！”一些设计师会抗议。确实如此。但是节省空间的代价是不能真正填写表单。不要这样做。把你的文本放在你的字段之外。

## 分享这篇文章