# 如何访问聚合元素中的成员函数

> 原文：<https://www.sitepoint.com/access-member-functions-polymer-elements/>

本文是微软网站开发技术系列的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。

![image 1- polymer](img/cc2235b7d3cc70b786a937b8f46e749c.png)

我现在正在做[一个愚蠢的聚合物项目](https://www.sitepoint.com/introduction-to-web-components-and-polymer-tutorial/)，它解析一个神奇宝贝数据库并返回一个神奇宝贝的图片，然后通过你的扬声器说出这个生物的名字。[这是我项目的源代码。](https://github.com/DaveVoyles/Pokemon-WebAudio?WT.mc_id=13400-DEV-sitepoint-article24)

这是我第一次使用聚合物，我当然会在一些地方被绊住。最近，它试图返回我创建的聚合物对象的成员函数。我花了很长时间才明白这一点，所以我想在本教程中与你分享。

旁注:您也可以在这里搜索我关于 Web 组件的更详细的文章。

## 错误的方式

我有一个类似这样的 Web 组件:

```
<x -radial-buttons id="radial-button-template"></x>
```

如果我试图通过它的 ID 访问它…

```
var  temp = document.querySelector("#radial-button-template");
// returns <x -radial-buttons id="radial-button-template"></x>
```

我无法使用里面的任何功能。他们回来了。所以如果我试着这样做:

```
var  temp = document.querySelector("#radial-button-template");
temp.getFirstElement  // returns undefined
```

## 为什么会这样？

这是由于影子 DOM 的封装。这既是礼物也是诅咒。在这种情况下，我访问的是元素，而不是`shadowRoot`，这将公开附加到影子 DOM 对象的公共方法。

在下一步中，您将看到如何访问自定义元素中的成员函数，以及如何返回 web 组件中更深层次的节点。

谷歌聚合物团队的 Rob Dobson 在这篇博文中对此做了很好的解释。Eric Bidleman 在他的[高级暗影 DOM 文章中进行了更详细的描述。](http://www.html5rocks.com/en/tutorials/webcomponents/shadowdom-301/?WT.mc_id=13400-DEV-sitepoint-article24)我强烈建议花时间通读这些内容，以便更好地理解这个版本的 DOM 是如何工作的。

## 做这件事的一种方法

```
var btn = document.querySelector("x-radial-buttons");
```

注意，我没有使用散列符号(#)来访问元素，就好像它是一个 ID 一样。相反，您需要简单地引用聚合物元素本身的名称:

```
document.querySelector("x-radial-buttons");
```

所以现在我可以写:

```
var temp = document.querySelector("x-radial-buttons");
// ALSO returns <x-radial-buttons id="radial-button-template"></x-radial-buttons>
```

现在，我可以像这样访问所有成员:

```
var temp = document.querySelector("x-radial-buttons");
temp.getFirstElement
// returns <paper-radio-button label="English-US" id="paper_radio_btn_en-US" on-click="{{ changeAccentUS }}" role="radio" tabindex="0" aria-checked="false" aria-label="English-US"></paper-radio-button>
```

**因此，我建议根本不要给你的`polymer-element`分配 ID。正如 Rob 在下面的评论中明确指出的，您可以按照自己的意愿查询定制元素(通过`ID`、`class`、`attr`或元素名称)并获得相同的结果。下面是他的例子:**[**http://jsbin.com/qikaya/2/edit**](http://jsbin.com/qikaya/2/edit?WT.mc_id=13400-DEV-sitepoint-article24)

## 做这件事的另一种方式…

您还可以获取聚合物元素的 ID 并访问成员函数。

这是通过[使用“聚合物就绪”](https://www.polymer-project.org/0.5/docs/polymer/polymer.html?WT.mc_id=13400-DEV-sitepoint-article24)事件来完成的。正如医生描述的那样:

Polymer 解析元素定义并异步处理它们的升级。如果您在 DOM 有机会升级之前过早地从 DOM 中获取元素，您将使用一个普通的 HTML 元素，而不是您的定制元素。

这正是我之前遇到的问题。在 Polymer 有机会升级它之前，我试图在我的`polymer-element`中抓取功能。这里有一个例子:

```
<head>
  <link rel="import" href="path/to/x-foo.html">
</head>
<body>
  <x-foo></x-foo>
  <script> window.addEventListener('polymer-ready', function(e) {
      var xFoo = document.querySelector('x-foo');
      xFoo.barProperty = 'baz';
    }); </script>
</body>
```

总之，只要您在 *polymer-ready* 事件中包装您试图调用的函数，您就可以开始了，并且可以从您的 polymer-element 中调用函数。

## 我如何使用它

```
(function (PokémonApp) {

// Grab inputs and button for speech-to-text
var form                 = document.querySelector('#player-form'),
    input                = document.querySelector('#player-input'),
    playerElement        = document.querySelector('#player-element'),
    xPokémon             = document.querySelector('#x-Pokémon'),
    btnChangeAccent      = document.querySelector('#btn-change-accent'),
    radialButtonTemplate = document.querySelector("#radial-button-template"),
  	playerAccent     = playerElement.getAttribute("accent");

// Take text from input & set it as the text that the speaker will say.
// Set the name of the Pokémon, which angular will then grab from the Pokémon DB 
input.addEventListener('input', function (e) {
	playerElement.setAttribute('text', input.value);
	xPokémon.name = input.value;
});

// Say the text when button is pressed
form.addEventListener('submit', function (e) {
	e.preventDefault();
	playerElement.speak();
	var btn = document.querySelector("x-radial-buttons");
	btn.getFirstElement();
});
```

```
<header>
<h1>article header h1</h1>
<p>This web app takes advantage of Web Components and Polymer to enable new HTML features in the browser.</p>
<p>
	In this particular case, we are using <a href="https://github.com/passy/x-Pokémon">
	the x-Pokémon web component </a> to pull the images from a database, as well as the 
	<a href="http://zenorocha.github.io/voice-elements/">voice-elements web component</a> to speak the name of the Pokémon we entered.							
</p>
<h2>Change the accent</h2>
<x-radial-buttons id="radial-button-template"></x-radial-buttons>
</header>
```

## 更多 JavaScript 学习

这可能会让你有点惊讶，但微软有许多关于开源 JavaScript 主题的免费学习，我们的任务是随着 [Microsoft Edge](http://blogs.windows.com/msedgedev/2015/04/29/introducing-microsoft-edge-the-browser-built-for-windows-10/?WT.mc_id=13400-DEV-sitepoint-article24) 的到来创造更多。

以下是我们团队关于 HTML、CSS 和 JS 的更广泛的学习系列:

*   [让你的 HTML/JavaScript 更快的实用性能技巧](http://channel9.msdn.com/Series/Practical-Performance-Tips-to-Make-Your-HTMLJavaScript-Faster/06?WT.mc_id=13400-DEV-sitepoint-article24)(从响应式设计到休闲游戏再到性能优化的 7 部分系列)
*   现代网络平台快速启动(HTML、CSS 和 JS 的基础)
*   [使用 HTML 和 JavaScript JumpStart 开发通用 Windows 应用](http://www.microsoftvirtualacademy.com/training-courses/developing-universal-windows-apps-with-html-and-javascript-jump-start?WT.mc_id=13400-DEV-sitepoint-article24)(使用您已经创建的 JS 来构建应用)
*   以及一些免费工具: [Visual Studio 社区](http://www.visualstudio.com/en-us/products/free-developer-offers-vs.aspx?WT.mc_id=13400-DEV-sitepoint-article24)、 [Azure 试用版](http://azure.microsoft.com/en-us/pricing/free-trial/?WT.mc_id=13400-DEV-sitepoint-article24)，以及[针对 Mac、Linux 或 Windows 的跨浏览器测试工具](http://dev.modern.ie/?utm_source=SitePoint&utm_medium=article24&utm_campaign=SitePoint)。

本文是微软网站开发技术系列的一部分。我们很高兴与您分享微软 Edge 和新的 T2 Edge html 渲染引擎。在 http://dev.modern.ie/获得免费的虚拟机或在你的 Mac、iOS、Android 或 Windows 设备上进行远程测试

## 分享这篇文章