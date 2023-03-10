# 如何在 React 中处理和操作状态

> 原文：<https://www.sitepoint.com/work-with-and-manipulate-state-in-react/>

以下节选自[React quick](https://www.manning.com/books/react-quickly?utm_source=sitepoint&utm_campaign=article&utm_medium=website&utm_term=react_quickly&utm_content=sitepoint_article_feb_2017)，这是一本由 Azat Mardan 为任何想快速学习 React.js 的人准备的动手书。

当在 React 中访问、处理和操纵状态时，知道自己在做什么(以及为什么这么做)是很重要的！).在本文中，您将学习 React 中的状态以及如何使用它。我们还将讨论状态和道具之间的一些区别，以及如何使用“无状态”组件。但是在我们深入研究所有这些之前，为了处理状态，我们需要知道如何访问值，更新它们以及如何设置初始值。让我们从访问 React 组件中的状态开始。

本文中示例的源代码可以在本书的 GitHub 资源库的 [ch04 文件夹中找到。](https://github.com/azat-co/react-quickly)

## 访问状态

状态对象是组件的一个属性，可以通过`this`引用访问，如`this.state.name`。我们可以在 JSX 用花括号`{}`访问和打印变量。类似地，我们可以在`render()`中呈现`this.state`(作为任何其他变量或自定义组件类属性)。比如`{this.state.inputFieldValue}`。这个语法类似于用`this.props.name`访问属性。

让我们继续尝试实现一个时钟(图 1)。我们的目标是拥有一个自包含的组件类，任何人都可以在他们的应用程序中导入和使用它，而不必经历重重困难。时钟必须显示当前时间。

![The clock displaying the current time](img/3f1a7465ddea56ad9cf5a6dfee1fc041.png)

***图 1:时钟组件以数字格式显示当前时间——每秒更新一次***

时钟项目的结构如下:

```
/clock
  - index.html
  /jsx
    - script.jsx
    - clock.jsx
  /js
    - script.js
    - clock.js
    - react-15.0.2.js
    - react-dom-15.0.2.js 
```

我使用带有手表`-w`和目录标志`-d`的 Babel CLI 将所有 JSX 源文件从`clock/jsx`编译到目标文件夹`clock/js`，并在更改时重新编译。此外，我将该命令作为 npm 脚本保存在父文件夹`ch04`中的`package.json`文件中，以便从`ch04`运行`npm run build-clock`:

```
"scripts": {
    "build-clock": "./node_modules/.bin/babel clock/jsx -d clock/js -w"
}, 
```

很明显，时间总是在变的(好的或坏的)。因此，我们需要使用状态更新视图。我们将其命名为`currentTime`,并尝试呈现如清单 1 所示的状态。

```
class Clock extends React.Component {
  render() {
    return <div>{this.state.currentTime}</div>
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('content')
) 
```

***清单 1:渲染状态在 JSX***

如果我们运行这个，我们会得到下面的错误:`Uncaught TypeError: Cannot read property 'currentTime' of null`。通常情况下，JavaScript 错误消息对溺水的人来说就像一杯冷水一样有用。好在在这种情况下，JavaScript 给了我们一个有用的错误消息。这意味着我们对`currentTime`没有任何值。与道具不同，状态不是在父对象上设置的。我们也不能在`render()`中`setState`，因为它会创建一个循环(setState>render>setState…)循环，在这种情况下，React 会抛出一个错误。

## 设置初始状态

您已经看到，在使用`render()`中的状态数据之前，我们必须对其进行初始化。要设置初始状态，在构造函数中使用带有 ES6 类`React.Component`语法的`this.state`。不要忘了用属性调用`super()`，否则 parent ( `React.Component`)中的逻辑将无法工作。

```
class MyFancyComponent extends React.Component {
  constructor(props) {
    super(props)
    this.state = {...}
  }
  render() {
    ...
  }
} 
```

开发人员可以在设置初始状态时添加其他逻辑。例如，我们可以使用`new Date()`设置`currentTime`的值。我们甚至可以使用`toLocaleString()`来获取用户所在地的正确日期和时间格式:

```
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.state = {currentTime: (new Date()).toLocaleString()}
  }
  ...
} 
```

***清单 2:时钟组件构造器(ch04/clock)***

`this.state`的值必须是一个对象。我们不会涉及太多关于 ES6 `constructor()`的细节，因为在 [ES6 备忘单](https://github.com/azat-co/cheatsheets/tree/master/es6)中有相关信息。要点是，和其他 OOP 语言一样，当这个类的实例被创建时，`constructor()`被调用。构造函数方法名必须是`constructor`。把它想象成一个 ES6 惯例。此外，**如果你创建了一个`constructor()`方法，你几乎总是需要调用它里面的`super()`**，否则父构造函数将不会被执行。另一方面，如果你没有定义一个`constructor()`方法，那么就假设调用`super()`。

### 类别属性

希望 TC39(ECMAScript 标准背后的人)在 ECMAScript 的未来版本中将属性添加到类语法中！这样，开发人员不仅可以在构造函数中设置状态，还可以在类的主体中设置状态:

```
class Clock extends React.Component {
  state = {
    ...
  }
} 
```

该提议被称为[类实例字段或类属性](https://github.com/tc39/proposal-class-public-fields)，但截至 2016 年 7 月，它只适用于 transpilers: Babel，Traceur 或 TypeScript，这意味着没有浏览器会在本地运行该功能。在 [ECMAScript 兼容性表](http://kangax.github.io/compat-table/esnext/)中检查类属性的当前兼容性。

这里，`curentTime`是一个任意的名称，我们以后在访问和更新这个状态时需要使用相同的名称。如果以后使用这个名称引用您的州，您可以随意命名它。

状态对象可以有嵌套的对象或数组。看看这个例子，我把我的书的数组添加到状态:

```
class Content extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      githubName: 'azat-co',
      books: [
        'pro express.js',
        'practical node.js',
        'rapid prototyping with js'
      ]
    }
  }
  render() {
    ...
  }
} 
```

当从此类创建一个 React 元素时，将调用一次`constructor()`方法。这样，我们可以在`constructor()`方法中使用`this.state`直接设置状态。避免在其他地方直接用`this.state = ...`设置和更新状态，因为这可能会导致意想不到的后果。

> 使用 React 自己的`createClass()`方法来定义组件，您将需要使用`getInitialState()`。

这样只会让我们得到第一个价值，这个价值很快就过时了；一秒钟后。不显示当前时间的钟有什么意义？幸运的是，有一种方法可以更新状态。

## 更新状态

我们用`this.setState(data, callback)`类方法改变状态。当这个方法被调用时，React 将数据与当前状态合并，并调用`render()`。之后，反应过来的来电`callback`。

在`setState()`中有回调是很重要的，因为这些方法是异步工作的。如果您依赖于新状态，可以使用回调来确保这个新状态可用。如果你依赖于一个新的状态而不等待`setState()`完成它的工作，即与异步操作同步工作，那么当状态还是旧状态时，你可能会有一个 bug。

我们已经从一个状态渲染了时间，我们也设置了初始状态，但是我们需要每秒更新一次时间，对吗？我们可以使用浏览器定时器函数`setInterval()`，它将每隔 *n* 毫秒执行一次状态更新。几乎所有现代浏览器都将`setInterval()`方法作为一个全局方法来实现，这意味着开发人员可以不用任何库或前缀来使用它。

```
setInterval(()=>{
  console.log('Updating time...')
  this.setState({
    currentTime: (new Date()).toLocaleString()
  })
}, 1000) 
```

为了启动时钟，我们需要调用一次`setInterval()`。我们可以创建一个方法`launchClock()`来做到这一点。我们将在构造函数中调用`launchClock()`。最终的时钟可能如清单 3 所示。

***清单 3:用 React 状态和`setInterval()` (ch04/clock/jsx/clock.jsx)实现时钟。***

```
class Clock extends React.Component {
  constructor(props) {
    super(props)
    this.launchClock()                                      <1> this.state = {
      currentTime: (new Date()).toLocaleString()            <2> }
  }
  launchClock() {
    setInterval(()=>{
      console.log('Updating time...')
      this.setState({
        currentTime: (new Date()).toLocaleString()          <3> })
    }, 1000) <4> }
  render() {
    console.log('Rendering Clock...')
    return <div>{this.state.currentTime}</div>              <5> }
} 
```

**< 1 >** 触发`launchClock()`
**< 2 >** 将初始状态设置为当前时间
**< 3 >** 每秒用当前时间更新状态
**< 4 >** 绑定上下文引用组件实例
**< 5 >** 呈现状态

您可以在任何地方使用`setState()`，而不仅仅是在`launchClock()`(由`constructor`调用)中，如示例所示。通常，`setState()`是从事件处理程序中调用的，或者作为传入数据或数据更新的回调。

> 像这样改变代码中的状态值不会有任何好处。它不会触发我们想要的重新渲染和可能的 DOM 更新。在大多数情况下，不使用`setState`直接改变状态是一种反模式，应该避免。

需要注意的是`setState()`只更新你传递给它的状态(部分或合并，但不是完全替换)。它不会每次都替换整个状态对象。如果你有三个状态，然后改变一个，其他两个保持不变。在下面的例子中，`userEmail`和`userId`将保持不变:

```
constructor(props) {
  super(props)
  this.state = {
    userName: 'Azat Mardan',
    userEmail: 'hi@azat.co',
    userId: 3967
  }
}
updateValues() {
  this.setState({userName: 'Azat'})
} 
```

如果您打算更新所有三个状态，那么您需要通过将这些状态的新值传递给`setState()`来显式地做到这一点。另一个方法是`this.replaceState()`方法，它有时会出现在旧的 React 代码中，但现在已经不再有效，并且被弃用。正如您从名称中可以猜到的，它用它的所有属性替换了整个状态对象。

记住`setState()`触发`render()`。它在大多数情况下都是有效的。在一些边缘场景中，当代码依赖于外部数据时，您可以使用`this.forceUpdate()`来触发重新呈现，但是应该避免这种方法，因为它依赖于外部数据，而不是状态，这使得组件更加脆弱，并且依赖于外部因素(紧耦合)。

如前所述，可以用`this.state`访问状态对象。如果你还记得，我们用花括号(`{}`)输出值；因此，在视图中声明一个状态属性(`render`的`return`语句)，应用`this.state.NAME`。

当您在视图中使用状态数据(例如，在`if/else`中作为属性值打印，或者作为子属性值打印)然后给`setState()`新值时，React 神奇地发生了。嘣！React 会为您更新 HTML。您可以在 DevTools 控制台中观察它。它应该显示*更新…* 然后*渲染…* 的循环，最好的部分是只有最少的 DOM 元素受到影响。

### 用 JavaScript 绑定它

在 JavaScript 中，`this`根据调用函数的位置改变它的值。为了确保`this`引用我们的组件类，我们需要将函数绑定到适当的上下文(这个值是我们的组件类)。

如果你像我在这里一样使用 ES6+/ES2015+,那么你可以使用[粗箭头函数语法](https://www.sitepoint.com/javascript-arrow-functions/)来创建一个带有自动绑定的函数:

```
setInterval(()=>{
  this.setState({
    currentTime: (new Date()).toLocaleString()
  })
}, 1000) 
```

自动绑定意味着用粗箭头创建的函数将获得`this`的当前值，在我们的例子中是`Clock`。

手动方法是在闭合上使用`bind(this)`方法；

```
function() {...}.bind(this) 
```

或者我们的时钟:

```
setInterval(function(){
  this.setState({
    currentTime: (new Date()).toLocaleString()
  })
}.bind(this), 1000) 
```

这种行为并不仅限于反应。`this`关键字在函数的闭包内发生了变异，我们需要绑定它或者保存上下文(`this`)值以备后用。通常，我们会看到像`self`、`that`或`_this`这样的变量被用来保存原始`this`的值。你们中的大多数人可能都见过类似下面的陈述:

```
var that = this
var _this = this
var self = this 
```

这个想法很简单；您创建一个变量并在闭包中使用它，而不是引用`this`。新变量不是一个副本，而是对原始值`this`的引用。这里是我们的`setInterval()`:

```
var _this = this
setInterval(function(){
  _this.setState({
    currentTime: (new Date()).toLocaleString()
  })
}, 1000) 
```

我们有我们的时钟，它工作正常(图 2)。Tadaaa！

![The clock is ticking](img/f7bcf3f8f9f7163bbf675b992d175e0b.png)

***图二:时钟在滴答作响***

在我们继续之前还有一件事。您可以看到 React 是如何重用同一个 DOM `<div>`元素，并且只修改其中的文本。继续使用 DevTools 来修改这个元素的 CSS。我添加了一个样式使文本变成蓝色(`color: blue`)，如图 3 所示。它创建了一个内联样式，而不是一个类。随着时间的推移，该元素及其新的内嵌样式保持不变(蓝色)。

![React updates the time as text, not the div element](img/280d03828690c3595e148280f02fe24a.png)

***图 3: React 将时间更新为文本，而不是 div 元素(手动添加颜色:蓝色)***

React 只会更新内部 HTML(第二个`<div>`容器的内容)。这个页面上的`<div>`和**所有其他元素，保持不变**。干净利落。；-)

## 状态和属性

状态和属性都是类的属性，这意味着它们是`this.state`和`this.props`。这是唯一的相似之处！属性和状态的主要区别之一是前者是不可变的，而后者是可变的。

属性和状态之间的另一个区别是，我们从父组件传递属性，因为我们在组件本身而不是其父组件中定义状态。这里的原则是，您只能从父组件中更改属性的值，而不能更改组件本身。属性决定了创建时的视图，然后它们保持静态(不变)。另一方面，状态由对象本身设置和更新。

属性和状态服务于不同的目的，但是它们都可以作为组件类的属性来访问，并且都可以帮助开发人员用不同的表示(视图)来组成组件。当涉及到组件生命周期时，道具和状态之间存在许多差异。将道具和状态视为产生不同输出的函数的输入。这些输出是视图。对于每组属性和状态，您可以有不同的 ui(视图)(图 4)。

![New values for props and states can change the UI](img/e9d4de0a99dd7ce367869e8a9b7fc025.png)

***图 4:属性和状态的新值可以改变用户界面，但是属性的新值来自父元素，状态的新值来自组件本身***

并非所有组件都需要有状态。让我们看看如何在无状态组件中使用属性。

## 无状态组件

无状态组件的概念是没有状态、没有组件、也没有任何其他反应生命周期事件/方法的组件。无状态组件的目的是呈现视图。它唯一能做的就是获取属性并对它们做一些事情——一个有输入(属性)和输出(UI 元素)的简单函数。

使用无状态组件的好处是它们是可预测的，因为我们有一个决定输出的输入。可预测性意味着它们更容易理解、维护和调试。事实上，没有状态是最理想的 React 实践——您使用的无状态组件越多，它们越少“有状态”就越好。

这个 Hello World 脚本是无状态组件的一个很好的例子(清单 4):

```
class HelloWorld extends React.Component {
  render() {
    return <h1 {...this.props}>Hello {this.props.frameworkName} World!!!</h1>
  }
} 
```

***清单 4(ch03/hello-js-world-jsx/jsx/script . jsx)***

为了让无状态组件有一个更小的语法，React 为我们提供了一个函数样式。我们创建一个将属性作为参数并返回视图的函数。无状态组件的呈现方式类似于任何其他组件。例如，HelloWorld 组件可以重写为返回`<h1>`的函数:

```
const HelloWorld = function(props){
  return <h1 {...props}>Hello {props.frameworkName} world!!!</h1>
} 
```

**注:**是的。您可以对无状态组件使用 ES6+/ES2015+箭头函数。下面的代码片段类似于上面的代码片段(return 也可以省略，但我喜欢使用它):

```
const HelloWorld = (props)=>{
  return <h1 {...props}>Hello {props.frameworkName} world!!!</h1>
} 
```

可以看出，当不需要状态时，开发人员也可以将函数定义为 React 组件。要创建无状态组件，只需将其定义为一个函数。`Link`是无状态组件的另一个例子:

```
function Link (props) {
  return <a href={props.href} target="_blank" className="btn btn-primary">{props.text}</a>
}

ReactDOM.render(
  <Link text='Buy React Quickly' href='https://www.manning.com/books/react-quickly'/>,
  document.getElementById('content')
) 
```

不需要自动绑定，但是为了简洁，我们可以使用胖箭头函数语法(当只有一条语句时，符号可以是一行代码):

```
const Link = props=> <a href={props.href} target="_blank" className="btn btn-primary">{props.text}</a> 
```

在无状态组件中，我们不能有状态，但是可以有两个属性:`propTypes`和`defaultProps`。我们把它们放在物体上:

```
function Link (props) {
  return <a href={props.href} target="_blank" className="btn btn-primary">{props.text}</a>
}
Link.propTypes = {...}
Link.defaultProps = {...} 
```

我们也不能在无状态函数中使用引用(`refs`)。如果需要使用`refs`，可以在普通的 React 组件中封装一个无状态组件。

## 结论

在本文中，我介绍了 React 中的状态，并演示了如何使用它。我已经研究了状态和道具之间的一些差异，以及如何使用所谓的无状态组件。

这就是目前的全部内容——希望这已经让您对在 React 中使用状态有了更好的理解。想了解更多关于 React 及其无数用途的信息，请查阅这本书:[快速反应](https://www.manning.com/books/react-quickly?utm_source=sitepoint&utm_campaign=article&utm_medium=website&utm_term=react_quickly&utm_content=sitepoint_article_feb_2017)。

## 分享这篇文章