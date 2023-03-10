# 探索类列表 API

> 原文：<https://www.sitepoint.com/exploring-classlist-api/>

自从 HTML 创建和第一个网站诞生以来，开发人员和设计人员一直试图定制他们页面的外观和感觉。这种需求变得如此重要，以至于人们创建了一个叫做 CSS 的标准来恰当地管理样式并将其与内容分开。在当今高度交互的网站中，您经常需要添加、删除或切换类名(通常称为“CSS 类”)。从历史上看，在 JavaScript 中处理这些变化有点复杂，因为没有内置的方法来执行这些操作。这种情况一直持续到 HTML5 引入 classList API。在本文中，我们将发现这个 API 是如何工作的以及它提供的方法。

注意:术语“CSS 类”通常用来指类名。这些是放在元素的`class`属性中的字符串。然而，有一篇[有趣的文章](http://tantek.com/2012/353/b1/why-html-classes-css-class-selectors)指出这个术语是不正确的，你应该避免使用。为了简洁起见，在本文中我将使用术语“类”作为“类名”的快捷方式。

## 什么是 classList API？

classList API 提供了管理 DOM 元素类名的方法和属性。使用它，我们可以执行诸如添加和删除类的操作，或者检查给定的类是否存在于元素中。classList API 通过 DOM 元素的一个名为`classList`的属性来公开这些方法和属性。该属性的类型为`DOMTokenList`，包含以下方法和属性:

*   `add(class1, class2, ...)`:将一个或多个类添加到元素的类列表中。
*   `contains(class)`:如果类列表包含给定的参数，返回`true`，否则返回`false`。
*   `item(index)`:返回位置`index`的类，如果个数大于或等于列表长度，则返回`null`。索引是从零开始的，这意味着第一个类名的索引是 0。
*   `length`:这是一个只读属性，返回列表中类的数量。
*   `remove(class1, class2, ...)`:从元素的类列表中删除一个或多个类。
*   `toString()`:以字符串形式返回元素的类列表。
*   `toggle(class[, force])`:从类列表中删除给定的类，返回`false`。如果该类不存在，则添加它，函数返回`true`。如果提供了第二个参数，它将根据类的真实性强制添加或删除类。例如，将该值设置为`true`会导致添加该类，而不管它是否已经存在。通过将该值设置为`false`，该类将被删除。

如果您熟悉 jQuery，您可能会认为`add()`和`remove()`方法通过传递一组空格分隔的类名(例如`add("red bold bigger")`)对多个类执行相同的操作。事实并非如此。要一次添加或删除更多的类，您必须为每个类传递一个字符串(例如`add("red", "bold", "bigger")`)。正如我指出的，`toggle()`方法有一个可选的参数，我们可以用它来强制一个给定的动作。换句话说，如果`toggle()`的第二个参数是`false`，则充当`remove()`方法；如果第二个参数是`true`，它充当`add()`方法。

现在我们已经描述了这个 API 的方法和属性，让我们看看它的一些实际例子。假设页面上存在以下 HTML 元素，下面显示的每个代码示例将执行一个操作。

```
<span id="element" class="description"></span>
```

### 添加类别

要将类名“red”添加到元素的`class`属性中，我们可以编写以下代码:

```
document.getElementById('element').classList.add('red');
// class="description red"
```

要添加多个类，例如“红色”和“粗体”，我们可以这样写:

```
document.getElementById('element').classList.add('red', 'bold');
// class="description red bold"
```

注意，如果所提供的类之一已经存在，就不会再添加了。

### 删除类

要删除一个类，例如“description ”,我们将编写以下代码:

```
document.getElementById('element').classList.remove('description');
// class=""
```

为了一次删除多个类，我们编写:

```
document.getElementById('element').classList.remove('description', 'red');
// class=""
```

请注意，如果提供的某个命名类不存在，不会引发错误。

### 切换课程

有时我们需要根据用户交互或站点状态来添加或删除类名。这是使用`toggle()`方法完成的，如下所示。

```
document.getElementById('element').classList.toggle('description');
// class=""

document.getElementById('element').classList.toggle('description');
// class="description"
```

### 检索类

classList API 提供了一种基于类名在类列表中的位置来检索类名的方法。假设我们想要检索元素的第一个和第三个类。我们将编写以下内容:

```
document.getElementById('element').classList.item(0);
// returns "description"

document.getElementById('element').classList.item(2);
// returns null
```

### 正在检索类的数量

虽然不是很常见，但有些情况下我们可能需要知道应用于给定元素的类的数量。classList API 允许我们通过如下所示的`length`属性检索这个数字:

```
console.log(document.getElementById('element').classList.length);
// prints 1
```

### 确定类是否存在

有时，我们可能希望基于某个类的存在来执行给定的操作。为了执行测试，我们以如下方式使用`contains()`方法:

```
if (document.getElementById('element').classList.contains('description')) {
   // do something...
} else {
   // do something different...
}
```

### 以字符串形式返回类列表

要以字符串的形式返回类的列表，我们可以使用`toString()`方法，如下所示。

```
console.log(document.getElementById('element').classList.toString());
// prints "description"

document.getElementById('element').classList.add('red', 'bold');
console.log(document.getElementById('element').classList.toString());
// prints "description red bold"
```

## 浏览器兼容性

除了 Internet Explorer 之外，classList API 在桌面和移动浏览器中得到广泛支持。IE 从版本 10 开始支持这个 API。更具体地说，你可以在 Chrome 8+、Firefox 3.6+、Internet Explorer 10+、Safari 5.1+和 Opera 11.5+中使用这个 API。正如我们已经看到的，classList API 非常简单，正如您可能猜到的那样，多填充它并不困难。创建自己的 polyfill 应该很简单，但是如果你想要已经存在的东西，你可以使用 Eli Grey 的 classList.js。

## 演示

本节提供了一个简单的演示，允许您尝试本文中解释的概念。演示页面包含两个基本字段:一个包含 API 公开的方法和属性的`select`元素，以及一个我们可以编写要传递的参数的文本框。正如您将看到的，这个演示没有显式地调用这些方法，而是使用了一个简单的技巧(使用 JavaScript `apply()`方法)，从而减少了代码行。因为一些浏览器不支持 API，我们执行检查，如果失败，我们显示消息“API 不支持”。如果浏览器支持 classList API，我们为按钮的`click`事件附加一个监听器，这样一旦点击，我们就执行选择的方法。

代码的现场演示可从[这里](http://aurelio.audero.it/demo/classlist-api-demo.html)获得。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>ClassList API Demo</title>
    <style>
      body
      {
        max-width: 500px;
        margin: 2em auto;
        font-size: 20px;
      }

      h1
      {
        text-align: center;
      }

      .hidden
      {
        display: none;
      }

      .field-wrapper
      {
        margin-top: 1em;
      }

      #log
      {
        height: 200px;
        width: 100%;
        overflow-y: scroll;
        border: 1px solid #333333;
        line-height: 1.3em;
      }

      .button-demo
      {
        padding: 0.5em;
        margin: 1em;
      }

      .author
      {
        display: block;
        margin-top: 1em;
      }
    </style>
  </head>
  <body>
    <h1>ClassList API</h1>

    <h3>Live sample element</h3>
    <div id="showcase">
      &lt;span id="play-element" class="description"&gt;&lt;/span&gt;
    </div>

    <h3>Play area</h3>
    <div>
      <div class="field-wrapper">
      <label for="method">Methods and Properties:</label>
        <select id="method">
          <option value="add">add()</option>
          <option value="contains">contains()</option>
          <option value="item">item()</option>
          <option value="length">length</option>
          <option value="remove">remove()</option>
          <option value="toString">toString()</option>
          <option value="toggle">toggle()</option>
        </select>
      </div>
      <div class="field-wrapper">
        <label for="parameter">Parameters (use spaces for multiple parameters):</label>
        <input type="text" id="parameter"></input>
      </div>

      <button id="execute" class="button-demo">Execute</button>
    </div>

    <span id="d-unsupported" class="hidden">API not supported</span>

    <h3>Log</h3>
    <div id="log"></div>
    <button id="clear-log" class="button-demo">Clear log</button>

    <span id="play-element" class="description"></span>

    <script>
      if (!'classList' in document.createElement('span')) {
        document.getElementById('c-unsupported').classList.remove('hidden');
        document.getElementById('execute').setAttribute('disabled', 'disabled');
      } else {
        var playElement = document.getElementById('play-element');
        var method = document.getElementById('method');
        var parameter = document.getElementById('parameter');
        var log = document.getElementById('log');
        var showcase = document.getElementById('showcase');

        document.getElementById('clear-log').addEventListener('click', function() {
          log.innerHTML = '';
        });

        document.getElementById('execute').addEventListener('click', function() {
          var message = method.value;

          if (method.value === 'length') {
            message += ': ' + playElement.classList[method.value]
          } else {
            var result = playElement.classList[method.value].apply(playElement.classList, parameter.value.split(' '));

            showcase.textContent = playElement.outerHTML;

            if (method.value === 'add' || method.value === 'remove' || method.value === 'toggle') {
              message += ' class "' + parameter.value + '"';
            } else {
              message += ': ' + result;
            }
          }

          log.innerHTML = message + '<br />' + log.innerHTML;
        });
      }
    </script>
  </body>
</html>
```

## 结论

在本文中，我们已经了解了 classList API、它的方法和属性。正如我们所看到的，这个 API 帮助我们管理分配给给定元素的类——它非常容易使用。这个 API 在桌面和移动浏览器中得到广泛支持，所以我们可以安全地使用它(如果需要，可以借助 polyfill)。最后要注意的是，不要忘记通过演示来更好地掌握这个 API 及其功能。

## 分享这篇文章