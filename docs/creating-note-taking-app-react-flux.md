# 使用 React 和 Flux 创建笔记应用程序

> 原文：<https://www.sitepoint.com/creating-note-taking-app-react-flux/>

脸书的 React 是一个非常好的创建用户界面的库。唯一的问题是 React 不关心应用程序如何处理数据。大多数人用 React 作为 MV*中的 **V** 。因此，脸书引入了一种叫做 Flux 的模式，它为应用程序内部的数据处理带来了一种功能性的方法。本教程简要介绍了 Flux 模式，并展示了如何使用 React 和 Flux 架构创建一个笔记应用程序。

## 焊剂入门

Flux 依赖于单向数据流。通量模式中有两个关键部分:

1.  **Stores**:store 组件，顾名思义，存储应用数据。
2.  **动作**:新的数据通过动作流入商店。存储监听动作，并在动作被调用时执行一些任务(例如修改数据)。这使数据流保持单向。

为了强调这个概念，让我们举一个真实世界的例子。例如，在笔记制作应用程序中，您可以有以下安排:

1.  一个名为`NoteStore`的存储，它存储了一个笔记列表。
2.  你可以有一个动作叫做`createNote`。商店`NoteStore`监听动作`createNote`,并在调用动作时用新的注释更新它的列表。数据只能通过操作流入存储区。
3.  每当数据改变时,`NoteStore`触发一个事件。您的 React 组件，比如说`NoteListComponent`，监听这个事件并更新视图上显示的注释列表。这就是数据流出商店的方式。

因此，数据流可以如下所示:

![Data Flow Diagram](img/21bea8c5c35ef2c7327e3ebd5f320756.png "Data Flow Diagram")

通量模式的最大优点是它使您的应用程序数据保持平坦。因为突变只能通过动作来完成，所以更容易理解数据变化如何影响整个应用程序。

**注:**

如果你读过脸书关于[通量](https://facebook.github.io/flux/)的指南，你可能会注意到调度员的概念。调度程序是存储回调的注册表。当一个动作被调用时，Dispatcher 对其做出响应，并将相关数据发送给所有注册的存储。然后，商店检查行动类型，并相应地执行任务。

上面的过程被一个叫做**回流**的库大大简化了。它通过使操作可列表化，消除了调度程序的概念。因此，在回流存储可以直接监听行动，并响应他们的调用。

为了充分理解通量模式，让我们用 Reflux、React 和 Node.js 构建一个简单的笔记应用程序。

## 设置开发环境

我们将使用 use React 和 Reflux 作为节点模块，并使用 Browserify 使它们在客户端也可用。这是我们设置环境的方式:

1.  我们将使用 Browserify 将我们的 React 组件、动作和存储捆绑到一个客户端`.js`包中。
2.  我们将使用`grunt watch`来检测上述组件的变化，并在每次发生变化时重新运行 Browserify。
3.  `grunt nodemon`用于在任何`.jsx`或`.js`文件改变时重启服务器，这样你就不必手动重启了。

你可以从 [GitHub](https://github.com/jsprodotcom/source/blob/master/react-note-app.zip) 下载代码，打开`Gruntfile.js`阅读任务。一旦您的机器上有了 repo，您就可以运行`npm install`来安装所需的节点模块。运行以下命令并开始开发:

```
grunt watch
grunt nodemon
```

该应用程序可在`https://localhost:8000`访问，其工作方式如下:

[https://quick.as/embed/6jyqiwbz](https://quick.as/embed/6jyqiwbz)