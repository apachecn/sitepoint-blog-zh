# React Native 中的状态管理

> 原文：<https://www.sitepoint.com/state-management-in-react-native/>

管理状态是学习 React Native 时最难掌握的概念之一，因为有太多的方法可以做到这一点。npm 注册表上有无数的状态管理库——比如[Redux](https://www.npmjs.com/package/redux)——并且有无数的库构建在其他状态管理库之上以简化原始库本身——比如 [Redux Easy](https://www.npmjs.com/package/redux-easy) 。React 中每周都会引入一个新的状态管理库，但是自从引入 React 以来，维护应用程序状态的基本概念一直保持不变。

在 React Native 中设置状态最常见的方法是使用 React 的`setState()`方法。我们还有上下文 API 来避免适当的钻取，并将状态向下传递许多层，而不是传递给树中的单个子节点。

最近，钩子出现在 React 16 . 8 . 0 版本中，这是一种简化 React 中状态使用的新模式。React Native 在 [v0.59](https://facebook.github.io/react-native/blog/2019/03/12/releasing-react-native-059) 得到。

在本教程中，我们将了解状态实际上是什么，以及`setState()`方法、上下文 API 和 React 钩子。这是在 React Native 中设置状态的基础。所有的库都是建立在上述基本概念之上的。所以一旦你知道了这些概念，理解一个库或者创建你自己的状态管理库将会很容易。

*想从头开始学习 React Native？这篇文章是从我们的优质图书馆摘录的。使用 SitePoint Premium 获得 React 本地书籍的完整收藏，涵盖基础知识、项目、技巧和工具&。[现在就加入，每月仅需 9 美元](https://www.sitepoint.com/premium/products/Z2lkOi8vbGVhcm5hYmxlL1Byb2R1Y3QvMzA3?utm_source=blog&utm_medium=articles)。*

## 什么是国家？

任何随时间变化的事物都被称为状态。如果我们有一个计数器应用程序，状态就是计数器本身。如果我们有一个待办事项应用程序，待办事项列表会随着时间而变化，所以这个列表就是状态。甚至一个输入元素在某种意义上也是一种状态，因为它随着用户的输入而变化。

## setState 简介

现在我们知道了什么是状态，让我们来理解 React 是如何存储它的。

考虑一个简单的计数器应用程序:

```
import React from 'react'
import { Text, Button } from 'react-native'

class Counter extends React.Component {
    state = {
        counter: 0
    }

    render() {
        const { counter } = this.state
        return (
            <>
                <Text>{counter}</Text>
                <Button onPress={() => {}} title="Increment" />
                <Button onPress={() => {}} title="Decrement" />
            </>
        )
    }
} 
```

在这个应用程序中，我们将状态存储在一个对象的`constructor`中，并将其分配给`this.state`。

记住，状态只能是一个对象。你不能直接存储一个数字。这就是为什么我们在对象内部创建了一个`counter`变量。

在`render`方法中，我们从`this.state`中析构了`counter`属性，并将其呈现在`h1`中。请注意，目前它只会显示一个静态值(`0`)。

您也可以在构造函数之外编写状态，如下所示:

```
import React from 'react'
import { Text, Button } from 'react-native'

class Counter extends React.Component {
    state = {
        counter: 0
    }

    render() {
        const { counter } = this.state
        return (
            <>
                <Text>{counter}</Text>
                <Button onPress={() => {}} title="Increment" />
                <Button onPress={() => {}} title="Decrement" />
            </>
        )
    }
} 
```

现在让我们假设我们想要`+`和`-`按钮工作。我们必须在它们各自的`onPress`处理程序中编写一些代码:

```
import React from 'react'
import { Text, Button } from 'react-native'

class Counter extends React.Component {
    state = {
        counter: 0
    }

    render() {
        const { counter } = this.state
        return (
            <>
                <Text>{counter}</Text>
                <Button onPress={() => { this.setState({ counter: counter + 1 }) }} title="Increment" />
                <Button onPress={() => { this.setState({ counter: counter - 1 }) }} title="Decrement" />
            </>
        )
    }
} 
```

现在，当我们单击`+`和`-`按钮时，React 会重新渲染组件。这是因为使用了`setState()`方法。

`setState()`方法重新呈现树中已经改变的部分。在这种情况下，它会重新渲染`h1`。

因此，如果我们单击`+`，它会将计数器加 1。如果我们点击`-`，它会将计数器减 1。

记住不能通过改变`this.state`直接改变状态；做`this.state = counter + 1`不行。

同样，状态改变是异步操作，这意味着如果你在调用`this.setState`后立即读取`this.state`，它不会反映最近的改变。

这就是我们对`setState()`使用“函数作为回调”语法的地方，如下所示:

```
import React from 'react'
import { Text, Button } from 'react-native'

class Counter extends React.Component {
    state = {
        counter: 0
    }

    render() {
        const { counter } = this.state
        return (
            <>
                <Text>{counter}</Text>
                <Button onPress={() => { this.setState(prevState => ({ counter: prevState.counter + 1 })) }} title="Increment" />
                <Button onPress={() => { this.setState(prevState => ({ counter: prevState.counter - 1 })) }} title="Decrement" />
            </>
        )
    }
} 
```

“function as a callback”语法将最近的状态(在本例中为`prevState`)作为参数提供给`setState()`方法。

通过这种方式，我们可以获得状态的最新变化。

## 什么是钩子？

[钩子](https://reactjs.org/docs/hooks-intro.html)是 [React v16.8](https://reactjs.org/blog/2019/02/06/react-v16.8.0.html) 的新成员。以前，您只能通过创建类组件来使用状态。你不能在功能组件本身中使用状态。

通过增加钩子，您可以在功能组件本身中使用状态。

让我们将上面的`Counter`类组件转换成一个`Counter`功能组件，并使用 React 钩子:

```
import React from 'react'
import { Text, Button } from 'react-native'

const Counter = () => {
    const [ counter, setCounter ] = React.useState(0)
    return (
        <>
            <Text>{counter}</Text>
            <Button onPress={() => { setCounter(counter + 1 ) }} title="Increment" />
            <Button onPress={() => { setCounter(counter - 1 ) }} title="Decrement" />
        </>
    )
} 
```

注意，我们已经将`Class`组件从 18 行代码减少到 12 行。此外，代码更容易阅读。

让我们回顾一下上面的代码。首先，我们使用 React 内置的`useState`方法。`useState`可以是任何类型——比如数字、字符串、数组、布尔值、对象或任何类型的数据——不像`setState()`只能有一个对象。

在我们的反例中，它接受一个数字并返回一个包含两个值的数组。

数组中的第一个值是当前状态值。所以`counter`目前是`0`。

数组中的第二个值是允许您更新状态值的函数。

在我们的`onPress`中，我们可以直接使用`setCounter`更新`counter`。

因此，我们的增量函数变成了`setCounter(counter + 1 )`，我们的减量函数变成了`setCounter(counter - 1)`。

React 有很多内置的钩子，比如`useState`、`useEffect`、`useContext`、`useReducer`、`useCallback`、`useMemo`、`useRef`、`useImperativeHandle`、`useLayoutEffect`和`useDebugValue`——你可以在 [React 钩子文档](https://reactjs.org/docs/hooks-reference.html)中找到更多信息。

此外，我们可以构建自己的[定制钩子](https://reactjs.org/docs/hooks-custom.html)。

构建或使用钩子时有两条规则要遵循:

1.  **只调用顶层的钩子**。不要在循环、条件或嵌套函数中调用钩子。相反，总是在 React 函数的顶层使用钩子。通过遵循这条规则，您可以确保每次组件呈现时都以相同的顺序调用钩子。这使得 React 能够正确地保存多个`useState`和`useEffect`调用之间的钩子状态。

2.  **只从 React 函数**调用钩子。不要从常规的 JavaScript 函数中调用钩子。相反，您可以从 React 功能组件调用钩子，或者从自定义钩子调用钩子。

通过遵循这条规则，您可以确保组件中的所有有状态逻辑从其源代码中清晰可见。

钩子非常容易理解，在向功能组件添加状态时非常有用。

## 上下文 API

上下文提供了一种通过组件树传递数据的方式，而不必在每一层手动向下传递属性。

在典型的 React 本机应用程序中，数据是通过 props 自顶向下传递的。如果 React 应用程序中有多个级别的组件，并且组件树中的最后一个子组件想要从最顶层的父组件中检索数据，那么您必须单独向下传递 props。

考虑下面的一个例子。我们希望将`theme`的值从`App`组件传递给`Pic`组件。通常，在不使用上下文的情况下，我们会将它传递到每个中间层，如下所示:

```
const App = () => (
    <>
        <Home theme="dark" />
        <Settings />
    </>
)

const Home = () => (
    <>
        <Profile />
        <Timeline />
    </>
)

const Profile = () => (
    <>
        <Pic theme={theme} />
        <ChangePassword />
    </>
) 
```

`theme`的值从`App`->-`Home`->-`Profile`->-`Pic`。以上问题被称为。

这是一个简单的例子，但是考虑一个真实世界的应用程序，其中有几十个不同的级别。

很难将数据传递给每个子节点，以便在最后一个子节点中使用。因此，我们有上下文。

上下文允许我们直接从`App`->-T1 传递数据。

下面是如何使用上下文 API 来实现这一点:

```
import React from 'react'

const ThemeContext = React.createContext("light") // set light as default

const App = () => (
    <ThemeContext.Provider value="dark">
        <Home />
        <Settings />
    </ThemeContext.Provider>
)

const Home = () => (
    <>
        <Profile />
        <Timeline />
    </>
)

const Profile = () => (
    <ThemeContext.Consumer>
        {theme => (
            <Pic theme={theme} />
            <ChangePassword />
        )}
    </ThemeContext.Consumer>
) 
```

首先，我们使用`React.createContext` API 创建`ThemeContext`。我们将`light`设为默认值。

然后我们用`ThemeContext.Provider`包装我们的`App`组件的根元素，同时提供`theme`作为道具。

最后，我们使用`ThemeContext.Consumer`作为[渲染道具](https://reactjs.org/docs/render-props.html)来获得`theme`值作为`dark`。

render prop 模式很好，但是如果我们有多个上下文，那么它可能会导致回调地狱。为了将我们自己从回调地狱中拯救出来，我们可以使用钩子来代替`ThemeContext.Consumer`。

我们唯一需要改变的是`Profile`组件的实现细节:

```
const Profile = () => {
    const theme = React.useContext(ThemeContext)
    return (<>
            <Pic theme={theme} />
            <ChangePassword />
        </>
    )
} 
```

这样我们就不用担心回调地狱了。

## 跨组件共享状态

到目前为止，我们只管理组件本身的状态。现在我们来看看如何跨组件管理状态。

假设我们正在创建一个简单的待办事项应用程序，如下所示:

```
import { View, Text } from 'react-native'

const App = () => (
    <>
        <AddTodo />
        <TodoList />
    </>
)

const TodoList = ({ todos }) => (
    <View>
        {todos.map(todo => (
            <Text>
                {todo}
            </Text>)
        )}
    </View>
) 
```

现在，如果我们想从`AddTodo`组件添加一个待办事项，它将如何出现在`TodoList`组件的`todos`道具中？答案是“提升状态”。

如果两个兄弟组件想要共享状态，那么状态必须提升到父组件。完成的示例应该如下所示:

```
import { View, Text, TextInput, Button } from 'react-native'

const App = () => {
    const [ todos, setTodos ] = React.useState([])
    return (
        <>
            <AddTodo addTodo={todo => setTodos([...todos, todo])} />
            <TodoList todos={todos} />
        </>
    )
}

const AddTodo = ({ addTodo }) => {
    const [ todo, setTodo ] = React.useState('')
    return (
        <>
            <TextInput value={todo} onChangeText={value => setTodo(value)} />
            <Button
                title="Add Todo"
                onPress={() => {
                    addTodo(todo)
                    setTodo('')
                }}
            />
        </>
    )
}

const TodoList = ({ todos }) => (
    <View>
        {todos.map(todo => (
            <Text>
                {todo}
            </Text>)
        )}
    </View>
) 
```

这里，我们将状态保存在`App`组件中。我们使用 React 钩子`useState`将`todos`存储为一个空数组。

然后我们将`addTodo`方法传递给`AddTodo`组件，将`todos`数组传递给`TodoList`组件。

`AddTodo`组件接受`addTodo`方法作为道具。一旦按下`button`，就应该调用这个方法。

我们还有一个`TextInput`元素，它也使用 React 钩子`useState`来跟踪`TextInput`的变化值。

一旦`Button`被按下，我们就调用`addTodo`方法，它是从父`App`传递过来的。这确保了`todo`被添加到`todos`列表中。然后我们清空`TextInput`盒子。

`TodoList`组件接受`todos`并呈现一个给它的`todo`项目列表。

你也可以试着删除一个待办事项来练习自己提升状态。解决方案如下:

```
const App = () => {
    const [ todos, setTodos ] = React.useState([])
    return (
        <>
            <AddTodo addTodo={todo => setTodos([...todos, todo])} />
            <TodoList todos={todos} deleteTodo={todo => setTodos(todos.filter(t => t !== todo))} />
        </>
    )
}

const TodoList = ({ todos, deleteTodo }) => (
    <View>
        {todos.map(todo => (
            <Text>
                {todo} <Button title="x" onPress={() => deleteTodo(todo)} />
            </Text>)
        )}
    </View>
) 
```

这是 React 中最常见的做法。提升国家并不像看起来那么简单。这是一个简单的例子，但是在现实世界的应用程序中，我们不知道哪个状态需要提升到它的父级，以便在兄弟组件中使用。因此，首先，将状态保存在组件本身中，当出现必须在组件之间共享状态的情况时，只需将状态提升到父级。

这样你就不会让你的父组件成为一个大的状态对象。

## 结论

总之，我们看了什么是状态以及如何使用 React 提供的`setState()` API 设置状态的值。我们还研究了 React 挂钩，它使得向功能组件添加状态变得容易，而不必将其转换为类组件。

我们了解了新的上下文 API 及其钩子版本`useContext`，这有助于我们远离渲染道具回调地狱。

最后，我们学习了提升状态以在兄弟组件之间共享状态。

一旦理解了这些核心概念，React 就变得非常简单。记住尽可能将状态保持在组件的本地。仅当支柱钻孔成为问题时，才使用上下文 API。仅在需要时提升状态。

最后，一旦你的应用变得复杂，并且很难调试状态变化，那么看看状态管理库，比如 [Redux](https://redux.js.org/) 和 [MobX](https://mobx.js.org/) 。

## 分享这篇文章