# 使用 HTML5 数据集 API 管理自定义数据

> 原文：<https://www.sitepoint.com/managing-custom-data-html5-dataset-api/>

很长一段时间以来，web 开发人员都需要在 DOM 元素上存储数据。最常见的方法之一是添加数据作为类名。像我这样的纯粹主义者总是觉得这样做是错误的，因为那不是存放数据的地方。另一种方法是向感兴趣的元素添加自定义属性。这种做法会导致无效的标记，因为规范不支持自定义属性。所以，你最终为了达到你的目标而牺牲了认可。这种情况非常令人沮丧。还好 HTML5 修好了。事实上，HTML5 不仅引入了通过*数据属性*添加自定义属性的可能性，而且还公开了一个 API，称为数据集 API，来处理它们。在本文中，我们将发现这个 API 是如何工作的，以及它能为我们做什么。

## 什么是数据集 API？

在所有新元素(如`article`、`section`、`header`和`footer`)和新 API(如[高分辨率时间](https://www.sitepoint.com/discovering-the-high-resolution-time-api/)、[用户计时](https://www.sitepoint.com/discovering-user-timing-api/)、 [getUserMedia](https://www.sitepoint.com/introduction-getusermedia-api/) 和[页面可见性](https://www.sitepoint.com/introduction-to-page-visibility-api/))中，HTML5 还引入了*数据属性*和数据集 API。在深入讨论数据集 API 之前，我想让您快速回顾一下什么是数据属性。

数据属性的名称来源于前缀`data-`。这也解释了为什么有时它们被称为`data-*`属性。使用数据属性的元素示例如下所示。

```
<span id="element" data-level="1" data-points="100" data-opponent="Dragon"></span>
```

你可以选择的名字不限于一个单词。名称也可以由多个单词组成，用连字符(`-`)分隔。所以，让我们假设您想将`opponent`属性更改为`final opponent`。您应该编写如下例所示的元素。

```
<span id="element" data-level="1" data-points="100" data-final-opponent="Dragon"></span>
```

现在您应该对什么是数据属性有了一个清晰的概念，所以让我们开始讨论 API。数据集 API 为我们提供了一种处理数据属性的简单方法。这个 API 允许我们设置、获取甚至删除数据属性值。dataset API 公开了一个名为`dataset`的 DOM 元素属性，其中包含一个`DOMStringMap`对象。这个对象的键是不带`data-`前缀的数据属性的名称。对应的值是数据属性的值。如果属性的名称由多个单词组成，并用连字符分隔，则它会被转换为 camelCase。让我们看看下面的例子:

```
var obj = document.getElementById("element").dataset
```

前面的语句将变量`obj`中的以下对象。

```
{
  level: "1",
  points: "100",
  finalOpponent: "Dragon"
}
```

可以使用`setAttribute()`、`getAttribute()`和`removeAttribute()`方法访问各个数据属性*。然而，数据集 API 为您提供了一种方便而直接的方式来访问定制数据。如果 API 不被支持，您应该检索所有属性，然后过滤掉那些不以`data-`开头的属性。此外，虽然数据集 API 更简单，但它也比前面提到的方法慢，正如 [this JSperf](http://jsperf.com/dataset-vs-attributes-loop/3) 所证明的。然而，除非您每秒访问数千个属性，否则您不会注意到任何差异。*

现在我们已经讨论了数据集 API，是时候看看如何使用它了。

### 设置值

假设我们想要将属性`data-media`添加到元素中，并将其值设置为`song`。为了执行这项任务，我们可以编写以下代码。请注意，如果已经定义了该属性，其值将被覆盖。

```
document.getElementById("element").dataset.media = "song";
```

### 获取值

如果我们不能检索属性，那么创建属性是完全没有用的。假设我们想要将`data-final-opponent`属性的值打印到控制台。这样做的代码会是这样的:

```
console.log(document.getElementById("element").dataset.finalOpponent);
// prints "Dragon"
```

### 删除属性

要删除一个值，只需使用空字符串覆盖它。然而，要真正删除一个属性，我们可以使用 JavaScript `delete`操作符。删除`data-final-opponent`属性的示例如下所示。

```
delete document.getElementById("element").dataset.finalOpponent;
```

在执行前面的语句后，尝试检索属性的值将产生`undefined`。

## 浏览器兼容性

除了只在 IE11 中实现 API 的 Internet Explorer 之外，dataset API 在桌面和移动浏览器中得到广泛支持。此外，还有一些较老的移动浏览器不支持它，但总的来说支持非常好。对于那些不支持这个 API 的浏览器，可以使用一个名为 [HTML 5 数据集支持](http://code.eligrey.com/html5/dataset/latest/html5-dataset.js)的聚合填充。如果不想为这么简单的 API 添加 polyfill，可以使用`setAttribute()`、`getAttribute()`和`removeAttribute()`(如前所述)。

## 演示

学习新东西很好，但如果我们能玩它就更好了。因此，我们将构建一个小演示，让我们看看数据集 API 是如何工作的。总的想法是有一个元素，我们可以在其上获取、设置和删除数据属性。为了查看正在发生的事情并关注元素的当前状态，我们将有一个小窗口，在这里我们将记录我们所做的更改。此外，我们将有一个区域显示代表其当前状态的元素的原始 HTML 代码。

为了使用它的数据属性，我们需要两个输入框:键和值。前者允许我们设置想要存储的属性的名称，而后者是我们写入属性值的地方。因为我们希望允许三种不同的操作(get、set 和 delete)，所以我们还需要三个按钮来添加处理程序。像往常一样，我们还将测试浏览器支持，如果测试失败，我们将显示消息“不支持 API”

在向您展示演示代码之前，我想与您分享两个注意事项。演示假设您已经阅读了整篇文章。因此，您知道要对用多个单词命名的数据属性执行操作，必须将名称转换为 camelCase。如果您想改变这种行为，并能够编写“最终对手”而不是“最终对手”，我已经为您编写了两个实用函数。要使用这些函数，您需要将它们添加到演示中，并更改代码，以便在执行操作之前在处理程序中调用它们。下面列出了这些函数的源代码。

```
function hyphenToCamelCase(string) {
  return string.replace(/-([a-z])/g, function(string) {
    return string[1].toUpperCase();
  });
}

function camelCaseToHyphen(string) {
  return string.replace(/([A-Z])/g, function(string) {
    return '-' + string.toLowerCase();
  });
}
```

要记住的第二件事是，到目前为止，我们已经使用点运算符访问了数据属性，因为我们提前知道了它的名称。在演示中我们没有这个信息，所以要访问`dataset`的属性，我们将使用等价的方括号符号。

现在，是时候向您展示源代码了。像往常一样，代码的现场演示可以在[这里](http://aurelio.audero.it/demo/dataset-api-demo.html)获得。

```
<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>Dataset API Demo</title>
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

      #log
      {
        height: 200px;
        width: 100%;
        overflow-y: scroll;
        border: 1px solid #333333;
        line-height: 1.3em;
      }

      .buttons-demo-wrapper
      {
        text-align: center;
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
    <h1>Dataset API</h1>

    <h3>Live sample element</h3>
    <div id="showcase">
      &lt;span id="play-element" class="hidden" data-level="1" data-points="100" data-final-opponent="Dragon"&gt;&lt;/span&gt;
    </div>

    <h3>Play area</h3>
    <div>
      <label for="key">Key:</label>
      <input type="text" id="key"></input>
      <label for="value">Value:</label>
      <input type="text" id="value"></input>

      <div class="buttons-demo-wrapper">
        <button id="set-data" class="button-demo">Set data</button>
        <button id="get-data" class="button-demo">Get data</button>
        <button id="delete-data" class="button-demo">Delete data</button>
      </div>
    </div>

    <span id="d-unsupported" class="hidden">API not supported</span>

    <h3>Log</h3>
    <div id="log"></div>
    <button id="clear-log" class="button-demo">Clear log</button>

    <span id="play-element" class="hidden" data-level="1" data-points="100" data-final-opponent="Dragon"></span>

    <script>
      if (!"dataset" in document.createElement("span")) {
        document.getElementById("d-unsupported").classList.remove("hidden");
        ["set-data", "get-data", "delete-data"].forEach(function(elementId, index) {
          document.getElementById(elementId).setAttribute("disabled", "disabled");
        });
      } else {
        var playElement = document.getElementById("play-element");
        var key = document.getElementById("key");
        var value = document.getElementById("value");
        var log = document.getElementById("log");
        var showcase = document.getElementById("showcase");

        document.getElementById("clear-log").addEventListener("click", function() {
          log.innerHTML = "";
        });
        document.getElementById("set-data").addEventListener("click", function() {
          if (key.value.indexOf("-") !== -1) {
            log.innerHTML = "Warning! Hyphen not allowed. Use camelCase instead.\n" + log.innerHTML;
          } else {
            playElement.dataset[key.value] = value.value;
            showcase.textContent = playElement.outerHTML;
            log.innerHTML = "Set data-" + key.value + " attribute to '" + value.value + "'<br />" + log.innerHTML;
          }
        });
        document.getElementById("get-data").addEventListener("click", function() {
          if (key.value.indexOf("-") !== -1) {
            log.innerHTML = "Warning! Hyphen not allowed. Use camelCase instead.<br />" + log.innerHTML;
          } else {
            log.innerHTML = "Get data-" + key.value + " attribute. Value: '" + playElement.dataset[key.value] + "'<br />" + log.innerHTML;
          }
        });
        document.getElementById("delete-data").addEventListener("click", function() {
          if (key.value.indexOf("-") !== -1) {
            log.innerHTML = "Warning! Hyphen not allowed. Use camelCase instead.<br />" + log.innerHTML;
          } else {
            delete playElement.dataset[key.value];
            showcase.textContent = playElement.outerHTML;
            log.innerHTML = "Deleted data-" + key.value + " attribute<br />" + log.innerHTML;
          }
        });
      }
    </script>
  </body>
</html>
```

## 结论

在本文中，我们讨论了数据集 API 以及它如何帮助我们在 DOM 元素上存储自定义属性。正如我们所看到的，API 非常容易使用。此外，桌面和移动浏览器也广泛支持它。您可以在下一个项目中立即开始使用它。如果你有任何疑问，我鼓励你玩提供的演示或张贴一个问题。

## 分享这篇文章