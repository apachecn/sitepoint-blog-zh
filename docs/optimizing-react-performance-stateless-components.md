# 使用无状态组件优化 React 性能

> 原文：<https://www.sitepoint.com/optimizing-react-performance-stateless-components/>

![A React logo made of bones, on display in a museum alongside dinosaur fossils. A look at the evolution of stateless components.](img/e0ad27879e5ac1f323412d87be78077d.png)

这个故事是关于无状态组件 T2 的。这意味着组件中没有任何`this.state = { ... }`调用。他们只处理输入的“道具”和子组件。

## 首先，超级基础

```
import React, { Component } from 'react'

class User extends Component {
  render() {
    const { name, highlighted, userSelected } = this.props
    console.log('Hey User is being rendered for', [name, highlighted])
    return <div>  <h3
        style={{fontStyle: highlighted ? 'italic' : 'normal'}}
        onClick={event => {
          userSelected()
        }}
        >{name}</h3>  </div>
  }
} 
```

[https://codesandbox.io/embed/1wR1DBG00](https://codesandbox.io/embed/1wR1DBG00)

> 编者按:我们正在为本文中的演示试用 [CodeSandbox](https://codesandbox.io/) 。
> 让我们知道你的想法！

耶！它工作了。这很简单，但是树立了榜样。

需要注意的事项:

*   它是无国籍的。没有`this.state = { ... }`。
*   `console.log`就在那里，这样你就能了解它被使用的情况。特别是，当你做性能优化时，你会希望避免不必要的重新渲染，因为道具实际上并没有改变。
*   那里的事件处理程序是“内联的”。这是一个方便的语法，因为它的代码靠近它所处理的元素，而且这个语法意味着你不需要做任何仰卧起坐。
*   使用这样的内联函数，会有一点点性能损失，因为每次渲染都必须创建该函数。关于这一点，以后再说。

## 这是一种表象成分

我们现在意识到上面的组件不仅是无状态的，它实际上是丹·阿布拉莫夫所说的表示性的组件。它只是一个名字，但是基本上，它是轻量级的，产生一些 HTML/DOM，并且不涉及任何状态数据。

所以我们可以把它变成一个函数！耶！这不仅让人感觉“时髦”，而且也让它不那么可怕，因为它更容易推理。它获得输入，并且独立于环境，总是返回相同的输出。当然，它“回调”了，因为其中一个道具是可调用的函数。

所以，让我们重写一遍:

```
const User = ({ name, highlighted, userSelected }) => {
  console.log('Hey User is being rendered for', [name, highlighted])
  return <div>  <h3
      style={{fontStyle: highlighted ? 'italic' : 'normal'}}
      onClick={event => {
        userSelected()
      }}>{name}</h3>  </div>
} 
```

[https://codesandbox.io/embed/r026Mnm0w](https://codesandbox.io/embed/r026Mnm0w)

感觉是不是很棒？感觉就像纯 JavaScript，你可以不用考虑你使用的框架就能写出来。

## 他们说，它一直在重现

假设我们的小`User`用于一个状态随时间变化的组件中。但是状态不影响我们的分量。例如，类似这样的事情:

```
import React, { Component } from 'react'

class Users extends Component {
  constructor(props) {
    super(props)
    this.state = {
      otherData: null,
      users: [{name: 'John Doe', highlighted: false}]
    }
  }

  async componentDidMount() {
    try {
      let response = await fetch('https://api.github.com')
      let data = await response.json()
      this.setState({otherData: data})
    } catch(err) {
      throw err
    }
  }

  toggleUserHighlight(user) {
    this.setState(prevState => {
      users: prevState.users.map(u => {
        if (u.name === user.name) {
          u.highlighted = !u.highlighted
        }
        return u
      })
    })
  }

  render() {
    return <div>  <h1>Users</h1>  {
        this.state.users.map(user => {
          return <User
            name={user.name}
            highlighted={user.highlighted}
            userSelected={() => {
              this.toggleUserHighlight(user)
            }}/>
         })
      }  </div>
  }
} 
```

[https://codesandbox.io/embed/JZoMP7kyg](https://codesandbox.io/embed/JZoMP7kyg)

如果你运行这个，你会注意到我们的小组件得到了重新渲染，即使什么都没有改变！现在没什么大不了的，但是在实际的应用程序中，组件会越来越复杂，每次不必要的重新渲染都会导致站点变慢。

如果你现在用 [`react-addons-perf`](https://facebook.github.io/react/docs/perf.html) 调试这个应用，我敢肯定你会发现渲染`Users->User`是浪费时间。哦不！怎么办？！

一切似乎都指向这样一个事实:我们需要使用 [`shouldComponentUpdate`](https://facebook.github.io/react/docs/optimizing-performance.html#avoid-reconciliation) 来覆盖 React 认为道具不同的方式，而我们却确定它们不同。要添加 React 生命周期挂钩，组件需要成为一个类。*叹息*。所以我们回到最初的基于类的实现，并添加新的生命周期钩子方法:

## 回到类组件

```
import React, { Component } from 'react'

class User extends Component {

  shouldComponentUpdate(nextProps) {
    // Because we KNOW that only these props would change the output
    // of this component.
    return nextProps.name !== this.props.name || nextProps.highlighted !== this.props.highlighted
  }

  render() {
    const { name, highlighted, userSelected } = this.props
    console.log('Hey User is being rendered for', [name, highlighted])
    return <div>  <h3
        style={{fontStyle: highlighted ? 'italic' : 'normal'}}
        onClick={event => {
          userSelected()
        }}
        >{name}</h3>  </div>
  }
} 
```

[https://codesandbox.io/embed/oQBD6yynN](https://codesandbox.io/embed/oQBD6yynN)

请注意新增的`shouldComponentUpdate`方法。这有点难看。我们不仅不能再使用某个功能，还必须手动列出可能改变的道具。这涉及到一个大胆的假设，即`userSelected`函数属性不会改变。不太可能，但还是要小心。

但是请注意，这只会渲染一次！即使在包含的`App`组件重新呈现之后。所以，这对性能有好处。但是我们能做得更好吗？

## 反应呢。PureComponent？

从 React 15.3 开始，组件有了一个新的基类。它被称为`PureComponent`，它有一个内置的`shouldComponentUpdate`方法，对每个道具进行“浅相等”比较。太好了！如果我们使用这个，我们可以扔掉我们的自定义`shouldComponentUpdate`方法，其中必须列出具体的道具。

```
import React, { PureComponent } from 'react'

class User extends PureComponent {

  render() {
    const { name, highlighted, userSelected } = this.props
    console.log('Hey User is being rendered for', [name, highlighted])
    return <div>  <h3
        style={{fontStyle: highlighted ? 'italic' : 'normal'}}
        onClick={event => {
          userSelected()
        }}
        >{name}</h3>  </div>
  }
} 
```

[https://codesandbox.io/embed/v28o219yX](https://codesandbox.io/embed/v28o219yX)

尝试一下，你会失望的。每次都会重新渲染。为什么？！答案是因为函数`userSelected`每次都会在`App`的`render`方法中重新创建。这意味着当基于`PureComponent`的组件调用它自己的`shouldComponentUpdate()`时，它返回 true，因为每次创建的函数总是不同的。

一般来说，解决方法是将函数绑定到包含组件的构造函数中。首先，如果我们这样做，这意味着我们必须键入方法名 5 次(而以前是 1 次):

*   `this.userSelected = this.userSelected.bind(this)`(在构造函数中)
*   `userSelected() {`(作为方法定义本身)
*   `<User userSelected={this.userSelected} ...`(定义在哪里渲染`User`组件时)

另一个问题是，如您所见，当实际执行那个`userSelected`方法时，它依赖于一个闭包。特别是依赖于来自`this.state.users.map()`迭代器的作用域变量`user`。

不可否认，有一个解决方案，那就是首先将`userSelected`方法绑定到`this`，然后在调用该方法时(从子组件中)将用户(或其名称)传回。[这里有一个这样的解决方案](https://codesandbox.io/s/or3qN9Dz)。

## `recompose`来救援了！

首先，重复一下，我们想要的是:

1.  编写功能组件感觉更好，因为它们是功能。这立即告诉代码阅读器它没有保存任何状态。从单元测试的角度来看，它们很容易推理。他们觉得 JavaScript 更简洁、更纯粹(当然还有 JSX)。
2.  我们懒得绑定所有传递给子组件的方法。当然，如果这些方法很复杂，那么重构它们比动态创建它们更好。动态创建方法意味着我们可以在附近的*处编写代码，而不必给它们起名字，也不必在 3 个不同的地方重复 5 次。*
3.  除非子组件的道具发生变化，否则永远不要重新渲染子组件。这对于小而漂亮的应用程序来说可能无关紧要，但是对于现实世界的应用程序来说，当你有很多很多这样的应用程序时，当可以避免的时候，所有的过度渲染都会消耗 CPU。

(其实我们理想中想要的是组件只渲染一次。为什么 React 不能为我们解决这个问题？那么关于“如何快速反应”的博客帖子就会减少 90%。)

[重组](https://github.com/acdlite/recompose)是*“一个功能组件和高阶组件的 React 实用带。就像 lodash 代表 React 一样。”*根据文档。这个库中有很多值得探索的东西，但是现在我们想要渲染我们的功能组件，而不是在道具*不变的时候重新渲染它们。*

我们第一次尝试用`recompose.pure`把它重写回一个功能组件，看起来是这样的:

```
import React from 'react'
import { pure } from 'recompose'

const User = pure(({ name, highlighted, userSelected }) => {
  console.log('Hey User is being rendered for', [name, highlighted])
  return <div>  <h3
      style={{fontStyle: highlighted ? 'italic' : 'normal'}}
      onClick={event => {
        userSelected()
      }}>{name}</h3>  </div>
})

export default User 
```

[https://codesandbox.io/embed/pQ9PpyBnQ](https://codesandbox.io/embed/pQ9PpyBnQ)

你可能会注意到，如果你运行这个，`User`组件仍然会重新渲染，即使道具(`name`和`highlighted`键)没有改变。

让我们更进一步。我们将使用`recompose.onlyUpdateForKeys`,而不是使用`recompose.pure`,它是`recompose.pure`的一个版本，但是您可以明确地指定要关注的道具键:

```
import React from 'react'
import { onlyUpdateForKeys } from 'recompose'

const User = onlyUpdateForKeys(['name', 'highlighted'])(({ name, highlighted, userSelected }) => {
  console.log('Hey User is being rendered for', [name, highlighted])
  return <div>  <h3
      style={{fontStyle: highlighted ? 'italic' : 'normal'}}
      onClick={event => {
        userSelected()
      }}>{name}</h3>  </div>
})

export default User 
```

[https://codesandbox.io/embed/nZkV54x24](https://codesandbox.io/embed/nZkV54x24)

当你运行它时，你会注意到只有当道具`name`或`highlighted`改变时，它才会更新。如果父组件重新渲染，那么`User`组件不会。

万岁！我们找到金子了！

## 讨论

首先，问问自己是否值得优化组件的性能。也许这是多余的工作。无论如何，你的组件应该是轻量级的，也许你可以将任何昂贵的计算从组件中移出，或者将它们移到外部的可记忆函数中，或者也许你可以重新组织你的组件，这样当某些数据不可用时，你就不会浪费时间来呈现组件。例如，在这种情况下，您可能不想在`fetch`完成之前呈现`User`组件。

用对你来说最方便的方式写代码并不是一个坏的解决方案，然后启动你的东西，从那里开始，迭代使它更有性能。在这种情况下，为了提高性能，您需要重写功能组件的定义:

```
const MyComp = (arg1, arg2) => {
...
} 
```

…到…

```
const MyComp = pure((arg1, arg2) => {
...
}) 
```

理想情况下，所有这一切的最佳解决方案不是展示破解的方法，而是一个新的补丁 React，它是对`shallowEqual`的巨大改进，能够“自动”破译传入和比较的是一个函数，仅仅因为它不相等并不意味着它实际上是不同的。

**入场！**有一个折中的选择，可以避免在构造函数和每次重新创建的内联函数中搞乱绑定方法。而且是[公共类字段](https://www.peterbe.com/plog/public-class-fields)。这是 Babel 中的[特性，所以你的设置很可能支持它。例如，](https://babeljs.io/docs/plugins/preset-stage-2/)[这里有一个使用它的叉子](https://codesandbox.io/s/MVzwyMJB)，它不仅更短，而且现在还意味着我们不需要手动列出所有非功能道具。这个解决方案必须放弃闭包。尽管如此，当需要的时候，理解并意识到`recompose.onlyUpdateForKeys`还是有好处的。

> 关于 React 的更多信息，请查看我们的课程[以 ES6 的方式](https://www.sitepoint.com/premium/courses/react-the-es6-way-2914)做出反应。

这篇文章由杰克·富兰克林进行了同行评议。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！

## 分享这篇文章