# React 片段:一种提高性能的简单语法

> 原文：<https://www.sitepoint.com/react-fragments-introduction/>

**[React 片段](https://reactjs.org/docs/fragments.html)于 2017 年 11 月随 [React 16.2.0](https://reactjs.org/blog/2017/11/28/react-v16.2.0-fragment-support.html) 推出。尽管它们已经存在了一段时间，但是许多 React 开发人员避免使用它们，或者在不知道它们是什么的情况下使用它们。React 片段是一个入门级但关键的特性，每个 React 开发人员都应该掌握它们，不管他们的技能水平如何。此外，考虑到他们成为 React 的一部分已经有多长时间了，他们不能再被忽视了。**

所以，让我们看看你需要掌握的一切，通过深入为什么、什么、如何和何时来反应片段。

## 为什么反应碎片存在

正如 React 官方文档中所述，返回多个 HTML 元素是 React 组件的常见行为。要实现这一点，您必须用 HTML 标记包装所有这些元素。这是因为 React 要求组件只返回一个 HTML 元素。最简单的解决方案是使用包装器`<div>`。从逻辑的角度来看，这个额外的`<div>`通常可以被认为是无关紧要的，但它确实会产生后果。首先，通过始终如一地使用这种方法，您可以使您的 DOM 更加嵌套，因此呈现起来更慢。第二，这种方法会导致无效的 HTML，正如您将要看到的。

例如，假设您有一个呈现 HTML 表格的`Table`组件，其列由另一个名为`Columns`的组件呈现。下面是您可能会用到的代码:

```
function Table() {
  return (
    <table>
      <tr>
        <Columns />
      </tr>
    </table>
  );
}

function Columns() {
  return (
    // the wrapper div used to return two <td> tags
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  );
} 
```

这将导致下面的 HTML 被呈现，这是无效的，因为`<div>`不能作为`<tr>`的子元素出现。

```
<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table> 
```

引入反应片段正是为了解决这个问题。

## 什么是反应碎片

React 片段涉及一种特殊的语法，允许您对 HTML 元素列表进行分组，而无需向 DOM 添加额外的节点。换句话说，React 片段使您能够对多个子组件进行分组，而无需在呈现的 HTML 中引入任何不必要的标记，为之前解决的问题以及许多其他问题提供了解决方案。

## 如何使用它们

您可以通过用一个`<React.Fragments>`标签包装组件返回的子元素来使用 React 片段。回到上面的例子，`Columns`组件将被编写如下:

```
function Columns() {
  return (
    <React.Fragment>
      <td>Hello</td>
      <td>World</td>
    </React.Fragment>
  );
} 
```

这将导致`Table`组件被翻译成以下 HTML:

```
<table>
  <tr>
    <td>Hello</td>
    <td>World</td>
  </tr>
</table> 
```

如您所见，在呈现的 HTML 中没有包装标签。与之前不同，这不会导致无效的 HTML。

React 片段也可以与短语法一起使用，看起来像一个空标签:

```
function Columns() {
  return (
    // using <> is just like using <React.Fragment>
    <>
      <td>Hello</td>
      <td>World</td>
    </>
    // using </> is just like using </React.Fragment>
  );
} 
```

这导致了与上面的例子相同的结果。所以，请记住，空标签是`<React.Fragment>`的简写。

## 何时使用它们

基本上，你应该在任何时候使用 React 片段，否则会引入不必要的包装器`<div>`来使你的组件返回多个 HTML 元素。所以，不要认为 Rect 片段可以替代 HTML 中的`<div>`。相反，把它们看作是避免不必要标签的一种方法，结果是得到一个更好的标记结构。

通常使用 React 片段的用例有三种。让我们都看看。

### 1.包装多个 HTML 元素

您可能已经猜到，包装多个 HTML 元素是 React 片段最常见的用例。每当您必须定义一个需要返回多个 HTML 元素的 React 组件时，您应该用`<React.Fragment>`或`<>`标记包装它们。如前所述，这是推荐的方法，因为使用任何其他包装器标签都会导致无效的 HTML。

让我们看看它们在定义一个返回四个 HTML 元素的组件时的运行情况。

见笔 [React 片段例 1](https://codepen.io/SitePoint/pen/VwbwQvJ) 由 site point([@ site point](https://codepen.io/SitePoint))
上 [CodePen](https://codepen.io) 。