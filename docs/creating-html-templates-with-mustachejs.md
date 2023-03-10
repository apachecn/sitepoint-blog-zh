# 用 Mustache.js 创建 HTML 模板

> 原文：<https://www.sitepoint.com/creating-html-templates-with-mustachejs/>

Web 应用程序使用 MVC 架构将业务逻辑从表示视图中分离出来。包含大量使用 JavaScript 的客户端 HTML 操作的复杂项目可能难以维护。在这种情况下，我们可以使用模板系统来增加可重用性，并简化管理视图的任务。js 提供了一个很好的模板系统，可以用来管理你的模板。而且，由于 mustache 支持各种语言，我们在服务器端不需要单独的模板系统。本文涵盖了使用 mustache 的基础知识。

## 为什么我们需要模板系统

大多数不了解模板系统的开发人员创建新的 HTML 代码块，并使用 JavaScript 动态地将它们插入 DOM。一种常见的方法是在字符串中指定 HTML 元素，然后设置`innerHTML`属性或调用 jQuery `html()`方法。这种技术的一个例子如下所示。

```
var dynamic_html = "<div><span>Highlighted</span><span>Author</span></div>";

document.getElementByID("container").innerHTML = dynamic_html;
```

构建 DOM 的另一种方法是创建元素并单独追加它们，如下所示。

```
var title = document.createElement('div');
var highlight = document.createElement('span');
var highlight_text = document.createTextNode("Highlight");
var author = document.createElement('span');
var author_text = document.createTextNode("Author");
var container = document.getElementById('container');

highlight.appendChild(highlight_text);
title.appendChild(highlight);
author.appendChild(author_text);
title.appendChild(author);
container.appendChild(title);
```

以上两种方法都可以有效地将元素动态添加到文档中。考虑这样一种情况，我们有一个设计非常好的项目符号列表，需要在我们站点的三种不同类型的页面上使用。使用这些技术，我们将不得不在三个不同的位置重复列表的 HTML 代码。这通常被认为是糟糕的编码实践。

在这种情况下，我们可以在不同的位置使用预定义的模板，而无需重复代码。Mustache.js 是一个非常流行的使用 JavaScript 的模板引擎。由于 mustache 为多种语言提供了服务器端和客户端模板，我们不必担心选择单独的模板引擎。

## Mustache.js 入门

Mustache 是一个为 JavaScript、Ruby、Python、PHP 和 Java 等语言开发的开源无逻辑模板系统。你可以通过访问 GitHub 上的[官方页面获得该库的副本。Mustache 提供模板和视图作为创建动态模板的基础。视图包含要作为 JSON 包含在模板中的数据。模板包含带有模板标签的表示 HTML 或数据，用于包含视图数据。之前，我们提到小胡子是没有逻辑的。这意味着模板将不包含任何`if-else`条件或`for`循环。现在，让我们用一个简单的例子开始小胡子模板。](https://github.com/janl/mustache.js)

```
<!doctype html>
<html lang="en">
  <head>
    <title>Mustache.js Inline Method</title>
    <script type="text/javascript" src="mustache.js" ></script>
    <script>
      var view = {
        name : "Joe",
        occupation : "Web Developer"
      };

      function loadtemp(){
        var output = Mustache.render("{{name}} is a  {{occupation}}", view);
        document.getElementById('person').innerHTML = output;
      }
    </script>
  </head>
  <body onload="loadtemp()" >
    <p id="person"></p>
  </body>
</html>
```

首先，我们需要在文档中包含 mustache.js 文件。然后我们可以创建小胡子模板。在上面的例子中，我们有一个包含一个人的姓名和职业的视图。然后，我们在`render()`函数中有一个模板，它带有名称和职业数据的表示代码和标签。标签由包围它们的双括号或八字胡来表示。现在，让我们看看`render()`方法是如何工作的。

### 渲染小胡子模板

下面的代码展示了 mustache.js 文件中`render()`函数的实现。三个参数可以传递给`render()`。前两个参数`template`和`view`是强制的。`partials`可以被认为是动态模板，你可以把它注入到你的主模板中。在前面的例子中，我们将模板作为内联参数传递，将视图作为第二个参数传递，并将结果赋给`output`变量。

```
Writer.prototype.render = function (template, view, partials) {
  return this.compile(template)(view, partials);
};
```

这是用小胡子做模板的最基本形式。让我们看看创建更有组织的代码的其他方法。

## 定义小胡子模板

在应用程序中定义 mustache 模板有多种方法。这些方法类似于使用内嵌样式、内嵌样式表和外部样式表来包含 CSS。我们之前讨论的例子可以被认为是一个内联方法，因为我们将模板直接传递给函数。这种方法防止了可重用模板的可能性。让我们看看如何将模板定义为内联脚本模板，而不是直接传递给函数。

### 作为内联脚本的模板

我们可以在一个`<script>`标签中定义模板数据，并将其包含在 HTML 文档中。为了防止浏览器执行模板代码，我们必须将 MIME 类型改为不同于`text/javascript`的类型。一些常见的哑剧类型有`text/html`、`text/template`和`text/mustache`。下面的示例是一个使用此方法的简单模板。

```
<script id="sample_template" type="text/html">
<h1>{{name}}</h1>
</script>
```

您可以在一个文档中包含任意多个带有不同 id 的模板。当您想要使用模板时，使用`innerHTML`获取脚本标记内的 HTML，并将其作为模板传递。我们的第一个例子将改为下面的代码。

```
<script type='text/javascript'>
  var template = document.getElementById('sample_template').innerHTML;
  var output = Mustache.render(template, view);
  document.getElementById('person').innerHTML = output;
</script>
```

如您所见，模板是单独存储的，需要时会动态使用。这种方法增加了重用模板的可能性。但是，使用内联脚本会将模板的范围限制在单个页面上。如果您有多个页面，您必须再次定义您的模板。因此，在外部文件中包含模板将是理想的解决方案——就像 CSS 一样。

### 作为外部 HTML 片段的模板

在这项技术中，我们将使用 jQuery 来实现模板。jQuery 提供了一个名为`load()`的函数，可以用来获取外部文档的一部分。我们将使用这个方法从外部模板文件中动态加载模板。`load()`函数执行脚本而不是返回它们，所以我们不能像在前面的方法中那样在脚本标签中创建模板。以下示例显示了我们将要使用的外部模板文件。

```
<div id="template1" >
<h1>{{name}}</h1>
</div>

<div id="template2" >
<div>{{name}}</div>
</div>

<div id="template3" >
<p><span>{{name}}</span></p>
</div>
```

我们对模板使用了`<div>`元素，而不是脚本，以保持它与 jQuery 的`load()`函数兼容。这里，我们有三个不同的模板，有三个不同的 id。现在，让我们继续在您的页面中使用这些模板。

```
<!doctype html>
<html lang="en">
  <head>
    <title>Mustache.js External Method</title>
    <script type="text/javascript" src="jquery.js" ></script>
    <script type="text/javascript" src="mustache.js" ></script>
    <script>
      $(document).ready(function(){
        var view = {
          name : "Joe",
          occupation : "Web Developer"
        };

        $("#templates").load("template.html #template1",function(){
          var template = document.getElementById('template1').innerHTML;
          var output = Mustache.render(template, view);
          $("#person").html(output);
        });
      });
    </script>
  </head>
  <body>
    <p id="person"></p>
    <div id="templates" style="display: none;"></div>
  </body>
</html>
```

jQuery 将返回的文档插入到 HTML 元素中，而不是将其赋给变量。因此，我们需要一个虚拟容器来保存模板。我使用了默认隐藏的`templates`容器。上面的例子检索`template1`，并加载它。然后，我们可以从虚拟容器中获取模板，并将其传递给 mustache 进行渲染。这就是外部方法的工作方式。我们还可以使用 AJAX 请求从服务器获取数据。

## 结论

模板引擎和框架对于管理具有动态变化的表示视图的复杂系统非常重要。Mustache.js 是在客户端管理模板的最佳选择之一。我们在本教程开始时解释了为什么模板很重要。然后，我们转向使用小胡子模板的各种技术。现在您将能够选择在您的项目中实现 mustache 模板的方法。

我们已经探索了使用 mustache 模板的各种技术，但是 mustache 还附带了变量、节、函数和部分等标签，用于管理复杂的模板。讨论每个标记的语法超出了本教程的范围。你可以在 [mustache GitHub 页面](https://github.com/janl/mustache.js)上找到关于 mustache 标签的综合指南。

可以随意分享一下之前和 mustache.js 合作的经验！

## 分享这篇文章