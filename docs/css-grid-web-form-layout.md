# 将 CSS 网格用于 Web 表单布局的好处

> 原文：<https://www.sitepoint.com/css-grid-web-form-layout/>

**表单布局和设计是网页设计和开发的一个基本但令人沮丧的部分。问问那些曾经试图在所有浏览器中一致地设计一个`<select>`框或者对齐一个标签的人。**

2016 年，我写了“[用 Flexbox](https://www.sitepoint.com/make-forms-fun-with-flexbox/) 让表单变得有趣”，这篇文章指出了如何用 Flexbox 解决几个表单难题。一个关键的好处是 HTML 源代码顺序的一致性，在容器中,`<label>`总是跟在它的相关字段标签后面:

```
<div>
  <input id="name" name="name" type="text" />
  <label for="name">name</label>
</div>

<div>
  <select id="experience" name="experience"><!-- options --></select>
  <label for="experience">experience</label>
</div>

<div>
  <input id="html" name="html" type="checkbox" />
  <label for="html">HTML</label>
</div> 
```

Flexbox 可用于:

*   如有必要，请重新定位标签，即将其移动到文本输入、选择框和文本区域的字段左侧
*   垂直对齐标签和字段。

还可以使用相邻的同级选择器基于标签字段的状态来设置标签的样式，例如，当标签的相关复选框被选中时，对标签应用粗体:

```
input:checked + label {
  font-weight: bold;
} 
```

## 有缺陷的 Flexboxed 表单

不幸的是，使用 Flexbox 进行表单布局存在许多问题。Flexbox 创建一个一维布局，其中每个项目跟随另一个项目，并在必要时换行。字段/标签对必须放在应用了`display: flex;`的容器元素中，以保证每个都出现在新的一行上。

还需要定义一个固定的标签宽度，比如`10em`。如果一个长标签需要更多的空间，它的文本将溢出或调整元素的大小，并使字段与其他字段不对齐。

最后，表单通常被放置在一个网格中。既然所有主流浏览器都完全支持 CSS Grid】，我们难道不应该使用它吗？*绝对的！*

## 开发方法

大多数 CSS Grid 文章演示了这些概念，并可能为旧浏览器提供优雅的降级支持。当布局主要是装饰性的时候，这种方法是理想的——例如，定位页面内容、页眉、页脚和菜单。当 *oldBrowserX* 以不寻常的顺序显示线性块时，这几乎无关紧要，因为页面内容仍然可用。

表单布局更为关键:标签错位会导致用户在错误的框中输入信息。因此，本教程采用了一种渐进的增强方法:

1.  初始浮动表单布局可以在所有浏览器中工作，包括 IE8+(它也不支持 Flexbox)。它不会是完美的，但浮动从来都不是！
2.  在所有现代浏览器中使用 CSS Grid 增强表单布局。

下面的例子包含很少的 CSS 类，样式直接应用于 HTML 元素。这并不是 BEM 的方式，但这是为了保持代码的整洁和易懂。

您可以考虑使用类似的代码作为站点上所有表单的基础。

## HTML

典型的 HTML 表单可以保持整洁，因为不需要在字段/标签对周围包含(`<div>`)元素:

```
<form action="get">
  <fieldset>
    <legend>Your web development skillset</legend>

    <div class="formgrid">

      <input id="name" name="name" type="text" />
      <label for="name">name</label>

      <select id="experience" name="experience">
        <option value="1">1 year or less</option>
        <option value="2">2 years</option>
        <option value="3">3 - 4 years</option>
        <option value="5">5 years or more</option>
      </select>
      <label for="experience">experience</label>

      <input id="html" name="html" type="checkbox" />
      <label for="html">HTML</label>

      <input id="css" name="css" type="checkbox" />
      <label for="css">CSS</label>

      <input id="javascript" name="javascript" type="checkbox" />
      <label for="javascript">JavaScript</label>

      <textarea id="skills" name="skills" rows="5" cols="20"></textarea>
      <label for="skills">other skills</label>

      <button type="submit">SUBMIT</button>

    </div>

  </fieldset>
</form> 
```

唯一增加的元素是`<div class="formgrid">`。浏览器不能将`display: grid`或`display: flex`应用于`fieldset`元素。这可能最终会得到解决，但目前需要一个外部容器。

## 表单布局浮动回退

在一些初始的字体和颜色样式之后，彩车布局将分配:

*   70%的空间给右浮动的字段
*   30%的空间留给向左浮动的标签。

```
/* fallback 30%/70% float layout */
input, output, textarea, select, button {
  clear: both;
  float: right;
  width: 70%;
}

label {
  float: left;
  width: 30%;
  text-align: right;
  padding: 0.25em 1em 0 0;
} 
```

复选框和单选按钮位于标签之前，向左浮动。可以使用其固有宽度(`width:auto`)，但需要 30%的左边距才能正确对齐:

```
button, input[type="checkbox"], input[type="radio"] {
  width: auto;
  float: left;
  margin: 0.5em 0.5em 0 30%;
}

input[type="checkbox"] + label, input[type="radio"] + label {
  width: auto;
  text-align: left;
} 
```

表单布局适用于所有浏览器，包括 IE8+:

在 [CodePen](https://codepen.io) 上通过 SitePoint ( [@SitePoint](https://codepen.io/SitePoint) )看到钢笔[形成网格 1:浮动布局](https://codepen.io/SitePoint/pen/QVjaOB/)。