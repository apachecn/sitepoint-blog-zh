# 15 用答案回应面试问题

> 原文：<https://www.sitepoint.com/react-interview-questions-solutions/>

React 的受欢迎程度没有减弱的迹象，在全球许多城市，对开发者的需求仍然超过供应。对于经验不足的开发人员(或者那些已经离开就业市场一段时间的人)，在面试阶段展示你的知识可能会令人生畏。

在本文中，我们将研究 15 个问题，涵盖了对于理解和有效使用 React 至关重要的一系列知识。对于每个问题，我都会总结答案，并给出其他资源的链接，您可以在那里找到更多信息。

## 1.虚拟 DOM 是什么？

### 回答

虚拟 DOM 是组成应用程序 UI 的实际 HTML 元素的内存表示。当组件被重新呈现时，虚拟 DOM 将这些变化与其 DOM 模型进行比较，以便创建要应用的更新列表。主要的优点是它非常高效，只需对实际的 DOM 做最少的必要更改，而不必重新呈现大块的内容。

### 进一步阅读

*   [了解虚拟 DOM](https://bitsofco.de/understanding-the-virtual-dom/)
*   [虚拟 DOM 讲解](https://www.pluralsight.com/guides/virtual-dom-explained)

## 2.JSX 是什么？

### 回答

JSX 是 JavaScript 语法的扩展，允许编写看起来像 HTML 的代码。它编译成常规的 JavaScript 函数调用，为组件创建标记提供了一种更好的方式。

拿这个 JSX 来说:

```
<div className="sidebar" /> 
```

它转化为以下 JavaScript:

```
React.createElement(
  'div',
  {className: 'sidebar'}
) 
```

### 进一步阅读

*   [JSX 简介](https://www.sitepoint.com/an-introduction-to-jsx/)
*   [深入 JSX](https://reactjs.org/docs/jsx-in-depth.html)

## 3.类组件和函数组件有什么区别？

### 回答

在 React 16.8(钩子的引入)之前，基于类的组件用于创建需要维护内部状态的组件，或者利用生命周期方法(即`componentDidMount`和`shouldComponentUpdate`)。基于类的组件是一个 ES6 类，它扩展了 React 的`Component`类，并且至少实现了一个`render()`方法。

**类组件**:

```
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
} 
```

功能组件是无状态的(同样，< React 16.8)，并返回要呈现的输出。它们更适合于呈现只依赖于道具的 UI，因为它们比基于类的组件更简单，性能更好。

**功能组件**:

```
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
} 
```

注意:React 16.8 中钩子的引入意味着这些区别不再适用(参见问题 14 和 15)。

### 进一步阅读

*   [React 中的功能组件 vs 类组件](https://www.freecodecamp.org/news/functional-components-vs-class-components-in-react/)
*   [React](https://medium.com/@Zwenza/functional-vs-class-components-in-react-231e3fbd7108)中的功能组件与类别组件

## 4.钥匙是用来做什么的？

### 回答

在 React 中呈现集合时，为每个重复的元素添加一个键对于帮助 React 跟踪元素和数据之间的关联非常重要。该键应该是唯一的 ID，最好是集合项中的 UUID 或其他唯一字符串:

```
<ul>  {todos.map((todo) =>
    <li key={todo.id}>  {todo.text}  </li>
  )}; </ul> 
```

在集合中添加和移除项时，不使用键或使用索引作为键可能会导致奇怪的行为。

### 进一步阅读

*   [列表和键](https://reactjs.org/docs/lists-and-keys.html#keys)
*   [了解 React 的关键道具](https://kentcdodds.com/blog/understanding-reacts-key-prop)

## 5.状态和道具有什么区别？

### 回答

属性是从父组件传入组件的数据。它们不应该被改变，而只是显示或用于计算其他值。状态是组件的内部数据，可以在组件的生命周期内修改，并在重新呈现之间维护。

### 进一步阅读

*   [道具 vs 状态](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md)

## 6.为什么调用 setState 而不是直接突变 State？

### 回答

如果您试图直接改变组件的状态，React 无法知道它需要重新渲染组件。通过使用`setState()`方法，React 可以更新组件的 UI。

### 奖金

另外，您还可以讨论状态更新如何不能保证同步。如果您需要基于另一个状态(或道具)来更新组件的状态，请将一个函数传递给`setState()`，该函数将`state`和`props`作为它的两个参数:

```
this.setState((state, props) => ({
  counter: state.counter + props.increment
})); 
```

### 进一步阅读

*   [正确使用状态](https://reactjs.org/docs/state-and-lifecycle.html#using-state-correctly)

## 7.如何限制作为属性传递的值的类型，或者使它成为必需的？

### 回答

为了对组件的属性进行类型检查，您可以使用`prop-types`包(之前包含在 React 15.5 之前的版本中)来声明预期值的类型以及属性是否是必需的:

```
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
}; 
```

### 进一步阅读

*   [使用 proptypes 进行类型检查](https://reactjs.org/docs/typechecking-with-proptypes.html)

## 8.什么是道具钻，如何避免？

### 回答

当您需要将数据从父组件向下传递到层次结构中较低的组件时，会发生属性钻取，即“钻取”其他组件，这些组件除了传递它们之外，不需要属性本身。

有时，通过重构组件，避免过早地将组件分解成更小的组件，并在最近的公共父级中保持公共状态，可以避免适当的钻取。当你需要在组件树中相距很远/很远的组件之间共享状态时，你可以使用 React 的[上下文 API](https://reactjs.org/docs/context.html) ，或者像 [Redux](https://redux.js.org/) 这样的专用状态管理库。

### 进一步阅读

*   [支柱钻孔](https://kentcdodds.com/blog/prop-drilling)

## 9.什么是反应上下文？

### 回答

React 提供了上下文 API 来解决一个应用程序中多个组件之间共享状态的问题。在引入上下文之前，唯一的选择是引入一个单独的状态管理库，比如 Redux。然而，许多开发人员认为 Redux 引入了许多不必要的复杂性，尤其是对于较小的应用程序。

### 进一步阅读

*   [上下文(反应文档)](https://reactjs.org/docs/context.html)
*   [如何用 React 钩子和上下文 API 替换 Redux】](https://www.sitepoint.com/replace-redux-react-hooks-context-api/)

## 10.Redux 是什么？

### 回答

Redux 是 React 的第三方状态管理库，在上下文 API 存在之前创建。它基于状态容器的概念，称为存储，组件可以从存储接收数据。更新存储的唯一方法是向存储分派一个动作，该动作被传递给一个 reducer。缩减器接收动作和当前状态，并返回一个新状态，触发订阅的组件重新呈现。

![Redux conceptual diagram](img/62e619b28adda80f37bcd5b6d8a430ee.png)

### 进一步阅读

*   【Redux 入门
*   [深入了解 Redux](https://www.sitepoint.com/redux-deep-dive/)

## 11.设计 React 应用程序最常用的方法是什么？

### 回答

有多种方法来设计 React 组件的样式，每种方法都有优缺点。值得一提的主要有:

*   **内联样式**:非常适合原型设计，但是有局限性(例如，没有伪类样式)
*   基于类的 CSS 样式:比内联样式更具性能，对于刚接触 React 的开发人员来说也很熟悉
*   CSS-in-JS 样式化:有各种各样的库允许样式在组件中被声明为 JavaScript，更像代码一样对待它们。

### 进一步阅读

*   [如何设计 React 组件的样式](https://www.sitepoint.com/react-components-styling-options/)

## 12.受控组件和非受控组件有什么区别？

### 回答

在一个 HTML 文档中，许多表单元素(例如，`<select>`、`<textarea>`、`<input>`)保持它们自己的状态。不受控制的组件将 DOM 视为这些输入状态的真实来源。在受控组件中，内部状态用于跟踪元素值。当输入的值改变时，React 重新呈现输入。

当与非 React 代码集成时，不受控制的组件会很有用(例如，如果你需要支持某种 jQuery 表单插件)。

### 进一步阅读

*   [受控与非受控输入](https://goshakkk.name/controlled-vs-uncontrolled-inputs-react/)
*   [受控组件(反应文件)](https://reactjs.org/docs/forms.html#controlled-components)
*   [非受控组件(反应文件)](https://reactjs.org/docs/uncontrolled-components.html)

## 13.生命周期方法有哪些？

### 回答

基于类的组件可以声明特殊的方法，在生命周期的某个时刻调用这些方法，比如当它被挂载(呈现到 DOM 中)和即将被卸载的时候。这些对于设置和删除组件可能需要的东西很有用，例如，设置计时器或绑定到浏览器事件。

以下生命周期方法可用于在组件中实施:

*   **componentWillMount** :在组件创建之后，但在呈现到 DOM 之前调用
*   **componentDidMount** :第一次渲染后调用；组件的 DOM 元素现在可用了
*   **componentWillReceiveProps**:当一个道具更新时调用
*   **shouldComponentUpdate** :当收到新的道具时，这个方法可以防止重新渲染以优化性能
*   **componentWillUpdate** :收到新道具*时调用，* `shouldComponentUpdate`返回`true`
*   **componentDidUpdate** :组件更新后调用
*   **componentWillUnmount** :在组件从 DOM 中移除之前调用，允许您清理事件侦听器之类的东西。

当处理功能组件时，[`useEffect`钩子](https://reactjs.org/docs/hooks-effect.html)可以用来复制生命周期行为。

### 进一步阅读

*   [React 生命周期方法图](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)
*   [组件生命周期 API](https://reactjs.org/docs/react-component.html#the-component-lifecycle)

## 14.什么是 React 钩子？

### 回答

钩子是 React 将基于类的组件(即内部状态和生命周期方法)的优点带到功能组件中的尝试。

### 进一步阅读

*   [在 5 分钟内学会 React 钩子](https://www.freecodecamp.org/news/react-hooks-in-5-minutes/)
*   [React Hooks:如何入门&打造自己的](https://www.sitepoint.com/react-hooks/)

## 15.React 钩子有什么优点？

### 回答

引入钩子来作出反应有几个好处:

*   不再需要基于类的组件、生命周期挂钩和`this`关键字诡计
*   通过将通用功能抽象到定制挂钩中，使得重用逻辑更加容易
*   通过将逻辑从组件本身中分离出来，使代码更具可读性和可测试性

### 进一步阅读

*   [React 挂钩的好处](https://www.darrenlester.com/blog/benefits-of-react-hooks)
*   [React Hooks——与旧的可重用逻辑方法相比的优势，简而言之](https://medium.com/@mateuszroth/react-hooks-advantages-and-comparison-to-older-reusable-logic-approaches-in-short-f424c9899cb5)

## 包扎

尽管这绝不是一个全面的列表(React 是不断发展的)，但这些问题涵盖了很多领域。理解这些主题会让你对这个库有一个很好的了解，以及它最近的一些变化。遵循建议的进一步阅读将有助于你巩固你的理解，这样你就可以展示一个深入的知识。

我们将会跟进 React 面试代码练习指南，所以请在不久的将来密切关注。

祝你好运！

## 分享这篇文章