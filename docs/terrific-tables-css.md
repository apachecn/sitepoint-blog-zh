# 使用 CSS 的极好的表格

> 原文：<https://www.sitepoint.com/terrific-tables-css/>

**这是我的书 [*的摘录，CSS*](https://www.sitepoint.com/books/cssdesign1/) 的艺术&科学，只提供未来 14 天的免费下载。只要在 Twitter 上关注 [@sitepointdotcom](http://twitter.com/sitepointdotcom) ，你就能收到免费的 PDF。这是一个推特的赠品。立即获取您的副本！**

你是否已经深入网络标准，并认为你再也不会遇到`table`元素了？由于过去作为布局元素的滥用，表格可能已经很快从“坏”和“邪恶”中消失，但网络标准运动并没有完全将它们从地球上消除。事实上，语义标记的普及所做的只是让表格回到它们最擅长的领域:显示表格数据。

虽然表格数据(以及它可能让你想起的电子表格的恐怖之处)可能并不总是最令人兴奋的工作材料，但使用表格让我们有足够的机会发挥一些重要的 CSS 技能并创造一些奇妙的外观——甚至在增加少量可用性的同时。

在这一章中，我们将花一些时间来了解构成一个表的元素。在我们建立了这个基础之后，我们将看看可以应用于这些表格元素的各种样式。在这个过程中，我们将处理跨浏览器的问题，这些问题肯定会在 Web 历史的这个时刻突然出现。理论讲完了，我们将通过一些实际例子来说明如何使我们的表格既实用又有吸引力，并了解少量 JavaScript 可以为不起眼的表格带来的一些好处。最后，我们将展望未来，预测 CSS 3 将如何影响我们的表格设计工作。

##### 该结构

设计表格的样式可以同时带来解放和困惑。虽然组成一个表的多个元素提供了很多方式来配合一些额外的样式，但是跨浏览器的不一致和缺乏对一些真正有用的 CSS 选择器的支持可能会成为令人沮丧的障碍。

然而，在我们处理复杂的表格样式之前，让我们先来看看表格可能包含的所有不同元素。其中大部分可能是熟悉的领域，但可能会有一些您以前没有遇到过的新元素。如果这些基础工作显得有些枯燥，我很抱歉，但它非常值得你的关注。把表格样式想象成过山车；在你开始疯狂的造型之前，你必须花时间在漫长缓慢的上升过程中！

我敢肯定，到目前为止，您所创建的所有表格都至少使用了三个基本元素:`table`、`tr`和`td`——它们分别是表格、行和数据单元格。同样，您可能已经使用或见过标题单元格`th`。您的标记可能如下所示:

**例 1。`table-example-basic.html`(节选)**

```
 <table> 

  <tr> 

    <th scope="col">Person</th> 

    <th scope="col">Web Site</th> 

  </tr> 

  <tr> 

    <td>Bryan Veloso</td> 

    <td><a href="http://avalonstar.com/">Avalonstar</a></td> 

  </tr> 

  <tr> 

    <td>Dan Rubin</td> 

    <td><a href="http://superfluousbanter.org/"> 

SuperfluousBanter</a></td> 

  </tr> 

</table>
```

然而，这些并不是一张桌子的所有基本组成部分。我们还有`thead`、`tbody`、`tfoot`、`caption`、`col`和`colgroup`元素供我们使用。所有这些元素都服务于一个非常语义化的目的，每一个我都会稍微详细地解释一下，这样你就知道什么时候用什么元素了。这些元素中的每一个都将提供一个点，在这里我们可以挂钩一些 CSS 样式，将我们的表格从我们页面上的一个无聊的黑点变成一个独立的迷你艺术品。

**元素`table`元素**

没有`table`元素的表格不是表格。一切都从这里开始。

一个`table`有很多属性，比如`border`、`cellpadding`和`cellspacing`，如果你是从网页设计的表格布局学校出来的，你会经常用到这些属性。我们现在可以忽略`border`和`cellpadding`，因为我们可以在 CSS 中复制这些属性。我们需要随时掌握的一个表示属性是`cellspacing`。Internet Explorer 不支持通过 CSS 处理`cellspacing`的能力，这意味着如果我们需要保持控制，我们将不得不在 HTML 级别上这样做。

除了这些属性，我们还有`frame`属性和`rules`属性。`frame`属性控制表格最外层边框的显示。其可能值为`void`、`above`、`below`、`hsides`、`vsides`、`lhs`、`rhs`、`box`和`border`。`void`是默认值，将从表格周围移除边框。

在我用来测试该标记的四种浏览器中，边框的表现方式各不相同:

*   Internet Explorer 在所有边上都呈现了三维边框。
*   Opera 渲染了一个纯黑的边框。
*   Firefox 在左侧和顶部呈现灰色边框，在右侧和底部呈现黑色。
*   Safari 没有呈现任何边框。

当 Internet Explorer 被赋予除`void`之外的值时，该浏览器也将错误地在表格内的单元格上呈现边框。例如，如果您指定`lhs`，每个单元格的左侧将被渲染:

```
<table frame="lhs">
```

Firefox 和 Opera 正确地呈现了这个标记，如下所示:

**图一。带有`frame="lhs"`的表格，由 Internet Explorer、Firefox、Opera 和 Safari** 呈现

![In Internet Explorer](img/860fc3dc458e444a5093b75211c2b116.png)

![In Firefox](img/6cc04fb1f43ccfa41c2c88a01f13adaa.png)

![In Opera](img/b3307e0ff0289a0ebfa3a30efd603b54.png)

![In Safari, which shows no frame](img/48a7978773bf33a502b4125ab8cb123f.png)

`rules`属性控制如何绘制表格的分隔线，它有五个有效值:`none`、`groups`、`rows`、`cols`和`all`。如果指定了默认值`none`，单元格之间将不会绘制任何线条。

这里值得注意的一点是，如果您没有指定一个`rules`属性，您为`colgroup`元素或`col`元素设置的边框样式(使用 CSS)将被忽略。但是，如果您指定一个值`none`，那么边框样式会突然变得生动起来。

值`groups`将在每个`thead`、`tfoot`、`tbody`和`colgroup`周围应用边框(在 Internet Explorer 中为灰色和斜角，在 Firefox 和 Opera 中为单像素和黑色)。将`rules`设置为`rows`或`cols`将在各个行或列之间应用边界。`all`将在每个单元格周围应用边框。同样，如果`frame`属性被省略并且`rules`被设置为除了`none`之外的任何值，IE 从包中分离并在整个表格周围显示一个边框。和`frame`属性一样，Safari 不支持`rules`属性。图 2 显示了四种最常见的浏览器的当前版本的输出。

**图二。比较`frame="hsides"`和`rules="groups"`适用于`table`和**

![In Internet Explorer](img/1eae7b2d30593305dc1150da4c6d6e06.png)

![In Firefox](img/25e2bb7af433195c2340a7ecd8fe946f.png)

![In Opera](img/66bfb324e02434d1e97dc7090e9160f1.png)

![In Safari, which shows no frame](img/a5ad628de43885c027c66f6904427555.png)

如果你希望使用`frame`或`rules`属性，最好一起使用，因为如果单独使用它们，会导致令人沮丧的渲染错误。

**元素`caption`元素**

`caption`旨在显示表格内容的摘要，默认情况下，它出现在表格上方的中心位置，如图 3 所示。A `caption`没有任何特殊属性，这使得我们的样式相当简单。

`caption`元素出现在`<table>`标签之后:

```
<table frame="hsides" rules="groups">  

  <caption>Sites that I like to visit</caption>  

  &hellip;
```

**图 3。Firefox** 中`caption`元素的默认显示

![This caption is displayed above the table's body](img/0c32dad038776f4d905b0c5c23ac8475.png)

**`thead``tbody``tfoot`元素**

`thead`、`tbody`和`tfoot`元素被称为*行组*。它们的功能是将多行组合在一起。一个`table`只能有一个`thead`和一个`tfoot`，但是可以有多个`tbody`元素。下面的示例演示了这些元素的预期用途:

**例二。`table-example.html`(节选)**

```
<table frame="hsides" rules="groups">  

  <caption>Sites that I like to visit</caption>  

  <thead>  

    <tr>  

      <th scope="col">Person</th>  

      <th scope="col">URL</th>  

    </tr>  

  </thead>  

  <tfoot>  

    <tr>  

      <td colspan="2">[1] Enjoys Dance Dance Revolution</td>  

    </tr>    

  </tfoot>  

  <tbody>  

    <tr>  

      <td>Bryan Veloso [1]</td>  

      <td><a href="http://avalonstar.com/">Avalonstar</a></td>  

    </tr>  

    <tr>  

      <td>Dan Rubin</td>  

      <td><a href="http://superfluousbanter.org/">  

        SuperfluousBanter</a></td>  

    </tr>  

  </tbody>  

</table>
```

从这个例子中您可能会注意到，页脚实际上出现在正文之前。请看图 4，看看它在浏览器中的样子，您会注意到页脚位于表的末尾，也就是它应该在的地方。“怎么回事？”你问得很有道理。该规范被设计成允许在收到全部内容之前呈现一个`table`。

**图 4。`tfoot`显示在表的末尾，不管信号源顺序如何**

![This tfoot element is used to show a footnote.](img/f0e74747789625ceb3f6b53421ac89f9.png)

所有行组都支持`align`和`valign`属性。`align`属性调整水平对齐，而`valign`处理垂直对齐。不要太担心这些属性，因为我们将使用 text-align 和 vertical-align 属性在 CSS 中处理它们。

**元素`tr`元素**

一个`tr`是一个表格行。行很像行组，因为它们都支持`align`和`valign`属性。表格行也有`bgcolor`属性，允许设置背景颜色。同样，我们将在 CSS 中处理这一步。

**中的`th`和`td`元素**

`th`和`td`元素是表格单元格，为`table`保存数据。表格单元格有许多属性，其中许多属性不仅从样式的角度来看很重要，而且从可访问性的角度来看也很重要。

与行和行组一样，表格单元格也有`align`和`valign`属性，以及`rowspan`和`colspan`属性。`rowspan`属性指示单元格应该有多少行高，包括当前单元格。`colspan`非常相似，与列的宽度有关——你猜对了。查看图 5，了解如何跨越列和行。

**图五。`colspan`和`rowspan`属性在起作用**

![Columns and rows span across this table](img/34086ac08a83d083868837aa77fbe647.png)

下面是生成该图的标记:

**例 3。`colspan-rowspan.html`(节选)**

```
 <table>  

  <thead>  

    <tr>  

      <th scope="col">Header</th>  

      <th scope="col">Header</th>  

      <th scope="col">Header</th>  

      <th scope="col">Header</th>  

    </tr>  

  </thead>  

     <tbody>  

       <tr>  

         <td rowspan="6">You can span down.</td>  

       </tr>  

       <tr>  

         <td colspan="3">You can span across.</td>  

       </tr>  

       <tr>  

         <td colspan="2">It's like a puzzle.</td>  

         <td rowspan="4">Over here.</td>  

       </tr>  

       <tr>  

         <td rowspan="3">This way.</td>  

       </tr>  

       <tr>  

         <td>That way.</td>  

       </tr>  

       <tr>  

         <td>Where am I?</td>  

      </tr>  

  </tbody>  

</table>
```

`th`元素还可能包含`axis`、`headers`、`scope`和`abbr`属性，每个属性都允许您创建不同单元格之间的关系。屏幕阅读器可以使用这些属性来提高读者浏览`table`的能力。由于浏览器对一些 CSS 选择器的支持，很难通过这些属性的存在来定位特定的元素，但为了完整起见，我在这里提到了它们。如果您想了解更多关于这些属性的信息，请查看 W3C CSS3 规范。

**中的`col`和`colgroup`元素**

我把最好的留到了最后！`col`用于标识一列；`colgroup`标识列组。就样式而言，这两个元素的最大好处是它们允许我们对整个列进行样式化，而不需要为列中的每个单元格添加一个`class`。

跨度可以分配给我们的`colgroup`元素和`col`元素。这种分配实际上并没有像单元格上的`rowspan`或`colspan`属性那样将多个单元格折叠成一个。它只是提供了一种指定跨多个列应用的属性的简便方法:

```
<colgroup>   

  <col />   

  <col />   

</colgroup>   

<colgroup>   

  <col />   

  <col />   

  <col />   

</colgroup>
```

这也可以这样写:

```
<colgroup span="2" />   

<colgroup>   

  <col span="2" />   

  <col />   

</colgroup>
```

`colgroup`上的`span`属性表示`colgroup`跨越两列。当`colgroup`上存在`span`属性时，不使用`col`元素。如果`col`元素确实存在于`colgroup`中，那么`colgroup`上的`span`属性将被忽略。`col`元素上的`span`属性也表明有两列。

可以使用以下三种格式之一指定`width`属性:

*   **width="100"** —以像素为单位的宽度
*   **宽度="20%"** —以百分比表示的宽度
*   **width="2*"** —相对宽度，表示单元格的宽度应该是常规单元格的两倍。(这种相对大小在 Internet Explorer 或 Opera 中不起作用，所以最好避免。)

在 Internet Explorer 中使用百分比或相对值`width`会将整个表格扩展到 100%，而 Firefox、Safari 和 Opera 会折叠到填充单元格所需的最小区域——这是预期的行为。

这里有一个例子，展示了我们刚刚讨论过的一些结构属性，包括它在 Firefox 中的显示方式(最终结果如图 6 所示):

**例 4。`growth-chart.html`(节选)**

```
<table>   

  <caption>Growth Chart</caption>   

  <col width="60%">   

  <col width="20%">   

  <col width="20%">   

  <thead>   

    <tr>   

      <th scope="col">Name</th>   

      <th scope="col">Age</th>   

      <th scope="col">Height</th>   

    </tr>   

  </thead>   

  <tfoot>   

    <tr>   

      <td colspan="3">[1] Has <a    

  href="http://en.wikipedia.org/wiki/Gigantism">   

  Gigantism</a></td>   

    </tr>   

  </tfoot>   

  <tbody>   

    <tr>   

      <th rowspan="3" align="left">Albert</th>   

      <td>1</td>   

      <td align="center">2 ft. 8 in.</td>   

    </tr>   

    <tr>   

      <td>10</td>   

      <td align="center">4 ft. 6 in.</td>   

    </tr>   

    <tr>   

      <td>20</td>   

      <td align="center">6 ft. 1 in.</td>   

    </tr>   

  </tbody>   

  <tbody>   

    <tr>   

      <th rowspan="3" align="left">Betty [1]</th>   

      <td>1</td>   

      <td align="center">2 ft. 3 in.</td>   

    </tr>   

    <tr>   

      <td>10</td>   

      <td align="center">4 ft. 2 in.</td>   

    </tr>   

    <tr>   

      <td>20</td>   

      <td align="center">7 ft. 2 in.</td>   

    </tr>   

  </tbody>   

</table>
```

**图 6。Firefox 呈现的前面的标记**

![This table shows several structural elements](img/6f8cd0872bac84ba9448db902ddd315e.png)

你已经忍受了缓慢而陡峭的上升，学会了如何创建一张桌子；快到我在这一章开始时承诺的过山车的时间了！在我们看了我们需要的 CSS 属性之后，我们将立即投入到样式中。

##### 造型

在我们深入一些实际的例子之前，理解我们实际上可以利用哪些 CSS 属性以及可以在哪里使用它们是很重要的。我们将研究特定于`table`元素、列和标题的样式。之后，我们将学习背景是如何处理的。从那以后，一切都变得有趣了——我们将通过一些例子来展示如何给桌子的科学带来一点艺术。

**使用`table`元素**

有几个属性是`table`元素独有的:

*   边界塌陷
*   边框间距
*   空单元格

border-collapse 属性的值可以是`separate`或`collapse`，如下面的图 7 所示。`separate`是默认属性，但是它创建的表格看起来相当大。使用`collapse`删除单元格之间的空间，有效地覆盖 HTML 中可能设置的任何单元格间距。这一步会让我们的桌子看起来更干净，所以这是一个好的开始。

**图七。比较边框折叠属性**的`separate`和`collapse`值

![Left, expanded - right, collapsed](img/8d663b381a57f6f9171054b1ee5f675c.png)

**折叠边框的问题**

如果两个单元格之间有边框，并且每个单元格有不同的边框样式，则折叠边框会产生问题。解释如何解决这些冲突的边界冲突解决指南可从 W3C 获得。

设置`table`的边框间距属性时，可以指定一个或两个长度值。如果只指定了一个，该值会影响所有边的间距。如果指定了两个值，第一个值指定水平间距(单元格的左侧和右侧)，第二个值指定垂直间距(单元格的上方和下方):

```
table {    

  border-spacing: 2px 5px;    

}
```

本示例在每个单元格之间添加两个像素的垂直间距和五个像素的水平间距。

空间中出现的颜色始终是表格背景的颜色。设置行或单元格背景永远不会改变单元格之间的颜色。

然而，我们的对手 Internet Explorer 不支持边框间距属性，即使在 IE7 中也是如此。这种情况留给我们的唯一办法是在 HTML 中使用`cellspacing`属性来达到与边框间距相同的效果。

empty-cells 属性有两个值:`show`和`hide`，其呈现如图 8 所示。

此属性确定空白单元格上的边框是否可见；它几乎可以应用于`table`中的任何元素，比如特定的行或单元格。然而，同样，Internet Explorer 不支持空单元格属性。

**图 8。空单元格属性的值–左边的`show`和右边的`hide`**

![Empty cells: shown and hidden](img/de7c60473bdfca9ff5d1af4a52749e57.png)

**设置列样式**

列组元素(`colgroup`)是唯一的，因为单元格实际上不从它们继承任何东西。所以*适用于`colgroup`的*只有四个属性:`border`、`background`、`width`、`visibility`。使用这些属性会导致浏览器之间的不一致，如图 9 所示——所以要做好准备！

border 属性在 Firefox 和 Safari 中运行良好。在 Opera 中，对设置了`span`属性的`col`元素应用边框，不会像在 Firefox 或 Safari 中那样对每一列应用边框。在 Internet Explorer 中，边框 CSS 属性根本不起作用。

下面是我们如何在 CSS 中设置表格边框和边框折叠:

```
table {    

  width: 400px;    

  border-collapse: collapse;    

}    

#test {    

  border: 1px solid blue;    

}
```

…并修改我们的 HTML 以禁用`border`属性:

```
 <table border="0">    

  <caption>Growth Chart</caption>    

  <col width="60%">    

  <col width="20%" id="test">    

  <col width="20%">    

  ...
```

边框折叠需要设置为`collapse`才能在 Firefox 和 Safari 中显示。

**图 9。Firefox 和 Opera 的列边框渲染对比**

![Firefox](img/6683476d38544272e208add910ba5b0b.png)

![Opera](img/1b5b4cecc7911dd89b5255210114d88a.png)

`background`属性在不同的浏览器中相当一致，但是它仍然有一些小问题。例如，应用于列组的背景图像在 Opera 中被设置为每个列的背景，但在 Safari 和 Internet Explorer 中被错误地应用于每个单独的单元格。还有分层问题，只有 Firefox 能够充分应对。如果幸运的话，你永远不会遇到这些分层问题，但是我会在“应用成功的背景”一节中更详细地讨论这些问题。

width 属性在所有测试的浏览器中都能很好地工作。请记住，当应用于`colgroup`时，宽度会影响包含在`colgroup`中的每一列的大小。例如，如果将包含两列的列组的宽度设置为 200 像素，则每列为 200 像素，列组的总宽度为 400 像素。

最后，`visibility`只是为了完整性，但是 Firefox 是目前唯一支持它的浏览器。可以将`visibility`设置为`collapse`，这样可以防止列被看到。

**格式化标题**

```
caption elements can be formatted like most other block elements, including properties like text-align and font-weight. There's an additional CSS property that can come in very handy, and that's caption-side. This property can be set to either top or bottom, which will allow the caption to appear either above or below the table respectively. Firefox takes it a step further and supports values of left or right. I hate to sound like a broken record, but good ol' Internet Explorer doesn't support caption-side.        **应用成功的背景**

既然我们讨论的是列上的背景，那就让我们更深入地研究一下表格上的背景应该如何工作。本质上，不同的元素充当层。一个级别上的任何透明度都会揭示其下一级别的背景。图 10 展示了一个 W3C 图表，演示了表格元素上背景的分层。

**图 10。W3C 的表层的[模式](https://www.w3.org/TR/REC-CSS2/tables.html#table-layers)**

![Exploded view of a table](img/6b25a64370111dff1cad471d9abba96e.png)

然而，正如你可能已经注意到的我之前提到的一些跨浏览器问题，大多数浏览器不像规范建议的那样处理背景。许多人实际上采用了在列或行级别指定的任何背景，并简单地在单元格级别应用它们。当使用有图案的背景时，这可能会证明*非常*令人沮丧——任何重复的图案都无法对齐。在单元格级别玩不透明也揭示了 Safari、Opera 和 Internet Explorer 的错误有多严重。例如，请看图 11，它展示了应用于表格行的背景。Albert 在 Firefox 中显示正确，但在 Internet Explorer 中显示时，他遇到了真正的麻烦。

**图 11。火狐和 ie 浏览器的背景对比**

![Firefox rendering of a table background image](img/0f7c549858fc38deee80dc4a4946b99a.png)

![The background image repeats improperly in IE](img/e2efe05aa7fc6ef70b1388d5c8480ff2.png)

不幸的是，表格的使用不像以前那么流行了，所以我们很可能要等很长时间才能在其他浏览器中纠正这个问题。

幸运的是，在`table`元素上应用背景确实工作一致。背景应该适当地平铺在整个表格上，并完全按照它应有的方式运行。让我们试一试这个应用程序，看看当 Albert 遇到在 Internet Explorer 中显示的严酷条件时，他能否避免被分割。

如果我们的表格和列是固定宽度的，我们实际上可以通过偏移每列的背景来解决单元格背景问题。这是一项繁琐的任务，但非常值得，所以让我们开始吧！这里有一个例子来演示这种方法:

**例 5。`background-position.html`(节选)**

```
table {    

  width: 223px;    

}    

td {    

  background: red url(images/albert.jpg) repeat 0 0;    

  height: 200px;    

}    

td.col1 {    

  width: 90px;    

}    

td.col2 {    

  background-position: -90px 0;    

  width: 43px;    

}    

td.col3 {    

  background-position: -133px 0;    

  width: 90px;    

}
```

第一列之后的每一列都简单地移动了前一个单元格的宽度。第一列不需要移动，而第二列需要移动第一列的宽度。最后，第三列背景必须在前两列的总宽度上移动。图 12 再次显示了 Internet Explorer 中的 Albert，但是每个单元格内的背景都发生了变化——他开心多了。
**图 12。已解析的 Internet Explorer 示例**
![Background shift resolved!](img/8ec18193f921d0c07f3ff67937975b5f.png)
好了，就这些了，伙计们。至少目前是这样。对列、标题和背景样式的理解为我们乘坐过山车做好了准备——现在是时候让我们看看我们所学的所有样式的一些实际应用了！

##### 表格元素的作用

所有的细节都说完了，让我们来看看一些表格的例子——通过仔细应用我们所学的样式，我们能把它们做得多漂亮呢？

**给表格日历添加样式**

日历喜欢桌子。事实上，这两者是天造地设的一对，因为日历将星期很好地适应于一系列行，将日子适应于列。这里有一个完全不加修饰的`table`:

**图 13。无样式日历**

![A basic, unstyled version of the calendar](img/93b0f5b15c0e9b570da1af3bc3b59fe1.png)

这是一张不错且功能完善的桌子，只是有点沉闷；我需要宣传我的生日，这也让我失去了一点平衡。让我们来看一下标记，并考虑一下怎样才能让这个表格更有风格一些:

**例 6。`calendar.html`(节选)**

```
<table>     

  <caption>June</caption>     

  <col class="weekend" />     

  <col class="weekday" span="5" />     

  <col class="weekend" />     

  <thead>     

    <tr>     

      <th>Sun</th>     

      <th>Mon</th>     

      <th>Tue</th>     

      <th>Wed</th>     

      <th>Thu</th>     

      <th>Fri</th>     

      <th>Sat</th>     

    </tr>     

  </thead>     

  <tbody>     

  <tr>     

    <td><div class="day">1</div></td>     

    <td><div class="day">2</div></td>     

    <td><div class="day">3</div></td>     

    <td><div class="day">4</div></td>     

    <td><div class="day">5</div></td>     

    <td class="birthday"><div class="day">6</div>     

      <div class="notes">It's my birthday!</div></td>     

    <td><div class="day">7</div></td>     

  </tr>     

  ...     

  </tbody>     

</table>
```

我们将在每一天的数字周围添加一个`div`。这允许将额外的项目添加到一天中，并让我们可以灵活地设计数字本身。更多的通用样式，比如假日，被应用到表格单元格中——让我们为我的生日应用一个合适的突出样式！

为了让它看起来更像一个日历，我们可以设置一些样式。我们将设计一个更大的`caption`，使月份更加突出。每一天都有一个高度和宽度，允许添加注释。周末栏目的设置是为了从工作日中脱颖而出，我们可以将节假日和生日指定为特殊的日子。

这是我们日历表的 CSS:

**例 7。`calendar.html`(节选)**

```
table {     

  border: 1px solid #999;     

  border-collapse: collapse;     

  font-family: Georgia, Times, serif;     

}     

th {     

  border: 1px solid #999;     

  font-size: 70%;     

  text-transform: uppercase;     

}     

td {     

  border: 1px solid #999;     

  height: 5em;     

  width: 5em;     

  padding: 5px;     

  vertical-align: top;     

}     

caption {     

  font-size: 300%;     

  font-style: italic;     

}     

.day {     

  text-align: right;     

}     

.notes {     

  font-family: Arial, Helvetica, sans-serif;     

  font-size: 80%;     

  text-align: right;     

  padding-left: 20px;     

}     

.birthday {     

  background-color: #ECE;     

}     

.weekend {     

  background-color: #F3F3F3;     

}
```

一旦我们将修改后的标记和样式表结合起来，我们就会得到一个更加美观的日历。

**图 14。风格日历**

![The styled calendar](img/b19d48429860a1318eff35659d43513c.png)

**条带化表格行**

*条带化*，也称为*斑马表*，对大型单调数据集很有用，因为它有助于提高可读性。太多没有描绘的文本会使人很难看出特定行中一列与另一列在哪里对齐。

要实现条带化，我们只需在每隔一行添加一个`class`(查看本章末尾的“未来”一节，了解一种更酷的、尽管支持较少的条带化方法):

```
...     

<tr>     

  <td>...</td>     

  <td>...</td>     

  <td>...</td>     

</tr>         

<tr class="even">     

  <td>...</td>     

  <td>...</td>     

  <td>...</td>     

</tr>     

...
```

我们的基本样式将被应用到普通的`tr`中，然后替换样式将被应用到具有`even`的`class`的`tr`元素中:

**例 8。`striped.html`(节选)**

```
tr {     

  background-color: #FEE;     

}     

tr.even {     

  background-color: #EEF;     

}
```

在本例中，奇数行将是淡红色，偶数行将是淡蓝色，如下所示:

**图 15。不同的背景色交替排列，使表格更加易读**

![Basic stripes](img/b0ff9ea2d5d81b1bb5ed736e8065d346.png)

在对表进行条带化时，我经常选择的另一个选项是使用半透明的 PNG 作为替代行的背景图像，如下面的图 16 所示。走这条路让我可以改变`table`上的背景颜色(或背景图像),而不必担心尺寸或颜色匹配问题:

**例 9。`striped-png.html`(节选)**

```
tbody tr.odd td {     

  background: transparent url(images/tr_bg.png) repeat top left;     

}
```

**图 16。使用半透明 PNG 对交替行进行条带化**

![Using a PNG for soft stripes](img/826adba008bb51c1de05b36a0e36d856.png)

PNG 发音为“ping”，是一种图像格式类型，就像 GIF 或 JPG 一样，但它也支持分级透明。换句话说，背景可以通过图像的一部分变得部分可见，就像透过有雾的玻璃窗看一样。相反，GIF 只支持没有不透明级别的索引透明度——要么打开，要么关闭。JPG 根本不支持透明。大多数图形软件，如 Adobe Fireworks、GIMP 或 Adobe Photoshop，都会很乐意导出 png，所以这是一种非常有用的图像格式。

我通常导出的 PNG 是白色的，透明度设置为 15%到 25%,因为这提供了一个半透明的覆盖，几乎可以在任何配色方案中使用。

正如在*CSS 的艺术与科学*的第 2 章中所讨论的，Internet Explorer 在版本 7 之前不支持 PNG 背景图像。我们将再次通过使用 Internet Explorer 专有的条件注释来解决这个问题:

**例 10。`striped-ong.html`(节选)**

```
<!--[if lt IE 7]>     

<style type="text/css" media="screen">     

tr.even {     

  background: none;     

  filter: progid:DXImageTransform.Microsoft.AlphaImageLoader     

    (src='images/tr_bg.png', sizingMethod='scale');     

  }     

</style>     

<![endif]-->
```

“但是，等一下，”你问。"为什么使用 PNG 而不是不透明 CSS 属性？"嗯，在 CSS 中设置不透明度似乎是实现这种效果的显而易见的选择；问题是，它将不透明度应用于包含在其中的所有元素。因此，所有文本和图像也将是透明的。在元素上设置不透明度需要一些额外的技巧来使它与 Internet Explorer 兼容，并导致您的 CSS 无效——除非通过 IE 条件注释设置专有的 filter 属性:

```
td {     

  opacity: 0.2;     

  filter: alpha(opacity=20);     

}
```

Safari、Firefox 和 Opera 支持不透明属性，它是 [CSS 3 规范](https://www.w3.org/TR/css3-color/#opacity)的一部分。为了在 Internet Explorer 中实现相同的结果，您必须使用专有的 filter 属性，该属性允许您指定一个`alpha`过滤器。

你已经和我一起读到这一章了，所以是时候告诉你我的一个小秘密了。我经常使用的一个技巧是将一个图像应用到`table`背景，最终淡入纯色:

```
table {     

  width: 450px;     

  color: #FFF;     

  background: #333 url(images/table_bg.png) no-repeat top left;     

  border-collapse: collapse;     

  border: 8px solid #9C0;     

}
```

这个小练习可以给你的表格带来一些疯狂的灵感，同时仍然是一个非常可靠的跨浏览器解决方案。使用这种效果，结合阿尔法 png，可以让您创建一些非常性感的表格！

背景图像设置在`table`上，正如我们在“应用成功的背景”一节中所提到的，它在所有浏览器中都是可靠的。该图像专门设计为在底部淡出为纯色，如下图所示。在本例中，我们渐变为纯灰色。

**图 17。将 alpha-PNG 行条纹与渐变背景相结合**

![Striped table](img/02bae8969c1b045245c1f88235f9eed3.png)

所以，这就是——我相信你会同意，明智地应用一点 CSS 可以使最无聊的日历变成一个值得墙壁规划者使用的布局，其样式允许创造性的自由。同样，条带化是一个非常简单的应用程序，但它产生的效果非常好，可以广泛用于许多表格应用程序中，为最普通的内容增添色彩。然而，事情并没有就此结束。

##### 使用 JavaScript

正如您到目前为止所看到的，CSS 为我们的表格增添了一些性感的味道。真正能让我们的表格崩溃的是一些不错的 JavaScript！

如果您已经使用 web 标准开发了一段时间，那么您很可能会遇到这样的口头禅:“将您的演示与您的内容分开。”这个网络标准轮子有第三个辐条，那就是*行为*。行为最好通过*不引人注目的 JavaScript* 来处理。不引人注目的 JavaScript 是让你的脚本驻留在一个外部文件中(就像一个样式表一样),这个文件把它自己和你的 HTML 文档联系起来。

使用不引人注目的 JavaScript 可以保持 HTML 的整洁和易于访问，即使对于那些没有 JavaScript 或者关闭了 JavaScript 的用户也是如此。对于这些用户来说，内容本身仍然是可用的和可访问的，毕竟他们是少数；同时，那些打开了 JavaScript 的用户将能够利用您启用的附加功能。

那么，JavaScript 如何美化我们的表格呢？

**行列高亮显示**

一个常见的功能是添加对 Internet Explorer 6(及更低版本)的行突出显示支持。我们还可以更进一步，为所有浏览器添加列高亮显示。

如果您不喜欢 JavaScript，并且这个示例中的代码没有多大意义，那也没关系。如果你对学习 JavaScript 感兴趣，我建议你拿一本 SitePoint 的书*[《JavaScript 选集:101 个基本技巧、诀窍、&黑客](https://www.sitepoint.com/books/jsant1/)* 》，这是这一领域的必备教材。

让我们定义一个在页面加载时运行的函数。考虑一下我们的逻辑，我们希望这个函数在用户将鼠标移到桌子上的任何时候运行。更具体地说，当鼠标在特定的单元格上时，它应该改变该行和该列的背景。

我们需要做的第一件事是获取`table`元素并将其传递给我们的`highlight`函数。

**例 11。`scripts/highlight.js`(节选)**

```
window.onload = function()     

  {     

    var tbl = document.getElementById('mytable');     

    setHighlight(tbl);     

  }          
```

我使用了`window.onload`，这是一种非常快捷的方式来说明这个代码块应该在`window`完成加载后运行。现在，让我们看看`setHighlight`函数是什么样子的。

**例 12。`scripts/highlight.js`(节选)**

```
 function setHighlight(table)      

  {     

    if (!table) return;     

    var TDs = table.getElementsByTagName("td");     

    for(var i = 0; i<TDs.length; i++) {     

      TDs[i].onmouseover = rowColHighlight;     

      TDs[i].onmouseout = rowColDelight;     

    }     

  }
```

如果一个元素没有传递给函数，我们的`highlight`函数将返回到它的原点。如果我们有一个元素，它将吸引我们的`table`中的所有表格单元格。它循环遍历它们，并给每一个附加两个事件。当用户将鼠标移到单元格上时，`rowColHighlight`将负责高亮显示行和列，当用户将鼠标移出单元格时，`rowColDelight`将负责取消高亮显示。

**例 13。`scripts/highlight.js`(节选)**

```
function rowColHighlight()     

  {     

    highlighter(this, '#EEE');     

  }     

  function rowColDelight()     

  {     

    highlighter(this, '');     

  }
```

我们的两个函数只是调用另一个函数，但是传递两个变量。首先是要突出显示的元素。`this`关键字指的是触发事件的元素——在我们的例子中，是单元格。第二个变量是我们想要的`highlighter`的颜色。

`highlighter`功能是我们的肉和土豆:

**例 14。`scripts/highlight.js`(节选)**

```
function highlighter(cell, color)     

  {     

    cell.parentNode.style.backgroundColor = color;     

    var table = getTable(cell);     

    var col = table.getElementsByTagName("col");     

    col[cell.cellIndex].style.backgroundColor = color;     

  } 
```

首先，从单元格中，我们告诉它获取`parentNode`(我的单元格周围的行元素)并将背景色更改为传入的颜色。然后，我们告诉它获取包围单元格的`table`。根据 HTML 的设置方式，检索`table`元素可能有点复杂，所以我们创建了另一个函数来处理这个问题。我稍后将再次谈到这一点。

一旦我们有了`table`元素，我们获取表中所有的`col`元素，然后获取与单元格所在的列相匹配的元素。`cellIndex`属性是到当前单元格的列数。一旦我们有了正确的列，我们就给它分配一个样式。只要没有在其他单元格、行或行组上指定背景，这种样式就应该起作用。

回到我之前跳过的`getTable`函数:

**例 15。`scripts/highlight.js`(节选)**

```
function getTable(obj)     

  {     

    while (obj &amp;&amp; obj.tagName.toLowerCase() != 'table')     

      {     

        obj = getTable(obj.parentNode);     

      }     

      return obj;     

    }          
```

这个函数获取当前元素并检查它是否是`table`元素。如果不是，那么函数获取父元素并检查它。这个检查过程将继续，直到函数找到`table`元素或根本没有元素。一旦找到了`table`，就返回那个`table`对象。

下图描述了我们的高亮功能。

**图 18。与大多数浏览器兼容的行和列高亮显示**

![Highlighted rows](img/58a294bedf61d8849ede5b6514d23e22.png)

我应该指出，这个脚本做了许多巨大的假设。为了使您的代码更加可靠，您应该提供检查机制来考虑不同的情况。例如，我们在这里做的一个假设是，一行中有多少个单元格，就有多少个`col`元素。如果您在其他`col`元素上使用了`colgroup`元素或`span`属性，情况可能就不一样了。如果这些假设中的任何一个不正确，您肯定会看到 JavaScript 错误。

**其他想法**

JavaScript 的另一个常见职责是*表排序*。表格排序对于用户来说是一个非常方便的工具，允许他们操作表格视图，而不需要从服务器进行缓慢而重复的页面刷新。在谷歌上快速搜索“表格排序”会得到许多脚本，我已经成功地使用了斯图尔特·朗里奇的“排序表脚本”[。](http://kryogenix.org/code/browser/sorttable/)

在 Ajax 的冲击下——通过 JavaScript 连接到服务器并从服务器发送和接收数据的能力——你甚至可以提供像 T2 一样的电子表格功能，包括实时编辑。一个很好的例子，看看[活动小部件网格组件。](http://www.activewidgets.com/grid/)说到新锐的东西，对了，CSS 3 怎么样？让我们来推测一下这个即将到来的标准将如何彻底改变我们设计表格的方式。

##### 未来

设计我们的表很容易，但是您可能会发现有些是多余的，比如对每隔一行应用一个`class`来创建一个条纹表。幸运的是，在新的 CSS 3 规范草案中有许多有用的选择器，它们将大大简化我们的生活。

**浏览器支持难题**

一些浏览器开发者，比如 Firefox 和 Opera 的开发者，一直在努力，试图在 CSS 3 规范中加入对许多有用东西的早期支持。然而，Internet Explorer 落后了，惊喜，惊喜。不幸的是，这一事实意味着 IE 仍然是大多数网络用户的首选浏览器，CSS 3 支持特性的广泛采用可能会受到限制。

在设计表格样式时，可能最令人兴奋和最有用的选择器是子伪选择器，其中有很多。

`:nth-child(an+b)`选择器允许你选择每第 *n* 个元素。本质上，`a`划分元素集合，`b`是偏移量。还记得我们的条纹桌子吗？以下是如何用不同的颜色来设计每隔一行的样式:

```
tbody:nth-child(2n) { ... } /* even rows */      

tbody:nth-child(2n+1) { ... } /* odd rows */
```

或者，您可以使用`:nth-of-type(an+b)`选择器来完成同样的事情:

```
tr:nth-of-type(2n) { ... }      

tr:nth-of-type(2n+1) { ... }
```

同样，如果您需要用不同的颜色为每隔一列设置样式，您可以将样式应用于每隔一个`td`元素:

```
tr:nth-child(2n) { ... }      

td:nth-of-type(2n) { ... } 
```

通过为`a`提供一个值`0`，偏移量允许你选择第`n`个元素。例如，如果您只想设置第五列的样式:

```
tr:nth-child(0n+5) { ... }      

td:nth-of-type(0n+5) { ... }
```

`nth-child`和`nth-of-type`非常相似，但是在设计你的表格时会给你很好的控制。

IE 隧道的尽头也有一丝曙光。最近发布的 Internet Explorer 7 支持许多新的选择器，包括`:first-child`和允许我们模仿`nth-child`的兄弟选择器。同级选择器使用加号(`+`)来定位元素。因此，如果您想设计左边第二列的样式，如图 19 所示，您可以使用下面的代码:

```
td:first-child + td {      

  background-color:#036;      

}
```

**图 19。使用`td:first-child+td`改变`background-color`和**

![The first-child selector causing a highlight effect](img/add5d15ce02e20f78e9f6647b0c70757.png)

`td:first-child`将指向一行中的第一个单元格，然后同级选择器(`+`符号)指向它旁边的元素。如果您想以第四列为目标，您可以使用以下内容:

```
td:first-child + td + td + td {      

  background-color:#036;      

}
```

假设有一个表，其中有许多值，您希望最后一列加粗，以表示该数据是一个总和。使用`:last-child`选择器就可以做到:

```
td:last-child { ... }
```

利用`:first-child`和`:last-child`，你可以扩展我们之前看到的条纹`table`，为页眉和页脚的第一个和最后一个单元格添加圆角。边框是设置为每行中第一个和最后一个单元格的背景图像的图像。

**图 20。使用 CSS 3 选择器给`table`** 添加圆角

![With rounded corners added](img/91375804ed5fca3481a9b651c21ec999.png)

在不久的将来，你可以期待看到更多的选择器。虽然我们可能还需要几年时间才能在所有流行的浏览器中使用这些功能，但做梦也无妨。关于 CSS 3 选择器的更多信息，请查阅 W3C CSS 3 规范的相关章节。

##### 摘要

我希望你在读完这一章后，对表格有了新的认识。幸运的话，我已经向你展示了一些你以前不知道的元素。

我们发现了如何创建一个完美的语义数据表，为我们的 CSS 提供许多钩子。我们已经建立了一个结构良好的`table`并学会了有效地设计它。we；我了解到，赋予表格某种样式实际上会使我们的表格更有用，更容易阅读和理解其中包含的数据。

我们已经看到了 JavaScript 如何注入一点个性和一些额外的可用性，而不会给没有 JavaScript 的用户造成困难。希望您已经获得了一些关于如何以新的有用的方式在表上实现 JavaScript 的有价值的想法。

我们已经看到了未来，它是光明的！我们已经预料到 CSS 3 的新特性将如何为我们提供简单的方法来使我们的表格看起来更好，现在我们有了使用它们的知识。

如果你喜欢 [*的这一章，CSS *](https://www.sitepoint.com/books/cssdesign1/) 的艺术&科学，为什么不注册免费接收整本书的 PDF 格式呢？这个“twit away”(Twitter 赠品)将只运行 14 天，所以赶快加入吧！只要在 Twitter 上关注 [@sitepointdotcom](http://twitter.com/sitepointdotcom) ，你就会收到免费的 PDF，没有任何附加条件。[立即获取您的副本！](http://twitaway.com/)

## 分享这篇文章

```