# CSS 选择器备忘单

> 原文：<https://www.sitepoint.com/css-selectors/>

## 什么是 CSS 选择器？

CSS 选择器是 CSS 规则集的一部分，它允许您通过类型、属性或在 HTML 文档中的位置来选择想要设置样式的元素。让我们看看所有不同种类的可用选择器，并对每一种进行简要描述。这里有一个简明的 CSS 选择器备忘单，你可以在工作中参考。

## CSS 选择器的类型

*   **[万能](#h-universal) :** 每一个元素
*   **[元素类型](#h-element-type) :** 特定类型的元素，例如`<button>`。
*   **[ID](#h-id) :** 按 ID 属性的元素，如`id="main-content"`。
*   **[类](#h-class) :** 根据类属性选择元素，例如`class="primary-color"`。
*   **[子组合子](#h-child-combinator) :** 一个元素的直接后代(子元素)。
*   **[一般弟妹](#h-general-sibling-combinator):HTML 中的**一般弟妹。
*   **[相邻的兄弟姐妹](#h-adjacent-sibling-combinator):**HTML 中的直接兄弟姐妹。
*   **[属性](#h-attribute) :** 基于 HTML 属性的存在和/或值，例如`[type="text"]`。
*   **[伪类](#h-pseudo-class) :** 基于伪类的存在，例如`:hover`。
*   **[伪元素](#h-pseudo-element) :** 为伪元素，如`:before`元素。

## 通用 CSS 选择器

通用选择器像通配符一样工作，选择页面上的所有元素。每个 HTML 页面都是建立在 HTML 标签中的内容之上的。每组标签代表页面上的一个元素。请看下面的 CSS 示例，它使用了通用选择器:

```
* {
   color: green;
   font-size: 20px;
   line-height: 25px;
}
```

花括号内的三行代码(`color`、`font-size`和`line-height`)将应用于 HTML 页面上的所有元素。如此处所示，通用选择器是用星号声明的。您也可以将通用选择器与其他选择器结合使用。

## 元素类型 CSS 选择器

也简称为“类型选择器”，这个选择器必须匹配一个或多个同名的 HTML 元素。因此，一个 nav 选择器将匹配所有的 HTML `nav`元素，一个`<ul>`选择器将匹配所有的 HTML 无序列表，或`<ul>`元素。

以下示例使用元素类型选择器来匹配所有的`<ul>`元素:

```
ul {
   list-style: none;
   border: solid 1px #ccc;
}
```

为了将它放在一些上下文中，这里有一个 HTML 部分，我们将对其应用上面的 CSS:

```
<ul>
  <li>Fish</li>
  <li>Apples</li>
  <li>Cheese</li>
</ul>

<div class="example">
  <p>Example paragraph text.</p>
</div>

<ul>
  <li>Water</li>
  <li>Juice</li>
  <li>Maple Syrup</li>
</ul>
```

页面的这一部分由三个主要元素组成:两个`<ul>`元素和一个`<div>`。CSS 将只适用于两个`<ul>`元素，而不适用于`<div>`。如果我们将元素类型选择器改为使用`<div>`而不是`<ul>`，那么样式将应用于`<div>`而不是两个`<ul>`元素。

还要注意，样式不会应用到`<ul>`或`<div>`元素中的元素。也就是说，一些风格可能会被那些内部元素继承。

## ID CSS 选择器

ID 选择器是在一个字符串前使用一个散列或井号(`#`)来声明的。字符串是由开发者定义的。这个选择器匹配任何一个 HTML 元素，该元素的 ID 属性与选择器的 ID 属性具有相同的值，但是减去了散列符号。

这里有一个例子:

```
#container {
   width: 960px;
   margin: 0 auto;
}
```

这个 CSS 使用一个 ID 选择器来匹配一个 HTML 元素，比如:

```
<div id="container"></div>
```

在这种情况下，这是一个`<div>`元素并不重要——它可以是任何类型的 HTML 元素。只要它有一个值为`container`的 ID 属性，样式就会应用。

网页上的 ID 元素应该是唯一的。也就是说，在任何给定的页面上应该只有一个 ID 为`container`的元素。这使得 ID 选择器非常不灵活，因为 ID 选择器规则集中使用的样式每页只能使用一次。

如果页面上碰巧有多个元素具有相同的 ID，那么样式仍然适用，但是从技术角度来看，这样的页面上的 HTML 是无效的，所以您应该避免这样做。

除了不灵活的问题之外，ID 选择器还具有非常高的特异性的问题。

## 类 CSS 选择器

类选择器是所有 CSS 选择器中最有用的。它是在一个或多个字符的字符串前用点来声明的。就像 ID 选择器一样，这个字符串是由开发人员定义的。类选择器还匹配页面上的所有元素，这些元素的 class 属性设置为与类相同的值，只是少了点号。

以下面的规则集为例:

```
.box {
   padding: 20px;
   margin: 10px;
   width: 240px;
}
```

这些样式将应用于以下 HTML 元素:

```
<div class="box"></div>
```

同样的样式也适用于任何其他具有值为`box`的 class 属性的 HTML 元素。在一个页面上有多个元素具有相同的 class 属性是有益的，因为它允许您重用样式，并避免不必要的重复。除此之外，类选择器具有非常低的专一性——同样，稍后会详细介绍。

类选择器是一个有价值的盟友的另一个原因是 HTML 允许将多个类添加到单个元素中。这是通过使用空格分隔 HTML class 属性中的类来实现的。这里有一个例子:

```
<div class=”box box-more box-extended”></div>
```

## 后代组合子

[CSS 选择器组合器](https://www.sitepoint.com/css-selectors-combinators/)组合选择器进行精确定位。后代选择器，或者更准确地说，后代组合器允许您组合两个或多个选择器，以便您可以更具体地选择方法。例如:

```
#container .box {
   float: left;
   padding-bottom: 15px;
}
```

该声明块将应用于 ID 为`container`的元素中所有具有 box 类的元素。值得注意的是，`.box`元素不必是直接子元素:可以有另一个元素包装`.box`，样式仍然适用。

看看下面的 HTML:

```
<div id="container">
  <div class="box"></div>

  <div class="box-2"></div>
</div>

<div class="box"></div>
```

如果我们将前一个例子中的 CSS 应用到 HTML 的这一部分，那么唯一会受到这些样式影响的元素是第一个拥有类`box`的`<div>`元素。拥有`box-2`类的`<div>`元素不会受到样式的影响。类似地，类为`box`的第二个`<div>`元素不会受到影响，因为它不在 ID 为`container`的元素中。

在 CSS 中使用后代组合符时应该小心。这种选择器虽然使您的 CSS 更容易阅读，但可能不必要地将您的样式限制在特定的上下文中——在这种情况下，样式被限制在`#container`内的框中——这会使您的代码不灵活。

## 子组合子

使用`child`组合子的选择器类似于使用后代组合子的选择器，只是它只针对直接的`child`元素:

```
#container > .box {
   float: left;
   padding-bottom: 15px;
}
```

这与后代组合子示例中的代码相同，但是我们使用了大于号(或右尖括号)而不是空格字符。)

在这个例子中，选择器将匹配具有类`box`并且是`#container`元素的直接子元素的所有元素。这意味着，与后代组合符不同，不能有另一个元素包装`.box`——它必须是一个直接的子元素。

下面是一个 HTML 示例:

```
<div id="container">
  <div class="box"></div>

  <div>
    <div class="box"></div>
  </div>
</div>
```

在本例中，上一个代码示例中的 CSS 将只应用于第一个具有类`box`的`<div>`元素。如您所见，第二个`<div>`元素和一个`box`类位于另一个`<div>`元素内部。因此，样式不会应用于该元素，即使它也有一个类`box`。

同样，使用这种组合子的选择器可能有些限制，但是它们可以派上用场——例如，在设计嵌套列表时。

## 一般兄弟组合子

使用通用兄弟组合符的选择器根据兄弟关系匹配元素。也就是说，选中的元素在 HTML 中是并排的。

```
h2 ~ p {
   margin-bottom: 20px;
}
```

这种类型的选择器是使用波形符(~)声明的。在本例中，所有段落元素(`<p>`)都将使用指定的规则进行样式化，但前提是它们是`<h2>`元素的兄弟元素。在`<h2>`和`<p>`之间可能会有其他元素，样式仍然适用。

让我们将上面的 CSS 应用到下面的 HTML 中:

```
<h2>Title</h2>
<p>Paragraph example.</p>
<p>Paragraph example.</p>
<p>Paragraph example.</p>
<div class="box">
  <p>Paragraph example.</p>
</div>
```

在本例中，样式将仅应用于前三个段落元素。最后一个段落元素不是`<h2>`元素的同级，因为它位于`<div>`元素内部。

## 相邻兄弟组合子

使用相邻兄弟组合符的选择器使用加号(+)，与一般的兄弟选择器几乎相同。不同之处在于，目标元素必须是直接同级，而不仅仅是一般同级。让我们看看它的 CSS 代码是什么样子的:

```
p + p {
   text-indent: 1.5em;
   margin-bottom: 0;
}
```

本示例仅将指定的样式应用于紧跟在其他段落元素之后的段落元素。这意味着页面上的第一个段落元素不会接收这些样式。此外，如果另一个元素出现在两个段落之间，两个段落中的第二个段落不会应用样式。

因此，如果我们将这个选择器应用于下面的 HTML:

```
<h2>Title</h2>
<p>Paragraph example.</p>
<p>Paragraph example.</p>
<p>Paragraph example.</p>

<div class="box">
  <p>Paragraph example.</p>
  <p>Paragraph example.</p>
</div>
```

…这些样式将仅应用于 HTML 这一部分中的第二、第三和第五段。

## 属性 CSS 选择器

[属性选择器](https://www.sitepoint.com/css-selectors-attribute-selectors/)基于 HTML 属性的存在和/或值定位元素，并使用方括号声明:

```
input[type="text"] {
   background-color: #444;
   width: 200px;
}
```

左方括号前不应有空格，除非您打算将它与后代组合符一起使用。上述 CSS 将匹配以下元素:

```
<input type="text">
```

但它和这个不匹配:

```
<input type="submit">
```

属性选择器也可以只使用属性本身来声明，不带任何值，如下所示:

```
input[type] {
   background-color: #444;
   width: 200px;
}
```

这将匹配具有类型属性的所有输入元素，而不考虑值。

您还可以使用属性选择器，而不需要在方括号外指定任何内容(因此只基于属性进行定位，而不考虑元素)。同样值得注意的是，在使用值时，您可以选择包含或不包含引号(单引号或双引号)。

## 伪类 CSS 选择器

伪类使用冒号来标识元素可能处于的伪状态，例如，悬停状态或激活状态。让我们看一个常见的例子:

```
a:hover {
   color: red;
}
```

在这种情况下，选择器的伪类部分是`:hover`部分。这里我们将这个伪类附加到所有锚元素(`a`元素)。这意味着当用户将鼠标悬停在一个`a`元素上时，该元素的`color`属性将变为红色。这种类型的伪类是一个动态伪类，因为它只在响应用户交互时出现——在本例中，鼠标在目标元素上移动。

重要的是要认识到这些类型的选择器不仅仅选择元素；它们选择处于特定状态的元素。出于该示例的目的，该状态是“悬停”状态。

其他[流行的伪类还有](https://developer.mozilla.org/docs/Web/CSS/Pseudo-classes):

*   `:visited`:匹配访问过的链接
*   `:target`:匹配文档 URL 的目标元素
*   `:first-child`:以第一个子元素为目标
*   `:nth-child`:选择特定的子元素
*   `:empty`:匹配没有内容或子元素的元素
*   `:checked`:匹配选中的复选框或单选按钮
*   `:blank`:样式化一个空的输入字段
*   `:enabled`:匹配[一个启用的输入字段](https://www.sitepoint.com/atoz-css-enabled-pseudo-class/)
*   `:disabled`:匹配禁用的输入字段
*   `:required`:目标为必填输入字段
*   `:valid`:匹配有效的输入字段
*   `:invalid`:匹配无效的输入字段
*   `:playing`:针对正在播放的音频或视频元素
*   嵌套样式的原生 CSS 解决方案
*   `[:has](https://www.sitepoint.com/css-is-where-has-pseudo-class-selectors/)`:与`:is`相同，但与[特异性](https://www.sitepoint.com/css-selectors-specificity/)不同。
*   `[:where](https://www.sitepoint.com/css-is-where-has-pseudo-class-selectors/)`:类似于`:is()`和`:where()`的语法，但是它的目标是一个*包含*一组其他元素的元素

## 伪元素 CSS 选择器

最后，CSS 有一个称为伪元素的选择器，如果使用得当，它会非常有用。唯一需要注意的是，这个选择器与我们考虑过的其他例子非常不同。让我们在上下文中查看一个伪元素:

```
.container:before {
   content: "";
   display: block;
   width: 50px;
   height: 50px;
   background-color: #141414;
}
```

这个例子使用了一种伪元素，即`:before`伪元素。顾名思义，这个选择器将一个虚构的元素插入到页面中，在目标元素的内部，在它的内容之前。

## 分享这篇文章