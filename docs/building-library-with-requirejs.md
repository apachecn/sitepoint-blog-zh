# 使用 RequireJS 构建库

> 原文：<https://www.sitepoint.com/building-library-with-requirejs/>

[RequireJS](http://requirejs.org/) 是一个用于浏览器的 AMD 模块加载器，可以异步加载你的脚本和 CSS 文件。您不再需要处理单个文件(例如 index.html)中脚本文件的顺序。相反，您只需将代码包装在模块定义中，RequireJS 会处理依赖关系，使您的代码更加结构化和组织良好。它还有一个优化工具，可以将文件进行变形和连接，以供生产使用。

官方网站提供了关于其 API 的大量文档，并且有许多范例库可以帮助你。但是它有很多配置，而且一开始使用 RequireJS 很棘手。

在本文中，我们将学习如何使用 RequireJS，方法是使用 AMD 模块构建一个库，使用 RequireJS 优化器优化它并将其导出为一个独立的模块。稍后我们将使用 RequireJS 构建一个应用程序并使用我们的库。

本教程假设您对 RequireJS 有所了解。如果你正在寻找一本入门书，请查阅:[理解有效加载 JavaScript 模块的要求](https://www.sitepoint.com/understanding-requirejs-for-effective-javascript-module-loading/)。

## 安装要求

可通过 bower 获得 RequireJS:

```
bower install requirejs --save
```

或者你可以在 [github](https://github.com/jrburke/requirejs) 上抓取文件。

对于 RequireJS 项目，还有一个基于 Grunt 的 Yeoman 生成器。

## 定义 AMD 模块

我们将把我们的代码包装在`define()`中，这将使它成为一个 AMD 模块。

文件:`mylib.js`

```
define(['jquery'], function($) {
    // $ is jquery now.

    return 'mylib';
});
```

就是这样。注意，`define()`接受一个依赖数组的可选第一个参数，在本例中是`['jquery']`。这是这个模块的依赖列表。数组中的所有模块都将在此模块之前加载。当这个模块被执行时，参数是依赖数组中相应的模块。

所以在这种情况下，jQuery 将首先被加载，然后作为参数`$`传递到函数中，然后我们可以在我们的模块中安全地使用它。最后，我们的模块返回一个字符串。当需要这个模块时，返回值是传递给函数参数的值。

### 需要其他模块

让我们通过定义第二个模块并要求我们的第一个模块`mylib.js`来看看这是如何工作的。

文件:`main.js`

```
define(['jquery', 'mylib'], function($, mylib) {
    // $ is jquery as usual
    // mylib is the string `mylib` because that's the return value
    // from the first module

    return {
        version: '0.0.1, jQuery version: ' + $.fn.jquery,
        mylibString: mylib
    }
});
```

您可以在依赖关系数组中要求任意多的依赖关系，所有模块都可以通过函数参数以相同的顺序使用。在第二个模块中，我们需要`jquery`和`mylib`模块，并简单地返回一个对象，暴露一些变量。此库的用户将使用此对象作为您的库。

## 配置 RequireJS 优化器

您可能想知道，RequireJS 如何仅通过查看依赖数组中的字符串来知道要加载什么文件？在我们的例子中，我们提供了字符串形式的`jquery`和`mylib`，并且 RequireJS 知道这些模块在哪里。`mylib`很简单，就是省略了`.js`的`mylib.js`。

`jquery`怎么样？这就是使用 RequireJS 配置的地方。您可以通过 RequireJS 配置提供广泛的配置。有两种方法可以提供这个配置，因为我们使用的是 RequireJS 优化器，我将向您展示 r.js 方法。r.js 是 RequireJS 优化器。

我们将为 r.js 提供一个配置，它将所有模块优化到一个文件中。我们提供的配置将使 r.js 将模块构建为独立的全局库，既可以用作 AMD 模块，也可以用作浏览器中的全局导出。

r.js 可以通过命令行运行，也可以作为节点模块运行。还有一个运行优化器的繁重任务`grunt-requirejs`。

也就是说，让我们看看我们的配置是什么样的:

文件:`tools/build.js`

```
{
  "baseUrl": "../lib",
  "paths": {
    "mylib": "../main"
  },
  "include": ["../tools/almond", "main"],
  "exclude": ["jquery"],
  "out": "../dist/mylib.js"
  "wrap": {
    "startFile": "wrap.start",
    "endFile": "wrap.end"
  }
}
```

配置文件实际上是 RequireJS 的核心。一旦你理解了这些参数的工作原理，你就可以像专业人士一样使用 RequireJS 了。

您可以做不同的事情，用配置文件调整您的项目构建。要了解更多关于配置和需求的信息，我建议参考[文档](http://requirejs.org/docs/optimization.html)和[维基](https://github.com/jrburke/requirejs/wiki)。还有一个[示例配置文件](https://github.com/jrburke/r.js/blob/master/build/example.build.js)，演示了如何使用构建系统，所以请务必也参考一下。

最后，我们实际运行优化器。正如我之前所说的，您可以通过命令行或节点运行它，也可以通过简单的任务运行它。参见 [r.js 自述文件](https://github.com/jrburke/r.js)了解如何在不同环境下运行优化器。

```
node tools/r.js -o tools/build.js
```

这将在`dist/mylib.js`中生成构建文件

### build.js

接下来，让我们看看参数的实际含义。

**base URL**–所有模块查找的根路径。

**路径**–相对于 baseUrl 的模块名称的路径映射。

在我们的示例中，“mylib”映射到“../main”，这是相对于`baseUrl`的，所以当我们需要“mylib”时，它会加载文件”../lib/../mylib/main.js "。
注意我们追加了`baseUrl`，然后是`paths`设置，比模块名后面跟着一个`.js`后缀。您可以在这里指定模块如何映射到诸如`jquery`和`mylib`这样的文件。

**包含**–我们希望在优化过程中包含的模块。包含的模块所需的依赖项被隐式包含。在我们的例子中，`main`模块依赖于也将被包含的`mylib`和`jquery`，所以不需要显式地包含它。我们还包括我稍后将提到的`almond`。

**排除**–我们希望从优化过程中排除的模块。在我们的例子中，我们排除了`jquery`。构建的库的消费者将提供一个 jQuery 库。我们将在以后使用我们的库时看到这一点。

**out**–优化输出文件的名称。

**wrap**–将构建包包装在由`wrap`指定的开始和结束文本中。优化后的输出文件类似于:`wrap.start` +包含的模块+ `wrap.end`。`wrap.start`和`wrap.end`是输出中包含其内容的文件的名称。

### 杏仁

构建的库在文件中没有包含 require.js，而是使用了 almond。almond 是一个小的 AMD API 实现，它将取代 require.js。

## 包装我们的图书馆

在 r.js 配置中，我们用`wrap.start`和`wrap.end`文件包装了我们的库。我们还在我们的库中包含了 almond，这将使我们的库成为独立的，因此它们可以通过 browser globals 使用而无需 RequireJS，或者通过 requirejs 作为 AMD 模块使用。

`File: wrap.start`

```
(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
    // AMD.
    define(['jquery'], factory);
  } else {
    // Browser globals.
    root.mylib = factory(root.$);
  }
}(this, function($) {
```

我们包含的模块`main`、`mylib`、`almond`就在这里`wrap.start`和`wrap.end`的中间。

`File: wrap.end`

```
// Register in the values from the outer closure for common dependencies
  // as local almond modules
  define('jquery', function() {
    return $;
  });

  // Use almond's special top level synchronous require to trigger factory
  // functions, get the final module, and export it as the public api.
  return require('mylib');
}));
```

如果消费者使用 AMD 加载程序，那么构建的文件将要求“jquery”作为 AMD 依赖项。如果消费者只是使用浏览器全局变量，库将获取`$`全局变量，并将其用于 jQuery 依赖项。

## 使用带 RequireJS 的库

我们完成了我们的库，现在让我们通过构建一个 requirejs 应用程序来实际使用它。

文件:`app.js`

```
define(['jquery', 'mylib'], function($, mylib) {
  // $ is jquery
  // mylib is mylib that is:
  // {
  //   version: 'version 0.0.1 jQuery version: xxx',
  //   mylib: 'mylib'
  // }
});
```

这里没有什么特别的，这是另一个需要 jQuery 和 mylib 的模块。当用`define`定义一个模块时，它不会被立即执行，也就是说它的回调函数(在依赖数组之后传递)不会被立即执行。这意味着我们的应用程序不仅仅是通过定义这个模块开始的。现在让我们看看如何配置 RequireJS 并实际执行这个模块，这是我们的应用程序。

### 为浏览器配置要求

我们将在一个文件中配置 RequireJS 并执行我们的应用程序模块。虽然有不同的方法可以做到这一点。

文件:`common.js`

```
requirejs.config({
  baseUrl: '../lib',
  paths: {
    'jquery': 'jquery/dist/jquery.min',
    'underscore': 'underscore/dist/underscore',
    'backbone': 'backbone/backbone',
    'mylib': 'mylib/dist/mylib',
    'app': '../app'
  },
  shim: {
    'jquery': {
      exports: '$'
    },
    'backbone': {
      deps: ['jquery', 'underscore'],
      exports: 'Backbone',
    },
    'underscore': {
      exports: '_'
    }
  }
});

require(['app/app'], function(App) {
  // app module is available here
  // you can start your application now
  // this is immediately called because
  // we used `require` instead of `define`
  // to define this module.
});
```

`baseUrl`和`paths`配置与之前相同。这里的附加配置值是:

**shim** :为传统的“浏览器全局”脚本配置依赖关系和导出，这些脚本不使用`define()`来声明依赖关系和设置模块值。例如， [Backbone](http://backbonejs.org/) 不是一个 AMD 模块，但是它是一个浏览器全局，将`Backbone`导出到我们在`exports`中指定的全局名称空间中。在我们的例子中，模块也依赖于 jQuery 和[下划线](http://underscorejs.org/)，所以我们指定使用`deps`。在加载 Backbone 之前，加载`deps`数组中的脚本，一旦加载完毕，`exports`值就被用作模块值。

注意，您也可以在这个应用程序项目中使用 r.js，这将需要一个单独的配置。但不要因此而困惑。我不会详细说明如何做到这一点，但这与我们为图书馆所做的类似。更多参考见[示例构建配置](https://github.com/jrburke/r.js/blob/master/build/example.build.js)。

### 要求与定义

稍后我们使用`require`来加载一个模块并立即执行它。有时`define`和`require`会被混淆，弄不清哪一个在什么时候使用。`define`定义一个模块，但不执行它，`require`定义一个模块并执行它——也就是说，它在执行自己之前，先加载并执行依赖的模块。通常你会有一个`require`作为主入口模块，它依赖于通过`define`定义的附加模块。

### 加载脚本

通常，您会将所有脚本文件包含在您的`index.html`中。现在我们正在使用 RequireJS，我们只需要包含 RequireJS 并指定我们的`data-main`，这是我们应用程序的入口点。有不同的方法来设置配置选项，或者分离`index.html`中使用的主模块。你可以在这里找到更多关于那个[的信息。](https://github.com/jrburke/requirejs/wiki/Patterns-for-separating-config-from-the-main-module)

```
<script data-main="scripts/common" src="scripts/lib/require/require.js"></script>
```

## 结论

在本文中，我们构建了一个库，以及一个使用该库的应用程序。我们学习了如何配置 r.js 优化器，以及如何在浏览器中配置 RequireJS。最后，我们学习了如何使用 RequireJS 来定义和使用 AMD 模块。这使得我们的代码结构良好，组织有序。

我已经在本教程的前半部分(配置优化器)使用了这个[示例——lib global](https://github.com/requirejs/example-libglobal)repo，后半部分没有那么复杂，所以现在您应该可以使用自己的示例了。

官方的 RequireJS 网站是最终的文档，但是请确保查看 github 上的[示例回购以及该回购中的示例项目，它们演示了 RequireJS 应用程序的使用。](https://github.com/requirejs)

## 分享这篇文章