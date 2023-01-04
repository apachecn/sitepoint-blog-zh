# jide.js 简介

> 原文：<https://www.sitepoint.com/introduction-jide-js/>

jide.js 是一个创建现代网络应用的新工具包。它由一组有用的控件和所有工具组成，您需要这些工具来创建自己的特定于应用程序的组件。jide.js 完全支持 AMD (require.js ),允许你只挑选那些你真正需要的部分。从版本 1.0.0-beta3 开始，您还可以在 Browserify 中使用它。

jide.js 的核心是围绕可观察值、事件发射器和数据绑定构建的。它利用现代浏览器(IE9+)的特性来创建可靠的跨平台体验，并利用当前 JavaScript 语言的特性，而不是依赖过去。

## 介绍核心概念

在我们开始创建实际的 jide.js 应用程序之前，让我解释几个核心概念。

### 可观察值

可观察性是保持应用程序各部分同步的好方法。jide.js 允许您订阅这些观察值，并在它们的值发生变化时接收通知。这方面的一个例子如下所示。

```
require(['jidejs/base/Observable'], function(Observable) {
  var counter = Observable(0);

  counter.subscribe(function(event) {
    console.log('counter changed', event.value);
  });

  counter.set(1);
  // invokes the subscribed listener and prints to console
});
```

在创建 jide.js 应用程序时，您可以从许多可观察对象中进行选择，例如`ObservableProperty`、`Observable.computed`和`ObservableList`。一个计算出的可观测值可以依赖于其它的可观测值，当它的一个依赖关系改变时，它将被重新计算。下面显示了一个创建计算可观察值的示例。

```
var counterText = Observable.computed(function() {
  return 'You clicked the button ' + counter.get() + ' times!';
});
```

如果你订阅了`counterText`，一旦`counter`发生变化，你就会得到通知。需要注意的一点是，默认情况下，以这种方式创建的计算可观测量是懒惰的。这意味着除非需要，否则不会计算它们的值。传递给订阅者的事件可能不包含值。

### 实例化控件

jide.js 中的所有控件都有相同的构造函数签名。它们都只需要一个参数，一个配置对象。继续前面的例子，下面是如何创建一个按钮，它的标签绑定到我们之前创建的`counterText` observable，并且每当它被点击时就增加`counter`。

```
var myButton = new Button({
  // bind the "text" property of the button to the "counterText" observable
  text: counterText,
  // we can add event listeners inline
  on: {
    // we use the "action" event instead of the "click" event
    // to support keyboard invocation, etc.
    action: function() {
      // increment the counter
      counter.set(counter.get() + 1);
    }
  }
});

// add the button to the document – you'd rarely do it this way, but it works
document.body.appendChild(myButton.element);
```

### 控件、外观和模板

jide.js 允许你以任何你想要的方式创建你的应用程序，但是建议的方式是在你的自定义控件中使用一个清晰的关注点分离。这就是我们在示例中要做的。

在 jide.js 中，每个控件都应该包含它需要显示的属性。例如，一个按钮应该有一个`text`和一个`icon`属性。除此之外，jide.js 中的每个控件都有一个皮肤，负责构建控件的内部 DOM 结构，包括事件处理程序，并将数据注入 DOM。

如果您想充分利用 jide.js，您可以将 DOM 创建和 DOM 与控件之间的数据绑定以及控件的皮肤留给一个模板。在这种情况下，您的皮肤应该只包含对模板重要的事件处理程序和自定义属性。这是我们将在接下来的介绍中使用的方法。

## 创建 jide.js 应用程序

启动一个新的 jide.js 项目最简单的方法是使用 [Yeoman](http://yeoman.io) 生成器。[约曼](http://yeoman.io)要求你的电脑上已经安装了 [node.js](http://nodejs.org) 和 [npm](http://npmjs.org) 。完成后，在终端窗口中运行以下命令:

```
npm install –g yeoman
npm install –g generator-jidejs
yo jidejs
```

给你的应用取一个你喜欢的名字，对事件总线说“不”。一旦 Yeoman 完成了项目的创建，您就可以通过键入`grunt serve`来查看它。这将启动一个服务器并打开您的浏览器，以便您可以开始使用您的应用程序。您的新应用程序支持实时重新加载，这意味着当您编辑源文件时，您的浏览器将自动刷新。

现在，您应该看到一个非常基本的应用程序，它显示了一个可以输入您的姓名的文本字段和一个按钮。当你点击按钮时，应用程序会向你致意。

### 向应用程序添加任务列表

接下来，打开项目目录中的`app/page/IndexPage.js`文件，并将其更改为:

```
define([
  'jidejs/base/Class',
  'jidejs/base/ObservableList',
  'jidejs/ui/Control',
  'jidejs/ui/Skin',
  'text!app/view/indexPage.html'
], function(
  Class, ObservableList, Control, Skin,
  IndexPageTemplate
) {
  function IndexPage(config) {
    this.tasks = ObservableList(config.tasks || []);
    delete config.tasks;
    Control.call(this, config);
    this.classList.add('page');
    this.classList.add('index');
  }

  Class(IndexPage).extends(Control);

  IndexPage.Skin = Skin.create(Skin, {
    template: IndexPageTemplate,

    addTask: function() {
      this.queryComponent('x-name').then(function(nameField) {
        this.component.tasks.add({
          name: nameField.text
        });

        nameField.text = '';
      }.bind(this));
    },

    deleteTask: function(task) {
      this.component.tasks.remove(task);
    }
  });

  return IndexPage;
});
```

您刚才应用的更改非常简单。您已经向`IndexPage`控件添加了一个新属性`tasks`。`tasks`由配置参数填充。这里很棒的一点是，由于您使用的是`ObservableList`，当您在列表中添加或删除项目时，UI 会自动更新。

现在，我们需要更改`app/view/indexPage.html`来实际显示我们的任务列表。将文件内容更改为:

```
<template>
  <input type="text" pseudo="x-name" bind="
    is: 'jidejs/ui/control/TextField',
    on: {
      action: addTask.bind($item)
    }
  ">
  <button bind="
    is: 'jidejs/ui/control/Button',
    on: {
      click: addTask.bind($item)
    }
  " text="Add"></button>
  <ul bind="
    foreach: component.tasks
  ">
    <template>
      <li>
        <span bind="text: name"></span>
        <a bind="
          is: 'jidejs/ui/control/Hyperlink',
          text: 'Delete',
          on: {
            action: $parent.deleteTask.bind($parent, $item)
          }
        ">Delete</a>
      </li>
    </template>
  </ul>
</template>
```

jide.js 中的模板允许你使用*数据绑定*将标准的 HTML 元素升级到 jide.js 控件。要绑定一个元素，只需要给它添加一个`bind`属性。该属性中的语法是一个文字 JavaScript 对象(没有左括号和右括号)。

通过指定一个`is`绑定，我们将元素升级到命名控件。它的值必须是作为控件的有效 AMD 模块的名称。我们可以使用`on`绑定将事件监听器添加到 HTML 元素或 jide.js 控件中。

绑定表达式中有一些特殊变量。我们这里用的是`$item`和`$parent`。对于外部的`template`元素，`$item`变量指的是`IndexPage`的皮肤。在内部的`template`元素中，`$parent`是指皮肤，`$item`是指当前的任务对象。

`foreach`绑定允许我们迭代一个项目集合，并为它迭代的每个项目复制它的`template`子节点。如果分配给它的值是一个`ObservableList`，它将根据`ObservableList`的变化自动添加、删除和修改它的子节点。

## 结论

正如你刚刚看到的，jide.js 为开发者提供了很多。通过使用可观察值，您可以停止编写事件侦听器，只需将数据绑定到 UI。创建定制控件不仅简单，而且有效地实施了关注点的清晰分离。

本文只是对 jide.js 的一个简要介绍。jide.js 内置了许多其他控件来帮助您创建应用程序，而呈现的样式只是一种方式。如果你不喜欢使用模板，那也没关系。例如，您可以使用`filter`、`map`和`sort`等操作来创建原始数据的实时更新变体。

要了解更多关于 jide.js 的信息，请访问[项目网站](http://js.jidesoft.com)，在那里您可以找到大量的示例、演示和指南。你还会被定向到该项目的 [GitHub 库](https://github.com/jidesoft/jidejs)。

## 分享这篇文章