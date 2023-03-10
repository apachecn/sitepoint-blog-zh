# 了解 ECMAScript 6:模板字符串

> 原文：<https://www.sitepoint.com/understanding-ecmascript-6-template-strings/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

欢迎阅读我的 ECMAScript 6 系列文章的第二部分！我最喜欢微软新开发的浏览器渲染引擎 Project Spartan T1 的一点是它对 ECMAScript 6 的广泛支持。因此，我写了这个系列文章，向您介绍在编写大型 web 应用程序时，使用 ECMAScript 6 可以做的一些很酷的事情。

在第一部分中，我介绍了[类和继承](https://www.sitepoint.com/understanding-ecmascript-6-class-inheritance/?WT.mc_id=13390-DEV-sitepoint-article14)。在本文中，我将根据我创建嵌入式表达式的个人经验，重点讨论模板字符串。

## 解决线路返回问题

在处理 [Babylon.js](http://www.babylonjs.com/?WT.mc_id=13390-DEV-sitepoint-article14) 时，为了便于理解，我不得不处理着色器的代码，就像一大串文本(看起来像 C)。

你可以在这里找到一个例子[。](https://github.com/BabylonJS/Babylon.js/blob/master/Babylon/Shaders/default.vertex.fx?WT.mc_id=13390-DEV-sitepoint-article14)

在 JavaScript 中处理冗长文本时的问题是换行。你写了多少次这种东西？

```
var myTooLongString = "A long time ago, in a galaxy far," +
                      "far away...." +
                      "It is a period of civil war." +
                      "Rebel spaceships, striking" +
                      "from a hidden base, have won" +
                      "their first victory against" +
                      "the evil Galactic Empire.";
```

当你不得不使用 200 多行的着色器时，这很快就变成了一个真正的痛苦。

幸运的是，ECMAScript 6 提供了新的模板字符串特性。在其他奇迹中，模板字符串直接支持多行字符串:

```
var myTooLongString = `A long time ago, in a galaxy far
 far away....
 It is a period of civil war.
 Rebel spaceships, striking
 from a hidden base, have won
 their first victory against
 the evil Galactic Empire`;
```

因为所有的字符在模板字符串中都是有意义的，所以我不能添加前导空格

现在，作为 JavaScript 开发人员，我们有 3 种方法来定义字符串:

*   用`""`
*   用`''`
*   带``` `` ```(也称反勾或[重音符](http://en.wikipedia.org/wiki/Grave_accent?WT.mc_id=13390-DEV-sitepoint-article14))

那么模板部分呢？

多行支持并不是模板字符串的唯一特性。事实上，您也可以使用模板字符串用变量值替换占位符，就像您在 C/C++中使用`printf`或 C#中使用`string.Format`一样:

```
var items = [];
items.push("banana");
items.push("tomato");
items.push("light saber");
var total = 100.5;
result.innerHTML = `You have ${items.length} item(s) in your basket for a total of $${total}`;
```

该代码产生以下输出:

```
You have 3 item(s) in your basket for a total of $100.5
```

很方便，对吧？

记住 ECMAScript 5 的方式:

```
result.innerHTML = "You have " + items.length + " item(s) in your basket for a total of $" + total;
```

## 使用标签走得更远

模板字符串规范的最后阶段是在字符串本身之前添加一个自定义函数，以创建一个带标签的模板字符串:

```
var myUselessFunction = function (strings,...values) {
  var output = "";
  for (var index = 0; index < values.length; index++) {
    output += strings[index] + values[index];
  }
  output += strings[index]
  return output;
}

result.innerHTML = myUselessFunction `You have ${items.length} item(s) in your basket for a total of $${total}`;
```

此处的函数用于访问常量字符串部分以及评估的变量值。

在前面的示例中，字符串和值如下:

*   `strings[0] = "You have "`
*   `values[0] = 3`
*   `strings[1] = "items in your basket for a total of $"`
*   `values[1] = 100.5`
*   `strings[2] = ""`

如你所见，每个`values[n]`都被常量字符串(`strings[n]`和`strings[n + 1]`)包围着。

这允许您控制最终输出字符串的构建方式。在我之前的例子中，我只复制了模板字符串的基本行为，但是我们可以更进一步，给你的字符串添加酷的处理。

例如，下面是一段代码，用于阻止试图插入自定义 DOM 元素的字符串:

```
var items = [];
items.push("banana");
items.push("tomato");
items.push("light saber");
var total = "Trying to hijack your site <BR>";
var myTagFunction = function (strings,...values) {
  var output = "";
  for (var index = 0; index < values.length; index++) {
    var valueString = values[index].toString();

    if (valueString.indexOf(">") !== -1) {
      // Far more complex tests can be implemented here :)
      return "String analyzed and refused!";
    }

    output += strings[index] + values[index];
  }

  output += strings[index]
  return output;
}

result.innerHTML = myTagFunction `You have ${items.length} item(s) in your basket for a total of $${total}`;
```

带标签的模板字符串可以用于很多事情，比如安全、本地化、创建你自己的领域特定语言等等。

## Raw Strings

当访问字符串常量时，标记函数有一个特殊的选项:它们可以使用`strings.raw`来获取未转义的字符串值。例如，在这种情况下`\n`将不会被视为只有一个角色，而是实际上是两个`\`和`n`。

主要目的是允许您在输入字符串时访问它:

```
var myRawFunction = function (strings,...values) {
  var output = "";
  for (var index = 0; index < values.length; index++) {
    output += strings.raw[index] + values[index];
  }
  output += strings.raw[index]
  console.log(strings.length, values.length);
  return output;
}

result.innerHTML = myRawFunction `You have ${items.length} item(s) in your basket\n.For a total of $${total}`;
```

此代码生成以下输出:

```
You have 3 item(s) in your basket\n.For a total of $100.5
```

您还可以使用 String 的一个新函数:`String.raw().`这个函数是一个内置函数，它的功能与我之前的示例完全相同:

```
result.innerHTML = String.raw `You have ${items.length} item(s) in your basket\n.For a total of $${total}`;
```

## 结论

Project Spartan 和最新版本的 Chrome (41+)、Opera(28+)和 Firefox (35+)支持模板字符串，你可以在这里跟踪 ECMAScript 6 的整体支持水平[。所以如果你的目标是现代网络，没有理由不接受模板字符串。](http://kangax.github.io/compat-table/es6/?WT.mc_id=13390-DEV-sitepoint-article14)

要全面了解 Project Spartan 中即将推出的新网络标准和功能，如 WebAudio，您可以在 [status.modern.IE](http://status.modern.ie/?utm_source=SitePoint&utm_medium=content&utm_campaign=article14) 查看完整列表。

## JavaScript 的更多实践

这可能会让你有点惊讶，但微软有许多关于开源 JavaScript 主题的免费学习，我们的任务是随着 [Project Spartan 的到来](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=13390-DEV-sitepoint-article14)创造更多。查看我自己的:

*   [web GL 3D 和 HTML5 以及巴比伦介绍。JS](http://www.microsoftvirtualacademy.com/training-courses/introduction-to-webgl-3d-with-html5-and-babylon-js?WT.mc_id=13390-DEV-sitepoint-article14)
*   [用 ASP.NET 和安古拉杰构建单页应用](http://channel9.msdn.com/Events/Build/2014/3-644?WT.mc_id=13390-DEV-sitepoint-article14)
*   [HTML 中的前沿图形](http://channel9.msdn.com/events/Build/2014/3-558?WT.mc_id=13390-DEV-sitepoint-article14)

或者我们团队的学习系列:

*   [让你的 HTML/JavaScript 更快的实用性能技巧](http://channel9.msdn.com/Series/Practical-Performance-Tips-to-Make-Your-HTMLJavaScript-Faster/06?WT.mc_id=13390-DEV-sitepoint-article14)(从响应式设计、休闲游戏到性能优化的七部分系列)
*   现代网络平台快速启动(HTML、CSS 和 JS 的基础)
*   [使用 HTML 和 JavaScript JumpStart 开发通用 Windows 应用](http://www.microsoftvirtualacademy.com/training-courses/developing-universal-windows-apps-with-html-and-javascript-jump-start?WT.mc_id=13390-DEV-sitepoint-article14)(使用您已经创建的 JS 来构建应用)

以及一些免费工具: [Visual Studio 社区](http://www.visualstudio.com/en-us/products/free-developer-offers-vs.aspx?WT.mc_id=13390-DEV-sitepoint-article14)、 [Azure 试用版](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13390-DEV-sitepoint-article14)，以及[针对 Mac、Linux 或 Windows 的跨浏览器测试工具](http://modern.ie/?utm_source=SitePoint&utm_medium=content&utm_campaign=article14)。

本文是微软网站开发技术系列的一部分。我们很高兴与你分享斯巴达项目的[和它的](http://blogs.msdn.com/b/ie/archive/2015/01/22/project-spartan-and-the-windows-10-january-preview-build.aspx?WT.mc_id=13390-DEV-sitepoint-article14)[新渲染引擎](http://blogs.msdn.com/b/ie/archive/2015/02/26/a-break-from-the-past-the-birth-of-microsoft-s-new-web-rendering-engine.aspx?WT.mc_id=13390-DEV-sitepoint-article14)。在 [modern 获得免费虚拟机或在您的 Mac、iOS、Android 或 Windows 设备上进行远程测试。即](http://modern.ie/?utm_source=SitePoint&utm_medium=content&utm_campaign=article14)。

## 分享这篇文章