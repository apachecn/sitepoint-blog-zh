# 车把初学者指南

> 原文：<https://www.sitepoint.com/a-beginners-guide-to-handlebars/>

如今，大部分 Web 由动态应用程序组成，其中的数据经常变化。因此，需要不断更新浏览器上呈现的数据。这就是 JavaScript 模板引擎的用武之地，它变得如此有用。它们简化了手动更新视图的过程，同时通过允许开发人员将业务逻辑与代码的其余部分分离，它们改进了应用程序的结构。一些最著名的 JavaScript 模板引擎是 Mustache、[下划线](http://underscorejs.org/)、 [EJS](http://embeddedjs.com/) 和[手柄](http://handlebarsjs.com/)。在这篇文章中，我们将通过讨论把手的主要特征来关注把手。

## 车把:它是什么，为什么要使用它

[Handlebars](http://handlebarsjs.com/) 是一个无逻辑的模板引擎，可以动态生成你的 HTML 页面。它是小胡子的延伸，增加了一些额外的功能。Mustache 是完全无逻辑的，但是 Handlebars 由于使用了一些助手(比如`if`、`with`、`unless`、`each`等等)而增加了最少的逻辑，我们将在本文中进一步讨论。事实上，我们可以说车把是小胡子的超集。

手柄可以像任何其他 JavaScript 文件一样加载到浏览器中:

```
<script src="/path/to/handlebars.min.js"></script>
```

如果你问为什么你应该使用这个模板引擎而不是另一个，你应该看看它的优点。下面是一个简短的列表:

*   它保持了 HTML 页面的整洁，并将无逻辑的模板与 JavaScript 文件中的业务逻辑分开，从而改进了应用程序的结构(以及它的可维护性和可伸缩性)
*   它简化了手动更新视图数据的任务
*   在 [Ember.js](http://emberjs.com/) 、 [Meteor.js](https://www.meteor.com/) 、 [Derby.js](http://derbyjs.com/) 和 [Ghost](https://ghost.org/) 等热门框架和平台中都有使用

我希望这个简短的回顾能帮助你决定是否值得使用车把。

## 它是如何工作的？

![how handlebars works](img/1784f98660e2bffe504f44585318d7bb.png)

如上图所示，车把的工作方式可以总结如下:

1.  Handlebars 获取一个包含变量的模板，并将其编译成一个函数
2.  然后通过传递一个 JSON 对象作为参数来执行这个函数。这个 JSON 对象被称为 context，它包含模板中使用的变量的值
3.  在执行时，该函数在用相应的值替换模板变量后返回所需的 HTML

为了理解上述过程，让我们从一个演示开始，它详细解释了上述所有步骤。

## 模板

模板可以在 HTML 文件中编写，也可以单独编写。在第一种情况下，它们出现在带有属性和 ID 的标签中。变量写在双花括号`{{}}`中，称为表达式。这里有一个例子:

```
<script id="handlebars-demo" type="text/x-handlebars-template"> <div>
      My name is {{name}}. I am a {{occupation}}.
   </div> </script>
```

有了这个标记，我们就可以看到我们必须使用它做什么了。在您的 JavaScript 文件中，我们首先需要从 HTML 文档中检索模板。在下面的例子中，我们将使用模板的 ID 来实现这个目的。获取模板后，我们可以使用返回函数的`Handlebars.compile()`方法来编译它。然后通过将上下文作为参数传递来执行该函数。执行完成后，该函数返回所需的 HTML，所有变量都替换为相应的值。此时，我们可以将 HTML 注入到我们的网页中。

将此描述转换成代码会产生以下代码片段:

```
// Retrieve the template data from the HTML (jQuery is used here).
var template = $('#handlebars-demo').html();

// Compile the template data into a function
var templateScript = Handlebars.compile(template);

var context = { "name" : "Ritesh Kumar", "occupation" : "developer" };

// html = 'My name is Ritesh Kumar. I am a developer.'
var html = templateScript(context);

// Insert the HTML code into the page
$(document.body).append(html);
```

这段代码的现场演示可以在这个 [Codepen 演示](http://codepen.io/SitePoint/pen/bdLRbE)中找到

## 句法

现在是时候深入把手了。我们将讨论一些重要的术语和语法，它们构成了车把的核心。

### 公式

我们已经在上节看到了表达式。模板中使用的变量用双花括号`{{}}`括起来，称为表达式:

```
My name is {{name}}
```

### HTML 转义

车把可以对表达式返回的值进行转义。例如，字符`<`被转换成`&lt`。如果你不想让把手转义一个值，你必须用三重花括号`{{{variableName}}}`将变量括起来。例如，当下列模板:

```
I am learning {{language}}. It is {{{adjective}}}.
```

与下面报告的`context`变量一起使用:

```
var context = {
  "language" : "<h3>Handlebars</h3>",
  "adjective": "<h3>awesome</h3>"
}
```

生成的 HTML 将是:

```
I am learning <h3>Handlebars</h3>. It is <h3>awesome</h3>
```

在这个 [Codepen 演示](http://codepen.io/SitePoint/pen/xGYrKJ)中可以找到展示这个特性的现场演示

### 评论

我们也可以在把手模板里写评论。车把注释的语法是`{{!TypeYourCommentHere}}`。然而，每一个包含`}}`的注释或者任何其他在车把上有特殊含义的符号都应该以`{{!--TypeYourCommentHere--}}`的形式书写。把手注释在 HTML 中是不可见的，但是如果你想显示它们，你可以使用标准的 HTML 注释:`<!--comments-->`。

如果我们将所有这些概念应用到我们正在使用的模板中，我们可以得到如下所示的代码:

```
<!-- I am learning {{language}} -->
I am learning {{language}}. It is {{!--adjective--}}
```

如果我们使用前面的模板和下面报告的`context`变量:

```
var context = {
  "language" : "Handlebars",
  "adjective": "awesome"
}
```

HTML 输出将是:

```
<!-- I am learning Handlebars -->
I am learning Handlebars. It is
```

这将导致浏览器不显示标准 HTML 注释中的内容。在这里可以找到这个例子的演示。

### 阻碍

在车把中，挡块是具有挡块打开(`{{# }}`)和关闭(`{{/}}`)的表达式。我们将在稍后重点讨论助手时深入研究这个主题。现在，看看`if`块是如何编写的:

```
{{#if boolean}}
   Some Content here
{{/if}}
```

### 小路

Handlebars 支持普通路径和嵌套路径，使得查找嵌套在当前上下文下的属性成为可能。手柄也支持`../`路径段。该段引用父模板范围，而不是上下文中的上一级。

为了更好地理解这个主题，我们将使用下面的例子，在这个例子中我们使用了`each`助手(稍后将详细讨论)。如您所料，后者遍历数组的所有元素。

在本例中，我们将使用以下模板:

```
This article is available on {{website.name}}.<br/>

{{#each names}}
  I am a {{../occupation}}. My name is {{firstName}} {{lastName}}.<br/>
{{/each}}
```

通过提供这个`context`变量:

```
var context = {
  "occupation" : "developer",
  "website" : {
    "name" : "sitepoint"
  }
  "names" : [
    {"firstName" : "Ritesh", "lastName" : "Kumar"},
    {"firstName" : "John" , "lastName" : "Doe"}
  ]
}
```

我们将获得如下所示的输出:

```
This article is available on sitepoint.
I am a developer. My name is Ritesh Kumar.
I am a developer. My name is John Doe.
```

像我们到目前为止创建的其他小片段一样，[这个例子可以在 Codepen](http://codepen.io/SitePoint/pen/pJawJP) 上找到

## 助手

尽管 Handlebars 是一个无逻辑的模板引擎，但它可以使用助手执行简单的逻辑。Handlebars 辅助对象是一个简单的标识符，后面可以跟参数(用空格分隔)，如下所示:

```
{{#helperName parameter1 parameter2 ...}}
  Content here
{{/helperName}}
```

每个参数都是一个手柄表达式。可以从模板中的任何上下文访问这些助手。

术语“块”、“助手”和“块助手”有时可以互换使用，因为大多数内置助手都是块，尽管有些函数助手与块助手有些不同。我们将在讨论自定义助手时讨论它们。

一些内置的助手有`if`、`each`、`unless`和`with`。让我们了解更多。

### `each`助手

`each`助手用于迭代数组。帮助器的语法是`{{#each ArrayName}} YourContent {{/each}}`。我们可以通过在块中使用关键字`this`来引用单个数组项。使用`{{@index}}`可以呈现数组元素的索引。下面的例子说明了`each`助手的用法。

如果我们采用以下模板:

```
{{#each countries}}
  {{@index}} : {{this}}<br>
{{/each}}

{{#each names}}
  Name : {{firstName}} {{lastName}}<br>
{{/each}}
```

结合此`context`变量:

```
var context = {
  "countries":["Russia","India","USA"],
  "names" : [
    {"firstName":"Ritesh","lastName":"Kumar"},
    {"firstName":"John","lastName":"Doe"}
  ]
}
```

那么输出将是:

```
0 : Russia
1 : India
2 : USA
Name : Ritesh Kumar
Name : John Doe
```

这个例子的现场演示可以在 Codepen 上找到。

### `if`助手

`if`助手类似于`if`语句。如果条件评估为一个*真值*，手柄将渲染块。我们还可以通过使用`{{else}}`来指定一个模板部分，称为“else 部分”。`unless`助手与`if`助手相反。当条件评估为 *falsy* 值时，它呈现该块。

为了展示`if`助手是如何工作的，让我们考虑下面的模板:

```
{{#if countries}}
  The countries are present.
{{else}}
  The countries are not present.
{{/if}}
```

如果我们提供下面的`context`变量:

```
var context = {
  "countries": []
}
```

我们将获得下面报告的结果:

```
The countries are not present.
```

发生这种情况是因为空数组是一个 *falsy* 值。

如果你想玩帮手，可以看看我在 Codepen 上制作的[现场演示。](http://codepen.io/SitePoint/pen/eNVRJy)

## 自定义助手

您可以使用 Handlebars 提供的表达式系统创建自己的助手来执行复杂的逻辑。有两种助手:**函数助手**和**块助手**。第一个定义适用于单个表达式，而后者适用于块表达式。提供给回调函数的参数是写在助手名称后面的参数，用空格分隔。使用`Handlebars.registerHelper()`方法创建助手:

```
Handlebars.registerHelper("HelperName", function(arguments){
  // This function is executed whenever this helper is used
})
```

### 自定义函数助手

函数助手的语法是`{{helperName parameter1 parameter2 ...}}`。为了更好地理解如何继续实现，让我们创建一个名为`studyStatus`的函数助手，它返回一个字符串，如果是`passingYear < 2015`则为“通过”，如果是`passingYear >= 2015`则为“未通过”:

```
Handlebars.registerHelper("studyStatus", function(passingYear) {
   if(passingYear < 2015) {
      return "passed";
   } else {
      return "not passed";
   }
})
```

在我们的例子中，参数只有一个。然而，如果我们想传递更多的参数给助手的回调函数，我们可以把它们写在模板中第一个参数之后，用空格隔开。

让我们用这个模板开发一个例子:

```
{{#each students}}
  {{name}} has {{studyStatus passingYear}}.<br>
{{/each}}
```

并且用下面的`context`变量:

```
var context = {
  "students":[
    {"name" : "John", "passingYear" : 2013},
    {"name" : "Doe" , "passingYear" : 2016}
  ]
}
```

在这种情况下，输出将是:

```
John has passed.
Doe has not passed.
```

此处提供了此示例的[现场演示。](http://codepen.io/SitePoint/pen/rVJwed)

### 自定义块助手

自定义块助手的使用方式与函数助手相同，但语法略有不同。块助手的语法是:

```
{{#helperName parameter1 parameter2 ...}}
  Your content here
{{/helperName}}
```

当我们注册一个自定义块助手时，Handlebars 自动添加一个`options`对象作为回调函数的最后一个参数。这个`options`对象有一个`fn()`方法，允许我们临时改变对象的上下文来访问某个属性。让我们通过使用一个名为`studyStatus`但具有相同的`context`变量的块助手来改变上一节的例子:

```
Handlebars.registerHelper("studyStatus", function(data, options){
  var len = data.length;
  var returnData="";
  for(var i=0;i<len;i++){
    // change the value of the passingYear to
    // passed/not passed based on the conditions.
    data[i].passingYear=(data[i].passingYear < 2015) ? "passed" : "not passed";

    // here options.fn(data[i]) temporarily changes the
    // scope of the whole studyStatus helper
    // block to data[i]. So {{name}}=data[i].name
    // in the template.
    returnData = returnData + options.fn(data[i]);

  }

  return returnData;
});

var context = {
  "students":[
    {"name" : "John", "passingYear" : 2013},
    {"name" : "Doe" , "passingYear" : 2016}
  ]
}
```

如果此代码与下面定义的模板一起使用

```
{{#studyStatus students}}
  {{name}} has {{passingYear}}
{{/studyStatus}}
```

我们将获得以下结果:

```
John has passed.
Doe has not passed.
```

这里是 [Codepen 演示](http://codepen.io/SitePoint/pen/YXeQWo)。

## 部分模板

车把零件是可以在不同模板之间共享的模板。它们被写成`{{> partialName}}`。在 HTML 中使用它们之前，我们需要使用`Handlebars.registerPartial()`方法注册分部。下面的例子将帮助你理解如何注册一个名为`partialTemplate`的分部:

```
Handlebars.registerPartial(
  'partialTemplate',
  '{{language}} is {{adjective}}. You are reading this article on {{website}}.'
);

var context={
  "language" : "Handlebars",
  "adjective": "awesome"
}
```

当与下面定义的模板一起使用时

```
{{> partialTemplate website="sitepoint"}} <br>
{{> partialTemplate website="www.sitepoint.com"}}
```

它将给出以下结果:

```
Handlebars is awesome. You are reading this article on sitepoint
Handlebars is awesome. You are reading this article on www.sitepoint.com
```

这段代码的现场演示可以在这个 [Codepen 演示](http://codepen.io/SitePoint/pen/BNYZLK)中找到。

## 预编译

正如我们所看到的，Handlebars 做的第一件事是将模板编译成一个函数。这是在客户机上执行的最昂贵的操作之一。如果我们预编译`templateScript`，然后将编译后的版本发送给客户端，我们可以提高应用程序的性能。在这种情况下，需要在客户机上执行的唯一任务就是执行该函数。由于预编译文件是一个脚本，我们可以像普通文件一样加载 HTML 中的脚本。让我们看看这些东西是如何完成的。

首先，你必须使用`npm install handlebars -g`全局安装车把。请确保不同的模板用不同的文件名和扩展名`.handlebars`写在不同的文件中(例如`demo.handlebars`)。没有必要在这些文件中使用`<script>`标签。

```
<div>
  My name is {{name}}. I am a {{occupation}}.
</div>
```

现在将所有模板文件存储在一个名为`templates`的文件夹中。您可以使用任何您希望的文件夹名称，但是如果您这样做，请不要忘记相应地更改下面的命令。因此，打开终端并执行命令:

```
handlebars path/to/templates -f templatesCompiled.js
```

该命令将生成一个名为`templatesCompiled.js`的文件，其中包含所有编译过的模板。编译器将在`Handlebars.templates`中插入模板。如果输入文件是`demo.handlebars`，那么它将在`Handlebars.templates.demo`被插入

现在我们必须将`templatesCompiled.js`作为普通的`script`包含在 HTML 文件中。值得注意的是，我们不需要加载整个 Handlebars 库，因为不再需要编译器本身。我们可以使用较小的“运行时”版本来代替:

```
<script src="handlebars.runtime.js"></script>
<script src="path/to/templatesCompiled.js"></script>
```

现在，我们可以通过使用以下代码来使用最初出现在`demo.handlebars`中的模板:

```
var context = {
  "name" : "Ritesh Kumar",
  "occupation" : "developer"
}

var templateScript = Handlebars.templates.demo(context);

$(document.body).append(templateScript);
```

最终输出将如下所示:

```
My name is Ritesh Kumar. I am a developer.
```

这种方法将显著提高应用程序的性能，并且页面加载时间也减少了，因为我们使用了比整个库更轻的 Handlebars 运行时构建。

这整个预编译演示的代码可以在 GitHub 上找到。

## 结论

在这篇文章中，我们讨论了车把的基本概念。我们还研究了它常用的函数和语法。我希望你喜欢这个教程，并且你会使用其中的演示来很好地理解这个主题。我期待着阅读您的评论。

如果你不想下载这个库，但你仍然想尝试一下，你可以在[http://tryhandlebarsjs.com/](http://tryhandlebarsjs.com/)在线玩手柄。

## 分享这篇文章