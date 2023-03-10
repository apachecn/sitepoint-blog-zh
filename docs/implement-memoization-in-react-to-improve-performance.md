# 如何在 react 中实现记忆以提高性能

> 原文：<https://www.sitepoint.com/implement-memoization-in-react-to-improve-performance/>

在本教程中，我们将学习如何在 React 中实现记忆化。内存化通过存储昂贵的函数调用的结果并在需要时返回这些缓存的结果来提高性能。

我们将讨论以下内容:

*   React 如何呈现用户界面
*   为什么需要反应记忆
*   我们如何实现函数和类组件的记忆化
*   关于记忆要记住的事情

本文假设您对 React 中的类和函数组件有基本的了解。如果你想重温这些话题，可以查看一下关于[组件和道具](https://reactjs.org/docs/components-and-props.html)的官方 React 文档。

![Memoization in React](img/c46c74c5a794218a027d31018766e559.png)

## React 如何呈现用户界面

在深入 React 中内存化的细节之前，我们先来看看 React 是如何使用虚拟 DOM 呈现 UI 的。

常规 DOM 基本上包含一组表示为树的节点。DOM 中的每个节点都代表一个 UI 元素。每当应用程序中出现状态变化时，该 UI 元素及其所有子元素的相应节点都会在 DOM 中得到更新，然后重新绘制 UI 以反映更新后的变化。

借助高效的树算法，更新节点的速度会更快，但是当 DOM 有大量 UI 元素时，重新绘制会很慢，并且会影响性能。因此，React 中引入了虚拟 DOM。

这是真实 DOM 的虚拟表示。现在，只要应用程序的状态有任何变化，React 就会创建一个新的虚拟 DOM，而不是直接更新真实的 DOM。React 然后将这个新的虚拟 DOM 与之前创建的虚拟 DOM 进行比较，找出需要重新绘制的差异。

利用这些差异，虚拟 DOM 将根据变化有效地更新真实 DOM。这提高了性能，因为虚拟 DOM 将只有效地更新真实 DOM 中必要的和最小的更改，而不是简单地更新 UI 元素及其所有子元素。

## 为什么反应需要记忆

在上一节中，我们看到了 React 如何使用虚拟 DOM 高效地执行 DOM 更新以提高性能。在这一节中，我们将看一个用例，它解释了内存化对进一步提高性能的需求。

我们将创建一个父类，它包含一个按钮来增加一个名为`count`的状态变量。父组件还有一个对子组件的调用，向它传递一个属性。我们还在两个类的 render 方法中添加了`console.log()`语句:

```
//Parent.js
class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  handleClick = () => {
    this.setState((prevState) => {
      return { count: prevState.count + 1 };
    });
  };

  render() {
    console.log("Parent render");
    return (
      <div className="App">  <button onClick={this.handleClick}>Increment</button>  <h2>{this.state.count}</h2>  <Child name={"joe"} />  </div>
    );
  }
}

export default Parent; 
```

这个例子的完整代码可以在 [CodeSandbox](https://codesandbox.io/s/adoring-flower-c7zu0) 上找到。

我们将创建一个`Child`类，它接受父组件传递的属性，并将其显示在 UI 中:

```
//Child.js
class Child extends React.Component {
  render() {
    console.log("Child render");
    return (
      <div>  <h2>{this.props.name}</h2>  </div>
    );
  }
}

export default Child; 
```

每当我们单击父组件中的按钮时，计数值都会改变。因为这是一个状态更改，所以调用父组件的 render 方法。

传递给子类的道具对于每个父类的重新渲染保持不变，所以子组件不应该重新渲染。然而，当我们运行上述代码并不断增加计数时，我们会得到以下输出:

```
Parent render
Child render
Parent render
Child render
Parent render
Child render 
```

您可以在下面的沙箱中自己增加上述示例的计数，并查看控制台的输出:

[https://codesandbox.io/embed/patient-currying-j2x3z?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/patient-currying-j2x3z?fontsize=14&hidenavigation=1&theme=dark)



从这个输出中，我们可以看到，当父组件重新呈现时，它也会重新呈现子组件——即使传递给子组件的道具没有改变。这将导致子虚拟 DOM 执行与前一个虚拟 DOM 的差异检查。因为我们在子组件上没有区别——因为所有重新渲染的道具都是一样的——所以真正的 DOM 没有更新。

我们确实有一个性能上的好处，真实的 DOM 没有不必要的更新，但是我们可以看到，即使在子组件中没有实际的变化，新的虚拟 DOM 也被创建并执行了差异检查。对于小的 React 组件，这种性能可以忽略不计，但是对于大的组件，性能影响是显著的。为了避免这种重新渲染和虚拟 DOM 检查，我们使用了内存化。

### 反应中的记忆

在 React 应用程序的上下文中，记忆化是一种技术，每当父组件重新渲染时，只有在道具发生变化时，子组件才会重新渲染。如果道具没有变化，它将不会执行 render 方法，并将返回缓存的结果。由于没有执行 render 方法，所以不会有虚拟 DOM 创建和差异检查——从而提高了性能。

现在，让我们看看如何在类和函数 React 组件中实现记忆化，以避免这种不必要的重新呈现。

## 在类组件中实现记忆化

为了在类组件中实现记忆化，我们将使用 [React。纯组件](https://reactjs.org/docs/react-api.html#reactpurecomponent)。`React.PureComponent`实现了 [shouldComponentUpdate()](https://reactjs.org/docs/react-component.html#shouldcomponentupdate) ，它对状态和道具做了一个浅显的比较，只有当道具或状态发生变化时才呈现 React 组件。

将子组件更改为如下所示的代码:

```
//Child.js
class Child extends React.PureComponent { // Here we change React.Component to React.PureComponent
  render() {
    console.log("Child render");
    return (
      <div>  <h2>{this.props.name}</h2>  </div>
    );
  }
}

export default Child; 
```

此示例的完整代码显示在以下沙箱中:

[https://codesandbox.io/embed/memoization-class-component-forked-jwt2q?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/memoization-class-component-forked-jwt2q?fontsize=14&hidenavigation=1&theme=dark)



父组件保持不变。现在，当我们增加父组件中的计数时，控制台中的输出如下:

```
Parent render
Child render
Parent render
Parent render 
```

对于第一次呈现，它调用父组件和子组件的呈现方法。

对于每个增量的后续重新渲染，只调用父组件的`render`函数。子组件不会重新呈现。

## 在功能组件中实现记忆

为了在功能性 React 组件中实现记忆化，我们将使用 [React.memo()](https://reactjs.org/docs/react-api.html#reactmemo) 。`React.memo()`是一个[的高阶组件](https://www.sitepoint.com/react-higher-order-components/) (HOC)，做着与`PureComponent`相似的工作，避免不必要的重新渲染。

下面是一个功能组件的代码:

```
//Child.js
export function Child(props) {
  console.log("Child render");
  return (
    <div>  <h2>{props.name}</h2>  </div>
  );
}

export default React.memo(Child); // Here we add HOC to the child component for memoization 
```

我们还将父组件转换为功能组件，如下所示:

```
//Parent.js
export default function Parent() {
  const [count, setCount] = useState(0);
  const handleClick = () => {
    setCount(count + 1);
  };
  console.log("Parent render");
  return (
    <div>  <button onClick={handleClick}>Increment</button>  <h2>{count}</h2>  <Child name={"joe"} />  </div>
  );
} 
```

此示例的完整代码可以在下面的沙箱中看到:

[https://codesandbox.io/embed/memoization-functional-component-forked-z42uj?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/memoization-functional-component-forked-z42uj?fontsize=14&hidenavigation=1&theme=dark)



现在，当我们增加父组件中的计数时，以下内容将输出到控制台:

```
Parent render
Child render
Parent render
Parent render
Parent render 
```

## React.memo()对于函数属性的问题

在上面的例子中，我们看到当我们对子组件使用`React.memo()` HOC 时，子组件没有重新渲染，即使父组件重新渲染了。

然而，需要注意的一点是，如果我们将一个函数作为 prop 传递给子组件，即使在使用了`React.memo()`之后，子组件也会重新呈现。让我们来看一个例子。

我们将更改父组件，如下所示。这里，我们添加了一个处理函数，我们将把它作为道具传递给子组件:

```
//Parent.js
export default function Parent() {
  const [count, setCount] = useState(0);
  const handleClick = () => {
    setCount(count + 1);
  };

  const handler = () => {
    console.log("handler");    // This is the new handler that will be passed to the child
  };

  console.log("Parent render");
  return (
    <div className="App">  <button onClick={handleClick}>Increment</button>  <h2>{count}</h2>  <Child name={"joe"} childFunc={handler} />  </div>
  );
} 
```

子组件代码保持不变。我们不使用我们传递的函数作为子组件中的道具:

```
//Child.js
export function Child(props) {
  console.log("Child render");
  return (
    <div>  <h2>{props.name}</h2>  </div>
  );
}

export default React.memo(Child); 
```

现在，当我们增加父组件的数量时，它会重新渲染子组件，即使传递的道具没有变化。

那么，是什么原因导致孩子重新渲染呢？答案是，每次父组件重新呈现时，都会创建一个新的处理函数并传递给子组件。现在，由于在每次重新呈现时都会重新创建处理程序函数，所以子组件在对 props 进行浅层比较时，会发现处理程序引用已经更改，并重新呈现子组件。

在下一节中，我们将看到如何解决这个问题。

## `useCallback()`为了避免进一步的重新渲染

导致子进程重新呈现的主要问题是重新创建处理函数，这改变了传递给子进程的引用。所以，我们需要有一种方法来避免这种娱乐。如果没有重新创建处理程序，对处理程序的引用不会改变，所以子进程不会重新呈现。

为了避免每次渲染父组件时都重新创建函数，我们将使用一个名为 [useCallback()](https://reactjs.org/docs/hooks-reference.html#usecallback) 的 React 钩子。钩子是在 React 16 中引入的。想要了解更多关于钩子的知识，可以看看 React 的官方[钩子文档](https://reactjs.org/docs/hooks-intro.html)，或者查看“ [React 钩子:如何入门&打造自己的](https://www.sitepoint.com/react-hooks/)”。

`useCallback()`钩子接受两个参数:回调函数和依赖项列表。

考虑下面这个`useCallback(`的例子:

```
const handleClick = useCallback(() => {
  //Do something
}, [x,y]); 
```

这里，`useCallback()`被添加到`handleClick()`函数中。第二个参数`[x,y]`可以是一个空数组、一个依赖项或者一个依赖项列表。每当第二个参数中提到的任何依赖关系发生变化时，只有这时才会重新创建`handleClick()`函数。

如果`useCallback()`中提到的依赖关系没有改变，那么作为第一个参数提到的回调的记忆版本将被返回。我们将修改父功能组件，将`useCallback()`钩子用于传递给子组件的处理程序:

```
//Parent.js
export default function Parent() {
  const [count, setCount] = useState(0);
  const handleClick = () => {
    setCount(count + 1);
  };

  const handler = useCallback(() => { //using useCallback() for the handler function
    console.log("handler");
  }, []);

  console.log("Parent render");
  return (
    <div className="App">  <button onClick={handleClick}>Increment</button>  <h2>{count}</h2>  <Child name={"joe"} childFunc={handler} />  </div>
  );
} 
```

子组件代码保持不变。

该示例的完整代码如下所示:

[https://codesandbox.io/embed/usecallback-forked-r797x?fontsize=14&hidenavigation=1&theme=dark](https://codesandbox.io/embed/usecallback-forked-r797x?fontsize=14&hidenavigation=1&theme=dark)



当我们增加上面代码的父组件中的计数时，我们可以看到以下输出:

```
Parent render
Child render
Parent render
Parent render
Parent render 
```

因为我们对父处理程序使用了`useCallback()`钩子，所以每次父处理程序重新渲染时，处理程序函数都不会被重新创建，处理程序的内存化版本会被发送给子处理程序。子组件将做一个简单的比较，并注意到处理函数的引用没有改变——所以它不会调用`render`方法。

## 要记住的事情

记忆化是一种提高 React 应用程序性能的好技术，如果组件的属性或状态没有改变，它可以避免不必要的重新渲染。您可能会想到为所有组件添加记忆，但这不是构建 React 组件的好方法。只有在组件出现以下情况时，才应该使用记忆化:

*   给定相同的属性时，返回相同的输出
*   有多个 UI 元素，虚拟 DOM 检查会影响性能
*   经常被提供相同的道具

## 结论

在本教程中，我们看到了:

*   React 如何呈现用户界面
*   为什么需要记忆
*   如何在 React 中通过功能性 React 组件的`React.memo()`和类组件的`React.PureComponent`实现内存化
*   一个用例，其中，即使在使用`React.memo()`之后，子组件将重新呈现
*   当一个函数作为道具传递给一个子组件时，如何使用`useCallback()`钩子避免重新渲染。

我希望你已经发现这个反应记忆的介绍是有用的！

## 分享这篇文章