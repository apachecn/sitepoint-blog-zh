# 具体到你的 CSS 样式

> 原文：<https://www.sitepoint.com/get-specific-css-styles/>

除了首字母缩略词 CSS 中的 C 之外，样式表被描述为“级联”的事实指的是将样式应用于文档中的元素的一个重要(如果复杂的话)的部分。它被称为 CSS 级联，因为样式声明级联到来自许多来源的元素。

级联结合了许多因素，以便准确地确定——并且没有冲突——哪个声明应该应用于任何给定的元素。影响样式规则是否生效的因素是重要性、来源、来源顺序和(可能是最容易被误解的，也是记录最少的因素)特殊性。

特异性是 CSS 级联中帮助解决冲突的一种机制。特定性的概念表明，当两个或多个应用于相同元素并设置相同属性的声明具有相同的[重要性和来源](https://reference.sitepoint.com/css/cascade)时，具有最特定选择器的声明将优先。

考虑这个例子:

```
p { 

  color: black; 

  background-color: white; 

} 

div.warning p { 

  color: red; 

} 

div#caution p { 

  color: yellow; 

} 

body#home div p { 

  color: white; 

}
```

上面的示例样式表包含四个样式规则，它们有一个匹配`p`元素的选择器。因为其中一个规则有一个元素类型选择器`p`，所以可以保证两个或更多规则将应用于同一个`p`元素，并且因为它们都包含一个`color`属性声明，所以用户代理需要一种方法来确定应该应用哪个声明。元素`p`的最终`color`值是多少？

简单的答案是更具体的选择器声明将优先。用户代理计算每个选择器的特异性以便进行比较，并通过选择其选择器具有最高特异性的声明来解决死锁。

本文摘自 SitePoint 的*终极 CSS 参考*，是[网上免费提供的](https://reference.sitepoint.com/css/)，可以购买到[的精装版](https://www.sitepoint.com/books/cssref1/)。

##### 计算特异性

下面是对比较两个或多个声明的选择器的特异性的过程的简单描述:

1.  如果一个声明来自一个`style`属性，而不是一个带有选择器的规则(内嵌样式)，那么它具有最高的特异性。如果没有任何声明是内联的，请继续执行第二步。

3.  对 [ID 选择器](https://reference.sitepoint.com/css/idselector)进行计数。计数最高的声明具有最高的特异性。如果两个或更多具有相同数量的 ID 选择器，或者它们都没有 ID 选择器，请继续执行步骤三。

5.  数一数[类选择器](https://reference.sitepoint.com/css/classselector)(例如`.test`)[属性选择器](https://reference.sitepoint.com/css/attributeselector)(例如`[type="submit"]`)[伪类](https://reference.sitepoint.com/css/pseudoclasses)(例如`:hover`)。总计最高的声明具有最高的特异性。如果两个或两个以上的总数相同，或者它们的总数都为零，请继续执行第四步。

7.  统计[元素类型选择器](https://reference.sitepoint.com/css/elementtypeselector)(例如`div`)和[伪元素](https://reference.sitepoint.com/css/pseudoelements)(例如`:first-letter`)。总计最高的声明具有最高的特异性。

如果两个或多个选择器具有相同的特异性，那么根据 CSS 级联的规则，后一个指定的规则优先。

如果你想更专业一点，W3C 推荐标准(6.4.3) 描述了计算选择器特异性的方法。此计算的结果采用四个逗号分隔值的形式，`a,b,c,d`。(这不同于 CSS1 规范，在 CSS1 规范中，特异性采用数字分数的形式，如在[https://www.w3.org/TR/CSS1#cascading-order](https://www.w3.org/TR/CSS1#cascading-order)中所解释的。)这里，“`a`”列中的值最重要，“`d`”列中的值最不重要。选择器的特异性计算如下:

*   要计算`a`，如果声明来自样式属性而不是带有选择器的规则(内联样式)，则计数 1，否则计数 0。
*   要计算`b`，请计算选择器中 ID 属性的数量。
*   要计算`c`，请计算选择器中其他属性和伪类的数量。
*   要计算`d`，请计算选择器中元素名称和伪元素的数量。

对这些元素进行计数的结果不是一个分数，而是一个可以逐列比较的值的矩阵。例如，考虑以下规则，它包含上一个示例中的元素类型选择器:

```
p { 

  color: black; 

  background-color: white; 

}
```

如果我们试图在特异性公式的基础上计算出上述选择器的特异性，我们会得到类似于`0,0,0,1`的结果，因为它只有一个元素名。

正如我们之前所说，这不是一个数字，而是四个逗号分隔的值，其中 a 列(内联样式)中的值最重要，d 列(元素名称和伪元素)中的值最不重要。当比较选择器以确定哪一个具有最高的特异性时，从左向右看，并比较每一列中的最高值。因此，b 列中的值将覆盖 c 列和 d 列中的值，不管它们是什么。因此，`0,1,0,0`的特异性将大于`0,0,10,10`的特异性。

##### 逐步具体化

让我们试着分解计算程序的每个部分，这样更容易理解。

第一步是计算列`a`的值，我们已经在[表 1](https://reference.sitepoint.com/css/specificity#specificity__step-one) 中完成了。如果样式规则是在元素的 HTML 样式属性中指定的，`a`应该等于`1`；否则应该等于`0`。事实上，这是列`a`中有值的唯一情况。

![Table 1\. Inline Style: Column a = 1 Inline Style](img/1827ddd65ff8609ee74d7eac0b2515a6.png)

正如您所看到的，内联样式规则总是有一个特定性`1,0,0,0`——最高级别的特定性。下面是这样一个样式规则的例子:

```
<p style="color:red;">Red Text</p>
```

这就是为什么应该避免内联样式的原因之一。由于内联样式规则总是具有最高的特异性，在 CSS 级联中覆盖它们的唯一方法是在相关声明中使用 [`!important`语句](https://reference.sitepoint.com/css/importantdeclarations)——这种方法会造成维护上的噩梦。

对于内联样式之外的规则，我们需要计算列`b`、`c`和`d`。让我们对以下规则进行一次完整的计算:

```
body#home div#warning p.message { 

  color: red; 

}
```

上面的规则有一个选择器`body#home div#warning p.message`和一个声明`color: red;`。由于这不是内联样式，我们从第一列的`0`开始，如[表 2](https://reference.sitepoint.com/css/specificity#specificity__column-a-calc) 所示。

![Table 2\. Column a = 0 Inline Style](img/7ed9b7304dc0e110f21fea4375c0ba78.png)

为了计算列`b`的值，我们计算选择器中 ID 选择器的数量。在我们的选择器`body#home div#warning p`中，有两个——`#home`和`#warning`——因此，列`b`等于`2`，如[表 3](https://reference.sitepoint.com/css/specificity#specificity__column-b-calc) 所示。

![Table 3\. Column b = 2](img/6c40c1e7b44d72b36eac8967fd2dc8bf.png)

接下来，我们计算列`c`的值，计算选择器中的类选择器、属性选择器和伪类的数量。

【their 的属性选择器
*注意`[id="foo"]`并不等同于`#foo`——您可以看到它们的特异性水平有显著的差异。*

在我们的示例选择器`body#home div#warning p.message`中，有一个类选择器`.message`，因此，正如您在[表 4](https://reference.sitepoint.com/css/specificity#specificity__column-c-calc) 中看到的那样，`c`等于`1`。

![Table 4\. Column c = 1 Inline Style](img/b9c18dac279b2b9ffa2e342c07f874f7.png)

最后，对于列`d`，我们统计元素类型选择器和选择器中伪元素的数量。在我们的示例选择器`body#home div#warning p.message`中，有三个:`body`、`div`和`p`。没有要统计的伪元素，所以我们在最后一列放一个`3`，如[表 5](https://reference.sitepoint.com/css/specificity#specificity__column-d-calc) 所示。

![Table 5\. Column d = 3](img/c3f5f19fd065fcf330c293ae871d1de6.png)

我们现在有了结果。选择器`body#home div#warning p.message`的特异性可以表示为:`0,2,1,3`。

好吧，让我们考虑一个疯狂的情况，其中同一个`p`元素的半打以上的颜色声明具有相同的重要性和来源级别。浏览器会对元素应用哪种颜色？

这是我们疯狂的样式表:

```
p.message { 

  color: green; 

} 

#home #warning p.message { 

  color: yellow; 

} 

#warning p.message { 

  color: white; 

} 

body#home div#warning p.message { 

  color: blue; 

} 

p { 

  color: teal; 

} 

* body#home>div#warning p.message { 

  color: red; 

} 

#warning p { 

  color: black; 

}
```

我们应该能够使用特异性计算方法来计算出哪个声明将被应用。但是，等一下！[通用选择器](https://reference.sitepoint.com/css/universalselector)、`*`、[子组合子](https://reference.sitepoint.com/css/childselector)、`>`的具体程度如何？

答案是它们根本没有任何特异性；它们在所有的计算中都被忽略了。这对于所有的组合子都是正确的，你可以把它们当作专一性为零，因为它们对你的计算没有影响。毕竟，上面的七个选择器中有五个使用了[后代组合子](https://reference.sitepoint.com/css/descendantselector)，你不用担心这些！

在查看[表 6](https://reference.sitepoint.com/css/specificity#specificity__tbl_selectorspecificityresults) 中的答案之前，看看你能否自己算出上面所有选择器的特异性。

![Table 6\. Selector Specificity Results](img/ba592ff7239d777eba2dfa2ae5c1e5fc.png)

结果已经根据特异性进行了排序——最高的在顶部，最低的在底部。正如你所看到的，最上面的两个选择器具有完全相同的特异性，尽管其中一个有额外的通用选择器和组合子。在这种情况下，它们因特殊性而并列，最后出现在样式表中的将是赢家。如果您查看上面的原始样式表源代码，红色将被应用于`p`元素。

从表 6 中可以看出，选择器`p.message`比选择器`#warning p`具有更低的特异性。这是 CSS 新手感到困惑的一个常见原因，他们经常认为一个类选择器足够具体，可以在所有情况下匹配一个元素。

要了解更多关于 CSS 语言的知识，请访问[在线参考文献](https://reference.sitepoint.com/css)，并查看[精装版](https://www.sitepoint.com/books/cssref1/)。

## 分享这篇文章