# 在 CSS 布局中轻击浮动

> 原文：<https://www.sitepoint.com/give-floats-the-flick-in-css-layouts/>

如果你是 CSS 布局的新手，你会认为以富有想象力的方式使用 CSS 浮动是技巧的顶峰。如果你已经阅读了尽可能多的 CSS 布局教程，你可能会认为掌握浮动是一种成年礼。当你最终理解了浮动是如何工作的时候，你会被它的独创性弄得眼花缭乱，被它的复杂性弄得目瞪口呆，你会有一种成就感。

不要被骗了。你被洗脑了。

**危险信号**

我在一个 CSS 重置文件中发现了这条规则:

```
address, blockquote, center, dir, div, fieldset, pre,
dd, dt, frameset,li, h1, h2, h3, h4, h5, h6, dl,
ol, ul, form, table {
  float: left;
}
```

这是 CSS 作者是 one true layout 的追随者的标志；相信彩车魔力的人。追随者相信他们已经找到了唯一需要的布局技术。随处可见的 CSS 框架，使用其中的一个就相当于决定你永远不需要学习另一个 CSS 布局技术。

事实是，在六年前，在当时有限的 CSS 支持下，使用 floats 进行布局是最好的组合。现在是 2011 年，浏览器 CSS 功能突飞猛进，而流动人群仍然像 2005 年一样编码 CSS。浮动布局正在扼杀 CSS 创新。

依赖浮动元素的 CSS 布局过于复杂，脆弱，需要曲折的思考，产生大量需要无意义规则的奇怪问题，对初学者不友好，而且在大多数情况下，根本不需要。

具有讽刺意味的 Clearfix

浮动布局的最大问题是需要清除浮动。当您有一堆浮动元素时，它们会从文档流中取出，并且它们的容器元素会折叠。如果你想让容器恰当地包围它的浮动子容器(我称之为浮动子容器),你需要清除这些浮动子容器。

实现这一点的方法多种多样，取决于具体的布局要求。如果在一个包含元素中有一组 float，并且需要该元素扩展到全高，那么也让容器浮动。然而，最流行的技术是应用 clearfix:添加一个拒绝屈服于浮动元素的元素。

最初的 clearfix 是用规则`clear: both;`在所有 floaters 之后添加一个 block 元素，如下所示:

```
<div></div>
```

```
.clearfix { clear: both; }
```

为了没有真正的语义目的而添加结构化标记的需求非常恼人，足以激励人们寻求另一种解决方案。

这就是 clear fix 技术的高度:

```
.clearfix:before, .clearfix:after { content: "020"; display: block; height: 0; overflow: hidden; }

.clearfix:after { clear: both; }

.clearfix { zoom: 1; }
```

您只需将类`clearfix`添加到容器元素中，然后让奇迹发生。它是如此的进步，甚至被包含在 [HTML5 样板工程](http://html5boilerplate.com/)中。

我觉得有点讽刺的是，唯一支持这种方法的浏览器——支持`:before`和`:after`伪元素和生成的内容——支持几乎所有的 CSS2.1 规范(加上一些 CSS3 规范),因此不需要像浮动布局这样的杂牌。我们这里说的是 IE8 及以上级别的浏览器。

**真题**

十年来，布局的唯一选择是内联或块显示，浮动或绝对定位。创建布局实际上是许多小而简单的布局任务的组合。通过使用浮动来定位元素，这些任务经常变得不必要的复杂。解决这种引入的复杂性所需的独创性已经成为 CSS 实力的基准。

为什么不停止使用浮动，而是去掉复杂性呢？

这里有一个 CSS 属性和值的集合，这些属性和值在过去由于无聊的兼容性问题而被避免，但是现在得到了足够广泛的支持，我们可以开始在我们的 CSS 布局中使用它们。在这些例子中，没有任何一个是浮动的，我希望通过探索这些可能性，我可以说服你在自动使用基于浮动的组装工具之前停下来思考一下。

**显示:inline-block；**

`Inline-block`是 display 属性的一个值，它允许您一致地应用像`margin`、`padding`、`width`、`height`和`vertical-align`这样的属性，同时保持一个内联元素。这在任何想要水平对齐行中的块元素的情况下都非常有用。

任何地方的博客的支柱都是使用无序列表作为导航菜单。水平旋转列表通常需要你向左或向右浮动所有的菜单项，然后处理包含元素的高度，以及任何清除问题。通过将`display: inline-block;`应用于所有的 li 元素，所有这些问题都消失了。如果你想让锚元素填满空间，对它们应用`display: block;`。

下面是一些简单菜单的 HTML 示例:

```
<div class="nav">
  <ul>
    <li><a href="home">Home</a></li>
    <li><a href="about">About</a></li>
    <li><a href="news">Latest News</a></li>
    <li><a href="meals">Kitten Meals</a></li>
    <li><a href="pelts">kitten Pelts</a></li>
    <li><a href="dioramas">kitten Dioramas</a></li>
    <li><a href="search">Search</a></li>
  </ul>
</div>
```

接下来，我们应用同样简单的 CSS:

```
.nav > ul > li {
     display: inline-block;
}
.nav > ul > li a:link, .nav > ul > li a:visited {
    display: block;
}
```

添加一些装饰风格后，我们有一个水平菜单:

![inline-block-nav](img/c32d44cb789ae615a2c0e2b733dc6c7b.png)

您可能已经发现了列表标记不再可见的事实。这是因为默认情况下，列表项的`display` 值为`list-item`；更改`display`值也删除了列表标记。

`inline-block`元素也将响应`vertical-align`属性。因此，如果你有不同尺寸的元素，你可以创建不同的效果，如下所示:

![inline-block-nav2](img/784723ea5d9f6d512917b4a0580b7ac6.png)

顶部菜单使用`vertical-align: middle;`，底部菜单使用`vertical-align: bottom;`。

这是另一个使用`inline-block`创建 2D 元素网格的例子。我喜欢以这种方式使用`inline-block`的原因是，这些元素可以很好地适应不同的浏览器宽度。HTML 和 CSS 也很简单:

```
<ul>
  <li class="product"><img src="..."><p>$5.99kg</p></li>
  <li class="product"><img src="..."><p>$2.99kg</p></li>
  <li class="product"><img src="..."><p>$15.99kg</p></li>
  <li class="product"><img src="..."><p>$3.99kg</p></li>
  <li class="product"><img src="..."><p>Fresh baked</p></li>
  <li class="product"><img src="..."><p>Ready to eat</p></li>
  <li class="product"><img src="..."><p>Taste in store</p></li>
  <li class="product"><img src="..."><p>Delicious with cranberries</p></li>
</ul>
```

```
li.product {
  display: inline-block;
  ...
}
```

加上一些装饰风格和适当的宽度，效果就完整了:

![inline-block-grid](img/028c77a6350086c421abf6a2c6c180cf.png)

如果使用媒体查询，可以确保将包含元素的宽度设置为最佳值，以避免最后一行出现任何孤立元素。就像铺瓷砖一样。如果您想看看实际操作，请查看[代码档案](https://github.com/spbooks/Give-Floats-the-Flick-in-CSS-Layouts-Examples/zipball/master)中的示例。

`Inline-block`也有助于表格的展示。这是一个无需使用结构化标记就能给`labels`应用一致宽度的好方法:

```
<fieldset>
  <legend>Your Details</legend>
  <label for="name">Full Name:</label><input type="text" value="" />
  <label for="address">Address:</label><input type="text" name="address" value="" />
  <label for="email">Email:</label><input type="text" name="email" value="" />
</fieldset>
```

将`display: inline-block;`应用于 `label`和`input`元素，将适当的宽度应用于`fieldset`元素:

```
label {
  display: inline-block;
  width: 10em;
}

input[type="text"] {
  display: inline-block;
  width: 20em;
}

fieldset {
  width: 30em;
}
```

结果是一个格式良好的表单:

![inline-block-form](img/fabb85b6408f4f24ef501ef31c50b12e.png)

如果您将`display: inline-block;`应用到多个`fieldset`元素，您可以将它们水平对齐，如下所示:

![inline-block-form2](img/00b64e8453df5dc1404d50c68da324fa.png)

**框尺寸:边框框**

`Box-sizing`实际上是一个 CSS3 属性，但是除了 IE6 和 7 之外的所有浏览器都支持。通过将`border-box`的`box-sizing`值应用于块元素，浏览器将把填充和边框宽度包括到元素的总宽度中；在此过程中缩小内容空间。这正是 IE5 过去的工作方式。现在计算元素宽度(元素宽度、填充、边框宽度和边距的总和)的标准方法的问题是，您不能指定 100%的宽度，然后应用填充或边框，因为这将使元素宽度增加超过 100%。`Box-sizing: border-box;`就是答案。

您可以愉快地将 100%的宽度应用于一个元素(或者两个或更多相邻元素的宽度总和为 100%)，还可以添加填充和边框，而不用担心元素会增长超过 100%。对于流畅的布局非常方便。

重要提示:根据浏览器版本的不同，您可能需要为基于 Gecko 的浏览器使用特定于供应商的前缀`-moz-box-sizing` ，为基于 webkit 的浏览器使用`-webkit-box-sizing`。Firefox 3.6 和 Chrome 10 似乎支持不带供应商前缀的属性，但 Safari 5 仍然需要它。

**最小高度**

这里有一个常见的布局情况:两列；一个用于主要内容，一个用于补充内容。HTML 可能看起来像这样:

```
<div class="wrapper">
  <div class="content">
    ... main content here
  </div>
  <div class="nav">
    ... navigation menu and complementary content here
  </div>
</div>
```

然后我们应用下面的 CSS 来排列这些列。

```
.wrapper {
  padding: 2em;
  max-width:  980px;
  margin: 0 auto;
  position: relative;
  min-height: 40em;
}
.nav {
  position: absolute;
  width: 150px;
  top: 2em;
  background-color: #FF6A00;
}
.content {
  margin-left: 200px;
}
```

内容栏是全幅的，但是我们在左侧增加了额外的空白来容纳菜单。然后使用绝对或固定定位来定位菜单。过去，这种方法的主要缺点是辅助柱比主柱长。

因为补充列被绝对定位在文档流之外，所以容器元素将折叠到较短内容列的高度。在这个广泛兼容`min-height`属性的时代，我们可以很容易地避免这个问题，在包含元素上应用一个`min-height`值；足以确保它包围互补列。

结果如下:

![position-demo](img/87f0b56dfae8185dfd18252b3a3f4a05.png)

如果你想要的列在另一边，不需要调整标记，只需调整 CSS 值。

```
.nav {
  position: absolute;
  width: 150px;
  top: 2em;
  right: 2em;
  background-color: #FF6A00;
}
.content {
  margin-right: 200px;
}
```

其他对布局有用但传统上被避免的 CSS 属性包括`position: fixed;`、`max-width`、`min-width`和`max-height`。您应该再看看这些属性，因为现在浏览器支持非常广泛。

例如，如果为补充列选择固定位置，它将固定在视窗上，而页面的其余部分滚动。你可以在 simplebits.com 身上看到这种效果。

**显示:表格**

随着 IE8 的到来，所有浏览器都支持`display`属性的表相关值。这允许我们将表格、行和单元格的布局行为应用于任何 HTML 元素。对于对齐列或行，这些`display`值是快速简单的解决方案。

人们通常会很快抵制与 CSS 布局相提并论的任何与表格相关的东西。事实上，一些网络专业人士对使用`display: table;`的建议感到非常愤怒，你会认为我们在杀害小猫。

基于表格的`display`值与使用 HTML 表格进行布局无关。辩论来了又去。他们现在很无聊。我们继续吧。

前面所有使用`display: inline-block;`的例子也可以使用`display: table;`和相关值来实现。这又是我们的导航菜单，但这次每个菜单项都像表格单元一样显示，而不是内嵌块。

![table-nav](img/8f9d856dab4b55415e15667458b752d3.png)

HTML 是一样的，CSS 只需要几处简单的修改:

```
.nav > ul {
  display: table;
  border-spacing: 0.25em;
}
.nav > ul > li {
  display: table-cell;
}
```

您可能会注意到，没有元素扮演表行的角色。没有必要:浏览器使用匿名元素来弥补表结构中缺失的部分。您可能还会注意到，因为我们将表格显示行为应用于`ul`元素，所以我们也可以应用与表格相关的样式属性，如`border-spacing`。

我们的新菜单栏与其基于`inline-block`的表亲有一个很大的行为差异。因为它就像一个只有一行的表格，所以那些菜单项不再换行。相反，如果浏览器窗口变窄，它们会聚集在一起——每个单元格的宽度都会像 HTML 表格一样缩小。在选择你的布局方法时，值得牢记在心；有时您可能希望元素换行，有时不希望。选择权在你。

如果我们想要制作一个固定的、等距的元素网格，CSS 表格就能帮上忙。这是对前面的网格示例的修改，只是这次我们需要调整 HTML。我们将使用多个`ul`元素，每个元素将成为一个表格行:

```
<div class="content">
  <ul>
    <li class="product"><img src="..."><p>$5.99kg</p></li>
    <li class="product"><img src="..."><p>$2.99kg</p></li>
    <li class="product"><img src="..."><p>$15.99kg</p></li>
    <li class="product"><img src="..."><p>$3.99kg</p></li>
  </ul>
  <ul>
    <li class="product"><img src="..."><p>Fresh baked</p></li>
    <li class="product"><img src="..."><p>Ready to eat</p></li>
    <li class="product"><img src="..."><p>Taste in store</p></li>
    <li class="product"><img src="..."><p>Delicious with cranberries</p></li>
  </ul>
</div>
```

这是 CSS:

```
.content {
  display: table;
  border-spacing: 1em;
}
.content > ul {
  display: table-row;
}
li.product {
  display: table-cell;
  width:  170px;
}
```

这是你的完美网格:

![table-grid](img/bdd80aced061f47f5914a3bac3724ef0.png)

当然，使用 display: table 实现我们的两列布局是微不足道的；：

```
.nav {
  display: table-cell;
  width: 150px;
  padding-right: 50px;
}
.content {
  display: table-cell;
}
```

高度相等的列，不需要指定一个`min-height`值，最大限度地减少了麻烦。当然，要调整补充列的位置，我们必须改变 HTML 源代码中的顺序。这是你在选择使用`display: table;`时必须考虑的一个小缺点。

当您想要在列之间包含垂直边框时，等高列特别有用。在这种情况下使用`display: table;`有一个额外的好处:属性`border-collapse`。举个例子，你可以在 WordPress 支持的网站上找到一个典型的三栏页脚:

```
<ul>
  <li class="widget-container">
    ...footer column content
  </li>
  <li class="widget-container">
    ...footer column content
  </li>
  <li class="widget-container">
    ...footer column content
  </li>
</ul>
```

这是最终结果。高度相等的列，列上有单个像素的垂直边框。

![table-footer](img/5d9429defb3aba358dab5e9f97299959.png)

CSS 也很简单:

```
.footer {
  display: table;
  border-collapse: collapse;
  width: 100%;
}
.footer > li {
  display: table-cell;
  width: 33.3%;
  padding: 1em;
  border-left: 1px solid #FF6A00;
  border-right: 1px solid #FF6A00;
}
```

一个小警告:必须在添加了`display: table;`属性的元素上设置`border-collapse`属性。

最后，这里有一个技巧 CSS 表格可以毫不费力地执行，其他技术很难做到:垂直对齐。这是我们想要达到的目标。块状元素的完美对中:

![table-valign](img/2731f875903af8adfeef6cb71cf65521.png)

我们需要一个元素作为表格，一个元素作为单个单元格。在这个例子中，我们将选择`html`和`body`元素，以使`div`元素居中。剩下的就简单了:

```
html {
  width: 100%;
  height:  100%;
  display: table;
}
body {
  display: table-cell;
  vertical-align: middle;
}
body > div {
  width: 300px;
  margin: 0 auto;
}
```

浮动不是有好处的吗？

浮动布局的辩护者很快指出了他们技术的优势。以下是经常提到的好处:

**源顺序独立性**

正如我上面提到的，当使用`display: table;`时，依赖于 HTML 源文件的顺序是一种常见的批评。但是我质疑浮动布局确实独立于源顺序的暗示，它们是实现源顺序独立的唯一方法的信念，以及源顺序独立甚至很重要的概念。

首先让我们澄清一些事情:纯 CSS 布局纯粹是虚构的。当前所有的 CSS 布局方法都在一定程度上依赖于文档流。CSS 缺乏一种方法来描述一个完全独立于标记顺序的布局，尽管它可能在不久的将来得到一个。

声称源顺序独立通常只意味着您可以对列进行重新排序。通过一个简单的 CSS 变化，左变成右，右变成左。页眉、页脚和列中的内容通常仍按显示顺序排列。但是，这种列切换功能真正投入使用的频率有多高呢？这种能力只有在特定的情况下才有可能，标记和 CSS 必须在构建时考虑到这种能力。如果这种能力实际上是因为特定的原因而需要的，那么所有的麻烦都是值得的。如果你只是用它来证明可以做到，那么所有增加的复杂性和脆性值得稍微降低对源顺序的依赖吗？

不考虑源顺序定位元素的唯一真正的方法是使用`position`值`fixed`或`absolute`。上面的`min-height`布局演示就是一个例子。此外，如果您使用`display: inline-block;`来水平对齐块元素，您可以使用正负`margin`值的组合来可视地重新排序元素，而不改变源顺序。

如果你仍然不相信源顺序问题是一个转移视线的问题，考虑一下，正如这篇 [465 Berea St 文章](http://www.456bereastreet.com/archive/201103/source_order_and_display_order_should_match/)指出的，网页内容可访问性指南明确建议 [HTML 源顺序应该与显示顺序相匹配](https://www.w3.org/TR/WCAG20-TECHS/C27.html)。还要考虑到 HTML5 的兴起使得源顺序问题成为一个争论点。HTML5 明确地提供了语义元素。它们在源代码中的放置顺序对内容的索引没有影响。`article`元素将包含文章内容，`header`元素将包含标题内容，`aside`元素将包含补充内容。

**浏览器兼容性**

IE 6 和 ie7 的兼容性经常被用作束缚新布局技术的枷锁。如果你仍然坚持认为一个样式表应该在任何地方、任何浏览器上都能工作，那么响应式设计的概念应该彻底摧毁这种观念。有这么多的网络设备，你需要为这些设备定制体验。

随着 IE9 的发布，是时候让这些老破车退休了。将 IE6 和 ie7 视为功能最差的移动设备，并使用条件注释来提供最低限度的体验。如果您正在使用我在本文开头提到的高级 clearfix 规则，但是缺少 IE6 和 ie7 的替代样式表，那么您已经忽略了浏览器兼容性问题。

**总会有浮动**

当然，浮动在某些情况下是有用的。总是需要浮动一个元素，以便其他页面内容围绕它流动。只是没有必要总是将它们压入复杂布局的服务中。

是这样吗？

你可能会想“是这样吗？看起来很简单，也很明显。”我同意！这才是重点。浮动人群会让你相信构建高级 CSS 布局的唯一方法是通过浮动元素的复杂堆栈。那不再是真的了。我们现在有了一堆易于使用的属性。将上面的一些或全部方法合并到一个布局中，你就可以不用浮动就可以做任何你想做的事情。

我看着我的孩子玩《小小大星球 2》，看着他们从零开始创造自己的世界。我希望他们以同样的方式看待网络:就像一个游乐场。当布局很容易创建时，制作生成 CSS 布局的工具也很容易。人们将不再需要手工编码。

自过去糟糕的日子以来，浏览器已经突飞猛进。在采用 Ajax 以及现在的 HTML5 和 CSS3 的推动下，浏览器制造商比以往更加努力地工作。我们必须保持这种势头，尽可能多地使用新技术。这些经验给了人们升级浏览器的理由，给了浏览器制造商继续开发的理由，给了工具制造商创造更好、更易用的工具的理由。我们必须探索边界，开发新的技术，并把那些旧的、珍贵的布局食谱推出门外。他们在拖我们的后腿。

**未来**

CSS3 布局被设置为提供更加令人兴奋的布局控件。但是，在这些标准得到广泛支持之前，我们还得再等几年。对我来说，似乎 web 行业的很大一部分都停滞不前，等待着 CSS3 布局标准得到完全支持。为什么要等？现在有了更简单的布局方法。

本文中所有示例的源代码都可以在 [GitHub]( https://github.com/learnable/Give-Floats-the-Flick-in-CSS-Layouts-Examples) 上获得。

在这篇文章的写作过程中，没有小猫受到伤害。

## 分享这篇文章