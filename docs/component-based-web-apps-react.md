# 基于组件的 React 应用

> 原文：<https://www.sitepoint.com/component-based-web-apps-react/>

ReactJS 是一个用于构建基于组件的 web 应用程序的工具包。React 聪明地使用 DOM 模拟来最小化执行的 DOM 操作和查找的数量，从而在快速和轻松方面大放异彩。React 组件是用 Javascript 和 XML 混合编写的，但是使用 React 的编译器工具编译成纯 JavaScript。下面是一个显示当前时间的组件示例，当您单击它时会弹出一个警告:

```
<script type="text/jsx">
/** @jsx React.DOM */
var Clock = React.createClass({
  render: function() {
    return (
      <div onClick={this.sayHi}>
        The time is: {this.props.when.toLocaleString()}
      </div>
    );
  },
  sayHi: function() {
    alert("Hello");
  }
});

React.renderComponent(
  <Clock when={new Date()} />,
  document.body
);
</script>
```

因为代码混合了 XML 和 JavaScript，所以浏览器不能直接执行语法。这就是为什么脚本标签需要将类型设置为`text/jsx`。要运行它，需要用 React 的编译器工具将代码编译成纯 JavaScript。或者，网站可以包括 JSX 实时编译器(另一个 JavaScript 库)，尽管这通常只用于开发或测试目的。

React 编译器还要求在顶部放置一个注释行，说明`@jsx React.DOM`。这一行告诉 React 编译器，嵌入式代码需要用`React.DOM`参数编译。这可能会在未来发生变化，但适用于 React v0.10 RC。

编译完代码后，XML 将被转换，并生成以下 JavaScript 代码。如您所见，XML 仅仅是一种语法糖，它允许用 HTML 语法编写接口。

```
<script type="text/javascript">
 var Clock = React.createClass({
  render: function() {
    return React.DOM.div(
      {onClick: this.sayHi},
      "The time is: ",
      this.props.when.toLocaleString()
    );
  },
  sayHi: function() {
    alert("Hello");
  }
});

React.renderComponent(
  Clock({when: new Date()}),
  document.body
);
</script>
```

## 使用 XML

每个 React 组件都只是一个带有`render()`函数的对象。这个函数返回描述接口外观的 XML。重要的是要记住，XML 不是我们习惯的 HTML 的直接映射。当你写`<table><tr><td></td></tr></table>`的时候，你不是在创建一个表。实际上，您正在创建三个组件(`table`、`tr`和`td`)，并将一个组件作为参数传递给另一个组件。

这也意味着，不是在 XML 中设置的每个属性都会出现在生成的 HTML 中。组件必须专门处理该属性才能被使用。幸运的是，React 的默认组件集支持所有通常使用的常见属性，如`id`、`href`、`src`、`type`、`checked`等。

与标准不同的是，所有属性都必须是驼峰式的。比如`<input onclick="" />`写成`<input onClick="" />`，`<td colspan="3">`变成`<td colSpan="3">`。另外，`style`属性被给予了特殊的处理，因为它期望一个样式的散列对象作为参数，而不是通常的 CSS 语法。一个示例性的`style`属性是`<div style={ {fontFamily:"Arial", marginLeft:10} }></div>`。

XML 的另一个特点是它的语法比 HTML 更严格。所有 XML 标签必须带有结束标签(`</td>`、`</p>`)或者是自结束标签(`<input />`)。

由于 XML 只是用作调用组件的方法，所以所有定制组件都以相同的方式调用。

```
<!-- inside the render function -->
<table>
  <tr>
    <td>
      <ShoppingCart size="mini">
        <List />
      </ShoppingCart>
    </td>
  </tr>
</table>
```

组件的名称就是您在创建时分配给它的变量名:

```
var ShoppingCart = React.createClass({ /* ... */ });
```

您可能已经注意到了 XML 中的花括号。这些包含 JavaScript 表达式，当代码被编译成 JavaScript 时，这些表达式将被逐字复制。

## 组件的作用

组件最重要的方面是`render()`函数。这个函数不呈现 HTML，也不生成一个 DOM 节点来附加到网页上。它的作用是生成一个 JavaScript 对象树，类似于 DOM 应该有的样子；一种“模拟的 DOM”，如果你愿意的话。但是，这都是用 JavaScript 对象完成的，这些对象是轻量级的，很容易被垃圾收集。

模拟的 DOM 不能直接使用。相反，它被传递给 React，React 使用差分算法来检测与上一次模拟的 DOM 相比的变化。然后，这些差异将作为一系列更新操作应用于实际网页。

除了`render()`之外，组件还控制事件注册并公开生命周期事件，以便开发人员可以在创建和销毁组件时执行额外的任务。

## DOM 模拟

由于 React 依靠差分算法来检测更改和更新，因此不再需要编写代码来修改 DOM。这意味着您不再需要调用`setAttribute()`或`input.value`。所有这些都由 React 引擎以不可见的方式处理。

您的组件需要做的就是提供一个`render()`函数来构建模拟的 DOM。每次页面需要更新时，都会调用`render()`，会生成一个新的模拟 DOM。这意味着需要编写和维护的代码更少。

这是可能的，因为模拟的 DOM 执行速度很快，允许 React 最小化每次渲染都必须重新生成整个树的性能影响。React 还能够使用几种试探法来使 O(n^3 树遍历问题更接近 O(n)问题。

## 事件处理

事件处理程序使用诸如`onClick` `onMouseOver`、`onKeyPress`等属性附加到组件上。这些事件处理程序只能处理 HTML 标签，不能处理自定义组件。对于自定义组件，必须将属性传递给自定义组件中的一个 HTML 标记。包含事件处理程序的示例如下所示。

```
<!-- inside the render function -->
<div>
  <button onClick={this.actionA} />
  <button onClick={this.actionB} />
</div>
```

在后台，React 将事件侦听器附加到基本节点，并使用事件委托将事件传播到目标组件。这样做是为了提高性能，所以您可以安全地向列表的每一行添加事件。

同样的技术也可以用于将回调函数传递给组件，作为组件与其父组件通信的一种方式。

```
var MainApp = React.createClass({
  render: function() {
    return (
      <div>
        <ShoppingCart onCheckout={this.checkoutCart} onEmpty={this.emptyCart} />
      </div>
    );
  },
  checkoutCart: function() { /* ... */ },
  emptyCart: function() { /* ... */ }
});
```

## 属性

使用属性将数据传递给组件:

```
var myData = {list: [], amount: 0, taxes:1.15};

var MainApp = React.createClass({
  render: function() {
    return <ShoppingCart goods={myData} />;
  }
});

var ShoppingCart = React.createClass({
  render: function() {
    return <div>Amount: {this.props.goods.amount}</div>;
  }
});
```

然后，组件通过从`this.props`属性访问数据来检索数据。与属性值为字符串的传统 HTML 不同，React 属性可以被赋予复杂的对象，因为在代码被编译后，它都被转换为 JavaScript 对象。

小型组件通常通过属性传递数据，而大型组件(行为像一个成熟的应用程序)从外部源检索数据，并将其分割并向下传递给较小的组件。

大型组件可以在内部以`state`的形式存储数据。状态可以被看作是组件私有的数据存储。状态中的数据通过调用组件上的`setState(objectHash)`来设置。这使得数据可以从`this.state`属性中获得。调用`setState()`触发组件更新，组件更新调用`render()`。`state`和`props`的用法是相似的，但是为了方便组件实现者，它们在语义上是不同的。

### 属性的健壮性

您可能已经注意到，React 非常依赖于属性。不像其他工具包带来了很多不同的装置，React 并没有真正打包那么多。这就是为什么，除非您引入其他具有数据持久性或通用消息系统的库，否则您将不得不依赖属性来传递函数和对象。这不一定是件坏事。React 的简单性使得它非常容易掌握和使用。属性系统非常灵活，并采用严格的自顶向下的方法来传递数据。

## 使其可扩展

到目前为止，我们已经看到 React 可以随时更新接口，即使是对数据的微小更改，因为 React 会计算更新 DOM 所需的最小更改集，因此效率很高。但是，在这个过程中，您可能会遇到性能问题，或者只是想优化您的组件。优化的秘密在于在`render()`之前调用的`shouldComponentUpdate()`函数。每个组件都有这个功能，可以覆盖特定组件及其子组件是否被更新。该函数将新的`props`和`state`作为参数。您可以使用这些来检查更新是否真的有必要。

例如，如果一个特定的数据列表有一个时间戳，实现可以简单地将时间戳与旧的时间戳进行比较，使接口不必更新那个特定的组件。

对于一个更复杂的例子，整个应用程序可以基于检查旧值和新值来创建。通常，当从服务器获得新数据时，诸如 Backbone 之类的数据模型库需要识别哪个特定属性被更改，并且需要触发该属性的相应处理程序。在这种方法中，当新数据可用时，它会立即替换旧数据，并更新界面。随着更新沿着树向上传播，每个组件只需要检查新数据是否不同于调用`shouldComponentUpdate()`中的旧数据，以确定树的该分支是否需要更新。

在试图提高性能时要记住的另一点是，`render()`函数可能会被多次调用，最好将繁重的计算(如排序)放在该函数之外，并可能缓存结果。当在`render()`中执行时，诸如小计之类的小计算是可以的。

## 结论

因为 React 只提供了构建用户界面的工具，它没有构建数据或服务提供商的工具。人们已经成功地使用 React 作为 UI 界面，使用 Angular 作为框架。其他人只用 React 就能让它工作。没有规定什么方法是最好的。许多组合都有效，这表明了 React 能够提供的灵活性。你只需要找到自己的组合。查看[项目的页面](http://facebook.github.io/react/)，让我们知道 React 如何为您工作。

## 分享这篇文章