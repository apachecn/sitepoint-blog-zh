# 特征比较表的响应解决方案

> 原文：<https://www.sitepoint.com/responsive-solutions-for-feature-comparison-tables/>

响应式网页设计和表格不一定是最好的朋友。许多人研究了这种情况，并设计了许多方法(其中一些甚至在 SitePoint 最近的一篇文章中进行了总结)。然而，我们离完美的解决方案还很远，研究还在继续。

虽然在一般情况下事情仍然很复杂，但是某些特定的情况可以得到更多的关注。我这里说的是特性对照表。我们在很多地方都会遇到这种情况——在选择汽车并试图决定选择哪些额外选项时；在虚拟主机网站上比较计划和功能时；在任何会员制的门户网站上，您可以决定用钱来换取什么功能。

因为这种表格具有相对稳定和一致的结构，所以当在小屏幕上显示时，有可能哄出更好的行为。

## 特征对照表的剖析

经典比较表将至少三种产品(以列显示)放在一起，而特性显示在下面的行中。在传统结构中，每一行的第一个单元格都有特性的名称，而每个产品下的单元格都有一个复选标记或其他符号，表示该特性是否属于该产品。我们可以找到这种经典结构的很好的例子:这里的，这里的，这里的

基于这些例子，我们可以用下面的代码来总结比较表的结构:

```
<table>
  <thead>
    <tr>
      <th>&nbsp;</th>
      <th>Product 1</th>
      <th>Product 2</th>
      <th>Product 3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Feature 1</td>
      <td>&#10004;</td>
      <td>&#10004;</td>
      <td>&#10004;</td>
    </tr>
    <tr>
      <td>Feature 2</td>
      <td>&mdash;</td>
      <td>&#10004;</td>
      <td>&#10004;</td>
    </tr>
    <tr>
      <td>Feature 3</td>
      <td>&mdash;</td>
      <td>&mdash;</td>
      <td>&#10004;</td>
    </tr>
    <tr>
      <td>Feature 4</td>
      <td>&mdash;</td>
      <td>&mdash;</td>
      <td>&#10004;</td>
    </tr>
  </tbody>
</table>
```

很容易识别前面提到的元素:产品名称、特性名称以及表示该特性是否存在的标记。注意，`&#10004;`代码代表一个复选标记(✔)字符。

我们现在找到了问题的根源。为了使工作台在低屏幕宽度下保持最佳效果，必须满足几个条件:

*   用户必须能够容易地区分产品；
*   这些特征必须易于识别；和
*   必须清楚产品的功能是否存在。

实现这一结果的最佳方法是将包含功能名称的单元格移动到其他三个标记功能存在与否的单元格之上。

## 第一个解决方案:Flexbox

我们如何实现这一目标？一个答案是 flexbox。如果你不知道 flexbox 是什么或者你需要复习，你可以看看 Nick Salloum 最近关于这个主题的文章。我们其余的人可以深入研究解决方案。

首先，我们需要确保我们的改变只发生在小屏幕上。为了实现这一点，我们使用媒体查询来定位我们的代码，使用经典的宽度`768px`作为断点:

```
@media screen and (max-width: 768px) {
  tr {
    display: flex;
    flex-flow: row wrap;
    justify-content: space-around;
  }

  td, th {
    display: block;
    width: 33%;
  }

  th:first-child,
  td:first-child {
    text-align: center;
    background: #efefef;
    width: 100%;
  }

  th:first-child {
    display: none;
  }
}
```

在这套规则中有几个重要的东西让奇迹发生:

*   我们将表格行的显示值更改为`flex`，并告诉它的子元素在一行中流动，均匀分布。
*   接下来，我们指导单元格采用`display:block`将它们规范化为普通容器(保留默认值将会带来表格规则的干扰，尤其是关于大小)。
*   下一步的目标是每一行的第一个单元格，使其为全幅并改变背景色，以增加对比度。流动规则使它停留在其他三个细胞的顶部——这正是我们所需要的。
*   我们通过隐藏第一个`th`来完成更改，这样产品名称上方就不会显示任何内容。

[**试玩可以在这里看**](http://codepen.io/SitePoint/full/jPXjJK/) 。

显然，一个解决方案只有得到足够的支持才是有效的。[根据 caniuse.com](http://caniuse.com/#feat=flexbox)的说法，对 flexbox 最现代版本的支持超过 80%,如果我们包括需要前缀或使用先前版本规则的浏览器版本，则超过 93%。对 IE 的支持是从 IE10 开始的(只有 2012 的语法)，而 IE11 是完全支持的。因为我们主要对小屏幕上的支持感兴趣，所以我们可以忽略对 IE 以前版本支持的缺乏。在移动方面，支持从 Android 4.4 和 iOS 7.1 开始。以前的版本需要供应商前缀，并且不支持换行功能。

你还应该提供回退，比如在 Bootstrap 中使用的滚动 div。这样，落在支架之外的参观者仍将有另一种展示选择。

## 第二个解决方案:额外的标记+ ARIA 角色

如果您要支持的大部分浏览器不支持 flexbox，还有一个替代方法。事实上，这是我在 2013 年的一个真实项目中使用的解决方案。我们将需要一些额外的标记:我们将不得不添加一个额外的行，复制特性名称。虽然手动操作看起来很乏味，但是如果从数据源读取信息，就可以实现自动化。最后，我们最初示例中的代码应该是这样的:

```
<table>
  <thead>
    <tr>
      <th>&nbsp;</th>
      <th>Product 1</th>
      <th>Product 2</th>
      <th>Product 3</th>
    </tr>
  </thead>
  <tbody>
    <tr class="visible-xs" aria-hidden="true">
      <td>&nbsp;</td>
      <td colspan="3">Feature 1</td>
    </tr>
    <tr>
      <td>Feature 1</td>
      <td>&#10004;</td>
      <td>&#10004;</td>
      <td>&#10004;</td>
    </tr>
    <tr class="visible-xs" aria-hidden="true">
      <td>&nbsp;</td>
      <td colspan="3">Feature 2</td>
    </tr>
    <tr>
      <td>Feature 2</td>
      <td>&mdash;</td>
      <td>&#10004;</td>
      <td>&#10004;</td>
    </tr>
    <tr class="visible-xs" aria-hidden="true">
      <td>&nbsp;</td>
      <td colspan="3">Feature 3</td>
    </tr>
    <tr>
      <td>Feature 3</td>
      <td>&mdash;</td>
      <td>&mdash;</td>
      <td>&#10004;</td>
    </tr>
    <tr class="visible-xs" aria-hidden="true">
      <td>&nbsp;</td>
      <td colspan="3">Feature 4</td>
    </tr>
    <tr>
      <td>Feature 4</td>
      <td>&mdash;</td>
      <td>&mdash;</td>
      <td>&#10004;</td>
    </tr>
  </tbody>
</table>
```

CSS 也非常简单:

```
.visible-xs {
  display: none;
}

@media screen and (max-width: 768px) {
  .visible-xs {
    display: table-row;
  }

  td:first-child,
  th:first-child {
    display: none;
  }
}
```

为了便于访问，我们可以多走一步，用`aria-hidden="true"`对屏幕阅读器隐藏额外的标记。这样，那些遵守 [aria-hidden](https://www.w3.org/TR/wai-aria/states_and_properties#aria-hidden) 规范的屏幕阅读器应用程序就不会读取重复的内容两次。

[**这里是第二种解决方案**](http://codepen.io/SitePoint/full/pJqXGZ/) 的演示。

## 结论

我们在这里发现了两种使比较表真正响应的方法。两者各有利弊。最后，选择应该取决于你的听众的具体情况。在大多数情况下，第一个选择(以及后备)就足够了。如果你真的需要迎合老版本的 **Android** 和 **iOS** ，你可以部署第二个选项。无论哪种方式，从现在开始，无论屏幕大小如何，你的特性对照表将会看起来更好。

## 分享这篇文章