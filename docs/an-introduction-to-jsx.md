# JSX 简介

> 原文：<https://www.sitepoint.com/an-introduction-to-jsx/>

本指南将带您了解什么是 JSX，我们为什么使用它，以及如何开始使用它。

## 什么是 JSX？

JSX (JavaScript 语法扩展或 JavaScript XML)是 JavaScript 的扩展。它提供了一种在 React 中创建 UI 组件的更简单的方法。

下面是它的语法示例:

```
const element = <h1>Hello, World!</h1>;
```

等号后面的不是字符串或 HTML，而是 JSX。它不直接呈现给 HTML，而是呈现给 React 类。

JSX 就是我们所说的[句法糖](https://en.wikipedia.org/wiki/Syntactic_sugar)。一种纯粹为编程而创建的语法，对于开发人员来说更容易阅读和编写——让生活更美好。在幕后，JSX 只是用传统的`React.createElement((component, props, ...children)))`方法将上面的代码变成了一个更加冗长的类:

```
React.createElement(
  "h1",
  null,
  "Hello, World!"
  )
) 
```

React 团队不仅仅推荐 JSX，因为它可以节省您的时间。它还可以帮助将标记和逻辑组合成组件，与 [TypeScript](https://www.sitepoint.com/react-with-typescript-best-practices/) 一起提供更严格的[数据类型](https://www.sitepoint.com/typing-versus-dynamic-typing/)，并提高安全性。

## 它是如何工作的？

JSX 仍然只是一些额外功能的 JavaScript。使用 JSX，您可以编写看起来非常类似于 HTML 或 XML 的代码，但是您可以无缝地将 JavaScript 方法和变量混合到您的代码中。JSX 由 transpiler 解释，比如 [Babel](https://babeljs.io/) ，并被渲染成 UI 框架(这里是 React)可以理解的 JavaScript 代码。

*注意:你可以使用[巴别塔 REPL](https://babeljs.io/en/repl) 将下面的例子转换成普通的 JavaScript。*

不喜欢 JSX？太酷了。这在技术上是不需要的，React 文档实际上包含了一个关于在没有 JSX 的情况下使用 React 的章节。不过，我现在要警告你，这可不太妙。不相信我？看一看。

**JSX:**

```
class SitePoint extends Component {
  render() {
    return (
      <div>My name is <span>{this.props.myName}</span></div>
    )
  }
} 
```

**反应无 JSX:**

```
class SitePoint extends Component {
  render() {
    return React.createElement(
      "div",
      null,
      "My name is",
      React.createElement(
        "span",
        null,
        this.props.myName
      )
    )
  }
} 
```

当然，看着那页上那些小的示例代码片段，你可能会想，“哦，那并不坏，我可以这么做。”但是你能想象像那样写一个完整的应用程序吗？

这个例子只是两个简单的嵌套 HTML 元素，没什么特别的。基本上，只是一个嵌套的`Hello World`等价物。尝试在没有 JSX 的情况下编写 React 应用程序会非常耗时，如果你像我们大多数其他开发者一样在 DevLand 中工作，这很可能会很快变成错综复杂的代码混乱。呸！

使用框架和库之类的东西是为了让我们的生活更容易，而不是更难。我相信我们在职业生涯中都见过对库或框架的过度使用和滥用，但在 React 中使用 JSX 绝对不是这种情况。

## 为什么用 JSX？

JSX 是个好主意有几个原因:

它的进入门槛很低。JSX 是目前最接近普通 HTML 和 CSS 的。使用 JSX，您可以轻松地在模板中嵌入 JavaScript 片段，而不必学习额外的模板语言，也不必处理复杂的抽象层次。任何熟悉 HTML、CSS 和 JavaScript 的人阅读和理解 JSX 模板应该没有问题。

**打字稿支持**。TypeScript 支持带有类型检查的 TSX 文件的编译。这意味着，如果您在元素或属性类型的名称上犯了错误，编译将会失败，您将会看到一条错误消息指出您的错误。流行的 ide 如 VS Code 也支持 TSX 文件，并提供代码补全、重构和其他有用的特性。

**安全**。JSX 处理通常的输出净化问题，以防止诸如跨站脚本之类的攻击。

## 关注点分离怎么样？

人们普遍认为 HTML 和 JavaScript 的混合违反了神圣的关注点分离原则。这个判断假设 HTML(负责表示)和 JavaScript(负责应用程序和业务逻辑)之间有明确的界限。然而，这种分离是基于技术，而不是基于他们的关注。负责呈现项目列表的 JavaScript 仍然属于表示层，应该更靠近模板而不是应用程序的其他部分。

通过使用 JavaScript，我们接受了这样一个事实:边界不应该画在 HTML 和 JavaScript 之间，而应该画在表示逻辑和应用程序逻辑之间，而不考虑两边使用的语言。关注这一点可以防止我们将表示性 JavaScript 与业务逻辑混淆，从而加强关注点的分离，减少耦合，并提高可维护性。

## 将 JSX 与 React 一起使用

让我们从基础开始。如前所述，在浏览器中呈现 JSX 之前，需要将其转换成常规的 JavaScript。因此，如果您希望跟随示例，您应该已经设置了一个 React 应用程序。

下面的例子都附带了 **runnable CodePen demos** ，所以如果你想快速浏览代码，这可能是一个不错的选择。

否则，你可以选择脸书的 [Create React App](https://www.sitepoint.com/create-react-app/) 工具。要使用它，您需要安装 Node 和 npm。如果您还没有，请访问 Node.js [下载页面](https://nodejs.org/en/download/)，获取您系统的最新版本(npm 与 Node 捆绑在一起)。或者，你可以参考我们的教程[使用版本管理器](https://www.sitepoint.com/quick-tip-multiple-versions-node-nvm/)安装节点。

安装 Node 后，您可以创建一个新的 React 应用程序，如下所示:

```
npx create-react-app myapp 
```

这将创建一个`myapp`文件夹。切换到这个文件夹并启动开发服务器，如下所示:

```
cd myapp
npm start 
```

您的默认浏览器将打开，您将看到新的 React 应用程序。出于本教程的目的，您可以在位于`src/App.js`的`App`组件中工作。

现在让我们进入一些代码。

### 基本表达

JSX 与普通 HTML 极其相似，使用相同的基于 XML 的语法。下面是典型的“Hello，World”示例:

```
const element = <h1>Hello, World!</h1>;

ReactDOM.render(element, document.getElementById('root')); 
```

参见 [CodePen](https://codepen.io) 上 SitePoint([@ SitePoint](https://codepen.io/SitePoint))
的笔[-jsx-hello-world](https://codepen.io/SitePoint/pen/OJXPZPq)。