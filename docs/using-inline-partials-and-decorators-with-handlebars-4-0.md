# 在把手 4.0 中使用内嵌 Partials 和 Decorators

> 原文：<https://www.sitepoint.com/using-inline-partials-and-decorators-with-handlebars-4-0/>

Handlebars 是客户端和服务器端渲染中使用最广泛的 JavaScript 模板库之一。它实现了 [mustache 规范](http://mustache.github.io/)，但是添加了一些额外的调料，使得使用模板更加容易。如果你是手柄新手，想了解更多，我建议你去看看我的 [Pluralsight 课程，学习如何用手柄](https://www.pluralsight.com/courses/handlebars-javascript-templating)制作 JavaScript 模板。

手柄 4.0 版本于 2015 年 9 月登陆，并带来了两大新功能:*内嵌 Partials* 和 *Decorators* 。在本文中，我们将看看这两个特性，解释它们的语法以及何时应该使用它们。到最后，你应该可以轻松地使用这两个特性，将你的模板游戏提升到一个新的水平！

## 内嵌部分

Partials 是一个常见的模板概念，并不是手柄独有的。其背后的想法是创建可能被重用的模板，将它们分离到自己的文件中(一个*部分*)，然后在不同的模板中使用它们。你可能认为在 *Partials* 是模块化你的模板的简单工具。

在手把中，零件可能不是最方便的结构。首先，所有的部分都是全局的。这意味着这个工具可能适用于您的应用程序，但是在大型应用程序中很难控制它可能会成为一个问题。其次，需要使用 JavaScript 注册分部。许多模板预编译器或加载器会为您处理这些，调用`Handlebars.registerPartial()`。最后，偏音必须从模板中分离出来。如果您的模板很大，这可能是一个好处，但也会使开发人员难以完全理解模板的输出。在理解完整的输出之前，他们需要在许多不同的文件之间切换。

所有这些问题塑造了开发者使用 *partials* 的方式。它们最终只被保留给最大的可重用代码块。

有了*内联分音*，Handlebars 终于释放了*分音*的真正潜力，让你能够放下 JavaScript，不再需要将分音分割成单独的文件。内联部分是在你的模板中定义的，在 Handlebars 语法中。注册它们不需要 JavaScript。你只需要声明一个分部并使用它。此外，它们不是全局的，而是块范围的。这意味着一旦你在你的模板中声明了一个内联分部，它只能在当前作用域和它下面的作用域中使用。

当决定使用内联部分还是普通部分时，请寻找具有以下任一属性的小型可重用 HTML 代码块:

*   它们太小了，不值得放在自己的局部文件中。
*   它们只在单个模板的上下文中使用。

### 使用内嵌部分

现在让我们看看内联部分语法和用法。

下面是如何声明内联分部的方法。首先，取你想成为你的部分的代码。

```
<li>I'm iteration #{{number}}</li>
```

然后用新的内联语法包装它，传递一个参数，即分部的名称。

```
{{#* inline "iterationCount"}}
    <li>I'm iteration #{{number}}</li>
{{/inline}}
```

现在，您可以在声明它的 Handlebars 模板中使用这个分部。这里有一个完整的例子。

```
{{#* inline "iterationCount"}}
    <li>I'm iteration #{{number}}</li>
{{/inline}}

{{#each someArray}}
    {{> iterationCount}}
{{/each}}
```

### 简单的部分例子

记住前面的解释，下一步是理解在我们有内联分部之前，我们如何使用分部。假设我们从这个模板开始:

```
// template.hbs
<h1>Hello {{firstName}} {{lastName}}</h1>
<ul>
    {{#each clients}}
        <li>{{firstName}} {{lastName}}</li>
    {{/each}}
</ul>
```

`{{firstName}} {{lastName}}`的重复为错别字和错误提供了机会。要完成的任务是将该模式提取为一个部分，所以让我们看看为了实现它我们必须做些什么。

首先，用下面的代码创建一个 JavaScript 文件，例如`someFile.js`:

```
Handlebars.registerPartial('fullName', '{{firstName}} {{lastName}}');
```

然后，在车把模板中，您可以有:

```
<h1>Hello {{> fullName}}</h1>
<ul>
    {{#each clients}}
        <li>{{> fullName}}</li>
    {{/each}}
</ul>
```

虽然这确实清理了我们的模板，使它更符合习惯，但它将`fullName`部分的实现混淆到一个单独的文件中(使用不同的语言和语法)。如果这些小块模板中的许多被重构为片段，第一次接触这些代码的开发人员可能会在试图理解整个模板时遇到一些麻烦。

### 内嵌部分示例

现在让我们以前面的例子为例，用内联偏导数来求解。在下一个示例中，您会注意到一些事情:

*   所有的东西都在同一个文件里，用同一种语言。
*   模板作用域部分意味着在另一个文件中可以有不同的“全名”格式。
*   保持正常的部分相同的好处，如消除冗余。

以下是使用内嵌部分的完整名称解决方案:

```
// template.hbs
{{#* inline "fullName"}}{{firstName}} {{lastName}}{{/inline}}
<h1>Hello {{> fullName}}</h1>
<ul>
    {{#each clients}}
        <li>{{> fullName}}</li>
    {{/each}}
</ul>
```

## 装修工

在这篇文章的介绍中，我提到了手柄版本 4.0 的另一个大特性，*装饰者*。

Decorators 让你“装饰”手柄程序函数，并在呈现模板之前修改状态。主要目标是允许您使用非输出“元数据”向模板添加功能。这个实现是基于耶胡达·卡茨为 [ES6](https://www.sitepoint.com/javascript/es6/) 提出的 JavaScript 装饰提议。在许多方面，手柄中的装饰器在更基础的层面上为您提供了助手功能的伴侣。事实上，在引入它们之前，你可能已经在使用助手来完成现在由装饰者优雅地完成的工作。

为了理解 Decorators 适合 Handlebars 模板渲染，让我们看看 Handlebars 是如何编译模板的。我喜欢称之为“车把舞”,它的作用类似于:

1.  获取模板
2.  编译模板
3.  呈现输出

在这三个步骤中，第二个是通过调用`Handlebars.compile`函数来执行的。它将一个模板作为字符串并编译它，返回一个函数，然后您可以用一些上下文数据调用它(上面的第三步)。Handlebars 模板中的每个块都创建这些编译函数中的一个，返回的主函数根据需要调用它们来呈现输出。

Decorators 将自己插入到这些块范围的编译函数中，让您在呈现块之前控制执行某些功能。你用它做什么取决于你自己，但是 Decorator 期望的返回值是一个呈现模板输出的函数。

在查看装饰函数参数之前，我们先来看一个简单的实例。

### 使用装饰者

装饰者在 JavaScript 中注册，像*助手*和*部分*(虽然不是内联的！).这里有一个例子:

```
Handlebars.registerDecorator('shhh', function(program, props, container, context) {
    var isLoud = program().trim() === 'loud';
    if (isLoud) {
        return function() { return ''; };
    } else {
        return program;
    }
});
```

在上面的例子中，我们看手柄程序函数(我通常称这个*“编译后的函数”*)。如果程序返回“loud”，那么我们将用一个返回空字符串的函数覆盖它。否则，我们将返回正常的程序功能。

让我们看看如何使用这个装饰器:

```
loud
{{*shhh}}
```

有了这个模板示例，原来的程序函数将返回“loud”(decorator 没有输出)。该模板呈现时的输出将是:

 `没错，只是一个空字符串。

其作用域是呈现已经被“修饰”的模板的函数(从“shhh”Decorator 返回)返回一个空字符串。该函数基于“loud”的真实性返回。

现在让我们来看一个不同的模板:

```
quiet
{{*shhh}}
```

呈现该模板时的输出将是:

```
quiet
```

由于程序与“大声”不匹配，所以它被通过而不是被覆盖。

这是一个非常随意的例子，但是希望你能看到装饰者是如何影响程序功能的，以及对它的控制是多么的强大。现在是时候看看装饰函数的参数了。

### 装饰函数参数

当 Handlebars 调用注册为装饰器的函数时，会向它传递一组参数。我们将在接下来的小节中对它们逐一进行分析，这样您就可以理解您可以用 Decorators 来装饰什么了。

以下是装饰函数的完整函数签名:

```
function(program, props, container, context)
```

#### 装饰函数返回值

装饰者必须返回一个函数或 falsy 值(`undefined`、`null`、`false`等等)。返回的任何字符串或对象都将引发异常。返回的函数将用于呈现完成的车把字符串。如果返回`undefined`，将隐式使用原程序参数。

#### `program`

这是经过编译的 Handlebars 函数，它被传递数据，并返回一个呈现的字符串。调用函数时，可以修改参数、返回值或调整上下文。返回这个程序参数，让渲染通过装饰器。您也可以通过返回不同的函数来“覆盖”程序参数。

#### `props`

即使程序函数被替换，在该对象上设置的任何属性也将在程序函数上设置。这是一个安全的地方来设置您想要在其他 Decorators 或 helpers 中访问的元数据。

#### `container`

这是当前的手柄运行时容器。这包含了所有的片段、助手和上下文数据，并且可以被修改(如下例所示)。

#### `context`

这是你的模板的父上下文，包括修饰器的所有参数以及传递给程序函数的数据。

### 在 4.0 之前的手柄中格式化货币

为了演示现实世界中的装饰者，让我们来看一个您可能熟悉的模板用例:格式化货币。我们希望找到一种简单的方法来动态格式化给定货币的给定值。车把确实提供了一些现有的机制来解决这个问题。让我们来看一个用 4.0 之前的车把功能解决这个问题的方法。

首先，我们创建助手来格式化货币。助手将接受要格式化的值和货币作为参数:

```
//someFile.js
Handlebars.registerHelper('formatMoneyHelper', function(value, currency) {
    switch(currency) {
        case 'USD':
            return new Handlebars.safeString('$' + value + 'USD');
        case 'EUR':
            return new Handlebars.safeString('€' + value + 'EUR');
    }
});
```

现在我们可以在模板中使用这个助手了。

```
//template.hbs

Starting amount: {{formatMoneyHelper this.start this.format}}
Ending amount: {{formatMoneyHelper this.end this.format}}
Profit/Loss: {{formatMoneyHelper this.net this.format}}
```

我们希望我们的数据采用以下格式:

```
{
    start: 12.30,
    end: 15.30,
    net: 3.00,
    format: 'USD'
}
```

这是个不错的解决方法。帮助器是为这类问题设计的，但是在模板和帮助器中都编写了大量冗余代码。我们可能会对此进行更多的优化，但是让我们研究一下在 Handlebars 4.0 中使用 Decorators 来完成这项任务的方法。

### 在把手 4.0 中用装饰者格式化钱

格式化货币的一个更好的方法是有一个更简单的助手，它只接受值。它应该已经知道货币应该被格式化成哪种货币。用动态方式实现这一点对帮助者来说很棘手，所以让我们利用 Decorators 找到一个更简单的解决方案。

由于 Decorator 能够修改主程序函数，所以让我们创建一个 Decorator 来设置一个已经加载了货币的 format helper 函数。我们将从 JavaScript 和装饰注册开始。

```
function formatUSD(value) {
    return new Handlebars.safeString('$' + value + 'USD');
}

function formatEUR(value) {
    return new Handlebars.safeString('€' + value + 'EUR');
}

Handlebars.registerDecorator('activateFormatter', function(program, props, container, context) {
    var moneyHelper,
        format = context.args[0] || context.data.root.format;

    switch(format) {
        case "USD":
            moneyHelper = formatUSD;
            break;
        case "EUR":
            moneyHelper = formatEUR;
            break;
        default:
            console.log('Money format not set. Please set before rendering template.');
            moneyHelper = function() {};
    }

    container.helpers = {
        formatMoneyHelper: moneyHelper
    };
});
```

装饰器负责根据我们的上下文对象中的静态值或格式属性注册正确的格式化帮助器，允许它在循环中也是动态的。这使得我们的助手功能更加模块化和可扩展。这种方法的另一个好处是格式化函数的可测试性，因为它们是普通的 JavaScript。

接下来让我们看看如何在模板中使用这个装饰器:

```
//template.hbs

{{* activateFormatter}}

Starting amount: {{formatMoneyHelper this.start}}
Ending amount: {{formatMoneyHelper this.end}}
Profit/Loss: {{formatMoneyHelper this.net}}
```

这将使用我们的上下文对象中的 format 属性将 formatUSD 函数设置为我们的 formatMoneyHelper 辅助函数。我们也可以使用以下语法覆盖它:

```
{{* activateFormatter "EUR"}}
```

使用 Decorators 的实现更加优雅、可测试，并且允许您控制模板中当前块的格式。

装饰者是非常强大的，上面的例子仅仅是可以实现的一点点。

## 结论

希望这篇文章已经启发了你在你自己的项目中使用内嵌 Partials 和 Decorators。您已经看到了内联片段对于在模板中定义片段和减少注册片段的 JavaScript 开销是多么有用。此外，您已经看到它们非常适合小的、重复的标记片段。另一方面，Decorators 允许您修改现有的 Handlebars 块程序函数，并在执行之前让您控制 Handlebars 运行时。它们非常适合处理上下文数据或助手。

因此，内联 Partials 和 Decorators 都是对前端开发的基本工具的强大补充。

现在开始用内嵌零件装饰你的把手模板吧！` 

## `分享这篇文章`