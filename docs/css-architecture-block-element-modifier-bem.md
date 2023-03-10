# CSS 架构块-元素-修改器(BEM)

> 原文：<https://www.sitepoint.com/css-architecture-block-element-modifier-bem/>

下面是我们的书《CSS 大师》的摘录，作者是蒂芙尼·布朗。世界各地的商店都有出售，或者你可以在这里买到电子书。

BEM 或 Block-Element-Modifier 是一种方法论、一种命名系统和一套相关工具。创建于 [Yandex](https://www.yandex.com/) 的 BEM 是为大型开发团队的快速开发而设计的。在这一节中，我们将关注概念和命名系统。

BEM 方法鼓励设计者和开发者将网站视为可重用组件块的集合，这些组件块可以混合和匹配来创建界面。块仅仅是文档的一部分，比如页眉、页脚或侧边栏，如图 2.3 所示。可能令人困惑的是，“块”在这里指的是组成页面或应用程序的 HTML 片段。

![](img/fdcc79175e91cd100c83d26e5dc88309.png)

图 2.3。主页可能有页眉、主页和页脚块

块可以包含其他块。例如，标题块可能还包含徽标、导航和搜索表单块，如图 2.4 所示。页脚块可能包含站点地图块。

![](img/8ccb2cc15d1fa243d0b5e9a5924233ab.png)

图 2.4。包含徽标、导航和搜索块的标题块

比块更细粒度的是元素。正如 [BEM 文档](https://en.bem.info/method/definitions/)所解释的:

> 元素是执行特定功能的块的一部分。元素是上下文相关的:它们只在它们所属的块的上下文中有意义。

例如，一个搜索表单块包含一个文本输入元素和一个提交按钮元素，如图 2.5 所示。为了澄清，我们在设计元素的意义上使用“元素”,而不是 HTML 元素的意义。

![](img/33b87f3b44badfbe4d46d3f6f617cd34.png)

图 2.5。带有文本输入和提交按钮元素的搜索块

另一方面，主内容块可能有一个文章列表块。该文章列表块可能包含一系列文章推广块。每个文章推广块可能包含图像、摘录和阅读更多元素，如图 2.6 所示。

![](img/d54272030ba3dbbc1bf16d40a1669558.png)

图 2.6。网站文章的促销区块

块和元素共同构成了边界元命名规则的基础。根据边界元法的规则:

*   块名在项目中必须是唯一的。
*   元素名称在块中必须是唯一的。
*   一个块的变体——比如一个黑色背景的搜索框——应该在类名中添加一个修饰符。

块名和元素名通常由双下划线`(.block__element)`分隔。块名和元素名通常用双连字符与修饰符名分开(例如，`.block--modifier`或`.block__element--modifier`)。

下面是使用搜索表单示例时 BEM 的样子:

```
<form class="search"> 
  <div class="search__wrapper"> 
    <label for="s" class="search__label">Search for: </label> 
    <input type="text" id="s" class="search__input"/>
    <button type="submit" class="search__submit">Search</button> 
  </div> 
</form>
```

带有深色背景的此表单的变体可能使用以下标记:

```
<form class="search search--inverse"> 
  <div class="search__wrapper search__wrapper--inverse"> 
  <label for="s" class="search__label search_label--inverse">
↵Search for: </label> 
  <input type="text" id="s" class="search__input search__input ↵--inverse"> 
  <button type="submit" class="search__submit search__submit-
↵inverse">Search</button> 
  </div> 
</form>
```

我们的 CSS 可能看起来像这样:

```
.search {
    color: #333;
}
.search--inverse {
    color: #fff;
    background: #333;
}
.search__submit {
    background: #333;
    border: 0;
    color: #fff;
    height: 2rem;
    display: inline-block;
}
.search__submit--inverse {
    color: #333;
    background: #ccc;
}
```

在我们的标记和 CSS 中，`search--inverse`和`search__label--inverse`是*附加的*类名。他们不是`search`和`search__label`的替代品。类名是 BEM 系统中唯一使用的选择器类型。可以使用子代和后代选择器，但是后代也应该是类名。禁止使用元素和 ID 选择器。这确保了选择器特异性保持较低，选择器没有副作用，CSS 独立于标记模式。强制块和元素名称的唯一性还可以防止命名冲突，这可能会成为团队中的一个问题。

这种方法有几个优点:

*   新团队成员很容易阅读标记和 CSS，并理解其行为
*   增加更多的开发人员可以提高团队的生产力
*   一致的命名减少了类名冲突和副作用的可能性
*   CSS 独立于标记
*   CSS 是高度可重用的

除了一章中的某一节可以轻松容纳的内容之外，还有更多的东西需要思考。BEM 站点更详细地描述了这种方法，并提供了一些工具和教程来帮助您入门。要了解更多关于 BEM 命名约定方面的内容，另一个很棒的资源是 [Get BEM](http://getbem.com/introduction/) 。

## 分享这篇文章