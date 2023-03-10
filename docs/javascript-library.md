# JavaScript 库世界杯

> 原文：<https://www.sitepoint.com/javascript-library/>

不管喜欢与否，JavaScript 库正在 DOM 脚本领域产生巨大的影响。随着 AJAX 的成熟，支持其不断扩展的用途所需的脚本的复杂性增加，为每个项目从头开始编写定制代码变得越来越不可行。

此外，AJAX 的发展和对 Web 2.0 应用程序的兴趣日益增长，也给这个领域带来了许多新人。不足为奇的是，他们不想花费漫长而艰苦的岁月来吸收与浏览器错误和 API 差异搏斗所需的晦涩知识。

无论您是传统的 DHTML 专家还是 Web 2.0 的追随者，是时候了解一些库了。那么，外面有什么？很高兴你问了！

在过去一年左右的时间里，随着 DOM 脚本在 AJAX 的支持下在主流编码领域迅速发展，似乎无穷无尽的 JavaScript 库也加入了竞争者的行列。幸运的是，对于我们可怜的大脑来说，有四个图书馆在采用程度、文档和社区支持方面成为了明显的先行者:

*   Dojo 是一个强大的库，主要是在 JotSpot 的基础上开发的
*   Prototype，Ruby on Rails 的支柱出色的 AJAX 支持
*   Mochikit，让 JavaScript 变得不那么糟糕的 Python 库
*   雅虎用户界面库(通常简称为 YUI)，这个街区的新成员

在这四个人中，还没有明确的领先者。每个图书馆不仅在功能上，而且在不太明显的方面，如易用性、社区支持和理念，都与其他图书馆有很大的不同。在选择开发平台时，最重要的因素之一是它的理念与您的大脑工作方式的契合程度。

在本文中，我将分析每个库，以帮助您决定哪个库最适合您的开发风格和项目需求。虽然不可能涵盖每个库的每个方面，但我已经尽了最大努力来涵盖每个库的亮点，并对它们如何处理 DOM 操作、事件处理和 AJAX 的基本任务给出了一些见解。

![1537_dojo](img/340fe1c9432bfebd696071a615f48563.png)

##### 柔道训练学校

Dojo 由首席开发人员亚历克斯·罗素和大约 30 名核心贡献者维护，他最近宣布将全职致力于 Dojo。该图书馆主要由 Jot 资助，JotSpot 是一个增强的维基引擎。

Dojo 可以从 Dojo 站点下载几个版本。每个版本都将 Dojo 库的某些部分捆绑到一个 JavaScript 文件中，并允许您使用 Dojo 的导入机制动态加载库的其他部分。Dojo 最受欢迎的版本是 AJAX 版本，压缩后的版本重约 132K，包括对异步 I/O 操作(用于 AJAX 调用)、视觉效果、事件处理和 Dojo 基本库的支持。

在 Dojo 版本的基础上加载额外的函数很容易。例如，如果您想要添加加密库，您应该编写以下代码:

```
dojo.require("dojo.crypto.*");
```

然而，出于性能原因，最好根据项目的需要选择好您的版本，并避免以这种方式需要额外的库。

就特性而言，Dojo 无疑是四个库中范围最广的，并且更加注重处理性能问题和浏览器兼容性问题。它的编写也考虑到了 SVG 等技术，并且正在以一种交叉兼容的方式将 Dojo 的功能引入 SVG。

从尽可能快地处理字符串，到高效的迭代，再到 AJAX 中对后退按钮的内置支持，Dojo 真的无所不包。然而，这意味着它有一个相当大的 API，因此考虑到目前稀疏的文档，与 Dojo 相关的学习曲线是相当长的。

最近，Django 框架开始将 Dojo 与它的发行版捆绑在一起，在它的管理系统中使用它，所以这可能是增加 Dojo 文档的催化剂。

***基础知识***

Dojo 的核心是一个灵活、强大且易于使用的方法，`dojo.io.bind`。这种方法可以通过多种方式进行同步和异步调用。为了让您了解它的灵活性，我们来看一些例子:

```
// Load a text document from the server, then alert the user of the contents. 

dojo.io.bind( 

  { 

    url: "http://example.com/document.txt", 

    load: function(type, data, evt) { alert(data) }, 

    mimetype: "text/plain" 

  } 

); 

// Serialize a form and send it to the server, then evaluate the response as JavaScript! 

dojo.io.bind( 

  { 

    url: "http://example.com/comment.cgi", 

    load: function(type, json) { alert(json.myProp) }, 

    formNode: document.getElementById("formToSubmit"), 

    mimetype: "text/javascript" 

  } 

);
```

```
dojo.io.bind can also handle the back button with ease:

```
dojo.io.bind( 

  { 

    url: " http://example.com/comment.cgi", 

    load: function(type, data) { alert(data) }, 

    backButton: function() { /* do something when back is pressed */} 

  } 

);
```

在这篇介绍性文章的[中阅读更多关于`dojo.io.bind`的全部功能。](http://dojotoolkit.org/docs/intro_to_dojo_io.html)
`dojo.event`方法采用实现自己的事件处理系统的方法，而不是在浏览器的事件系统周围提供包装。这就形成了一个相当稳固的系统，并且像`dojo.io.bind`一样，它既易于使用，又展现了巨大的能力和灵活性。
你可以[在这里](http://dojotoolkit.org/docs/dojo_event_system.html)阅读更多道场活动。在此期间，让我们先睹为快，看看`dojo.event`方法的实际应用。正如所料，下面的代码由`onClick`事件触发，并使用直观和熟悉的语法。

```
dojo.event.connect(node, "onclick", "handleOnClick");
```

 ***高点和低点*** 
Dojo 的伟大之处在于它的特性非常丰富。小部件系统提供了大量有用的控件，如日期选择器、RichText 小部件，以及大量你可能会在微软的 MFC 中找到的控件。除此之外，您还可以使用 HTML、CSS 和 JavaScript 在框架上构建自己的小部件([详见本文](http://dojotoolkit.org/docs/fast_widget_authoring.html))。
但是 JavaScript 不需要仅仅局限于浏览器，Dojo 在设计时就考虑到了这一点。Dojo 的平台独立性可能使其成为桌面小部件开发的有趣平台，以及许多其他潜在的应用程序。例如，Open Laszlo 最近宣布，它正在为平台的 JavaScript 实现授权 Dojo。
Dojo 的设计有相当类似 Java 的美感，而不试图成为 Java。事实上，我认为 Dojo 将 JavaScript 作为一种语言运用得非常好。不过，该库设计的一个缺点是有时需要输入很长的包字符串来调用方法或实例化库的对象——如果 Dojo 能够提供一种将包“混合”到全局或局部范围的方法，那就太好了。如果您计划使用某个包中的许多方法，这将提供对该包的方便访问，尽管我不确定它能有多容易被合并。
此外，对于它的所有特性，Dojo 完全缺少任何可以帮助选择 DOM 元素的函数——这是 DOM 脚本编写的基础。这似乎是库中的一个很大的漏洞——如果能够使用 CSS 和/或 XPath 选择元素就太好了。类似地，虽然库中的一些对象似乎支持某种迭代框架，但 Dojo 缺少遍历数组和集合的方法，这似乎是 DOM 脚本任务的一个主要部分。此时此刻，Dojo 的文档并不珍贵。官方的 Dojo 站点包含一些远未完成的 API 文档，但是它有一些精彩的文章突出了这个库的各个方面。然而，JavaScript 社区还没有接受记录 Dojo 的艰巨任务，因此独立于主题的文章很少。
![1537_protoscript](img/bb92484f509905550e28bed35be3b895.png)
原型和脚本
原型库的开发由 37 Signals 的 Sam Stephenson 领导，与 scriptaculous 一起，作为 T2 Ruby on Rails AJAX helpers 的 JavaScript 骨干而声名鹊起。
Prototype 本身包含基本级别的功能，如 AJAX、DOM 操作、事件处理和 JavaScript 本身的一些扩展，而由 Thomas Fuchs 开发的独立但非常相关的库 scriptaculous 基于 Prototype，实现了大量的视觉效果，以及拖放和一些用户界面组件。
原型可以从原型网站的[下载，或者如果你想活在前沿，你可以直接从 Ruby on Rails subversion 库下载。它目前的重量相当紧凑，只有 54KB。scriptaculous 可从 scriptaculous 网站](http://prototype.conio.net)的[获得，并被分成三个文件:`controls.js`、`dragdrop.js`和`effects.js`，它们的重量分别为 28KB、23KB 和 32KB。默认情况下，Ruby on Rails 捆绑了所有这些文件。](http://script.aculo.us/)
关于 Prototype 的概述，请看我之前的 SitePoint 文章，[使用 Prototype 的无痛 JavaScript】。](https://www.sitepoint.com/article/painless-javascript-prototype/)
 ***基础知识*** 
就 DOM 操作而言，Prototype 很大程度上位于 [`innerHTML` /DOM 方法参数](http://www.webstandards.org/2006/04/13/dom-builder/)的`innerHTML`一边，这可能是一个加号也可能是一个减号，取决于你在这场特殊的圣战中站在哪一边。
不管怎样，对于您需要做的大多数 DOM 提升，Prototype 非常有用。一个新的但非常好的特性是许多方法被直接添加到节点本身。这里有几个例子:

```
$('form').visualEffect('fade', { duration : 3 }); 

$('loader').show();
```

这段代码在三秒钟的时间内淡出应用它的窗体。您甚至可以用自己的方法扩展节点对象。这种方法存在一些性能问题，但我相信它们大部分都已得到解决。
不幸的是，尽管 Prototype 有一套处理事件的方法，但目前它们还很不完善。`Event.observe`或多或少是一个常用的包装，但现在被斯科特·安德鲁·莱帕拉取代。然而，它很容易使用，并且能够处理大多数情况。一个好的方面是，当页面卸载时，它会自动删除所有你使用`Event.observe`设置的事件处理程序，这应该有助于防止 IE 泄漏内存。
AJAX 支持相当简单，而且开发得非常好，因为它是和 Ruby on Rails 一起开发的。它提供了许多我认为非常有用的优秀特性。
它透明地处理 JSON，甚至自动评估从服务器发回的 JavaScript。这是 Rails 中备受称赞的 RJS 技术的基础，非常强大。不过，您不需要 Rails 来利用这一点——您可以发出一个 AJAX 请求:

```
new Ajax.Request('new_comment.php',  

  {  

    data: 'comment=My comment.',  

    evalScripts: true  

  } 

);
```

然后，您可以用 JavaScript 从服务器返回页面更新:

```
 $('comments').replace('<div class="comment">My   comment.</div>'); 
```

如您所见，这是一种非常强大的 AJAX 应用程序设计方式。
`Ajax.Request`对象的另一个很好的小特性是，它自动附加 X-Requested-With HTTP 头，这使您能够判断您的请求是否来自服务器端的 AJAX。
 ***高点和低点*** 
有了原型方便才是王道。最值得注意的是，`$`函数(通过`id`选择元素)和`$$`函数(使用 CSS 选择器选择元素)提供了对页面元素的快速访问。`$$`函数甚至支持 CSS3 选择器——大多数浏览器不支持。当您将它与可枚举方法和 Prototype 的其他方便方法结合使用时，您可以得到一些非常简洁的语句。例如，使用/#c# "淫秽"类隐藏所有的`div`元素:

```
$$("div.obscene").map(Element.hide); 

$$("a[href='http://']").each(function(element)  

  { 

    Event.observe(element, 'click', openNewWindow); 

  } 

);
```

由于我们都将大部分脚本时间花在了 DOM 节点列表上，这确实给我们带来了很多好处。对我来说，紧凑和直观的 API 是 Prototype 的杀手锏。
Scriptaculous 是一个强大的、广泛的效果库，具有可靠的拖放支持，并且非常容易使用。考虑一下这个:

```
new Draggable('my_element');
```

这会产生一个用户可以拖动的元素。然后，您可以使用对象符号添加进一步的配置，如下所示:

```
new Draggable('my_element',  

  {  

    revert : true  

  } 

);
```

文档在很长一段时间内非常有限，但最近许多人填补了这一空白，使 Prototype 成为最广泛记录的大型 JavaScript 库之一，尽管该文档有点零散。没有集中的 API 文档，但是有一大堆涵盖部分库的库，以及 Jonathan Snook 的优秀的[备忘单，详细介绍了整个库](http://www.snook.ca/archives/000531.php)。prototypedoc.com 的[站点也维护了一个关于 Prototype 的非常完整的文章列表，以帮助你开始使用这个库。](http://prototypedoc.com/)

![1537_mochikit](img/3431f04f953d63e39f8b1ee43cbd2707.png)

##### Mochikit

Mochikit 是 Bob Ippolito(主要贡献者)的心血结晶，可从 Mochikit 网站获得。基本包重约 90KB(压缩)，但是您可以单独加载每个模块。Mochikit 目前还捆绑了 Python web 框架， [TurboGears](http://www.turbogears.com) 。

用鲍勃的话说，

我们从 Python、Objective-C 等语言中汲取了所有好的想法。体验并使其适应 JavaScript 的疯狂世界。

因此，在很大程度上，Mochikit 的 API 借鉴了 Python 的习惯用法。如果你熟悉 Python，你会很快掌握 Mochikit，尽管它有一个相当容易掌握的简单 API。在本文涉及的所有库中，Mochikit 的 API 感觉是最“有设计感”的。它不依赖于包风格的命名约定，而是将选定数量的函数导出到全局命名空间，这有助于使您的脚本更加紧凑。

开始使用 Mochikit 时，您应该:

1.  看鲍勃的[截屏](http://mochikit.com/screencasts/MochiKit_Intro-1.html)。

3.  使用 Mochikit 优秀的[交互解释器](http://mochikit.com/examples/interpreter/)开始抨击，就像你使用 Python 一样。这是感受库和基本了解 Mochikit 如何工作的好方法。

 ***基础知识*** 

当使用 DOM 时，Mochikit 有一些真正的技巧:

```
var warning = P(  

  { 'class' : 'warning' },  

  "Please provide a ", STRONG(null, "valid email address")  

);  

swapDOM(document.getElementById("notifications"), warning);
```

这段代码将使用相关的 W3C DOM 方法为以下 HTML 创建等效的节点树:

```
<p class="warning">Please provide a <strong>valid email address</strong></p>
```

Mochikit 代码几乎比 HTML 语法更干净！

莫奇基特。DOM module 还包含许多其他方法，用于方便 DOM 操作，比如上面提到的`swapDOM`方法(它在 tin 上执行它所说的操作)，以及`toHTML`(它将 DOM 节点转换为它所表示的 HTML)。另外，Mochikit 还为那些习惯使用它的人提供了`$`功能。

就事件处理而言，Mochikit 有一个设计良好的(如果稍微不熟悉的话)在 Mochikit 中实现的系统。信号模块。它基于这样一个概念:将一个监听器连接到一个物体发出的信号上。所有常规的 DOM 事件都是信号，但是您也可以创建自己的信号。connect 方法完成了这里的所有工作:

```
// connects the onclick signal of the element with id="thing"   

// to the function showDialog, which points to the element.  

connect($('thing'), 'onclick', showDialog);  

// connects the onsubmit signal of element id="form" to   

// formController.checkSubmit, which points to the   

// formController object.  

connect($('form'), 'onsubmit', formController, 'checkSubmit');
```

只需使用 signal 方法，您就可以让自己的对象发送信号:

```
signal(anObject, 'a_signal');
```

虽然 Mochikit 的事件系统与您通常期望的事件处理方式有所不同，但是一旦您习惯了它，它实际上非常简单而且非常好用。

AJAX 的东西由 Mochit 处理。异步模块，以及位于实现核心的延迟对象。要执行一个基本的 AJAX 调用，可以使用`loadJSONDoc`或`doSimpleXMLHttpRequest`:

```
var request = doSimpleXMLHttpRequest('get_options.php',   

    { q : 'web developer'}  

);
```

此函数返回一个延迟对象，您可以在其上设置回调:

```
request.addCallbacks(mySuccessFunction, myErrorFunction);
```

当`XMLHttpRequest`完成时，相关的回调被调用，并将`XMLHttpRequest`对象作为参数传递:

```
function mySuccessFunction(req) {  

  alert(req.responseText);  

}
```

延迟对象对于任何异步编程都是有用的，并且是一个好主意。在此阅读更多[，或观看截屏了解更多详情。](http://mochikit.com/doc/html/MochiKit/Async.html)

 ***高点和低点*** 

首先，Mochikit 的日志框架非常优秀。只需添加日志记录语句:

```
log("This is so much better than alert");  

log("ERROR This thing is broke");
```

然后，您可以使用 Mochikit 的 bookmarklet 打开一个日志窗口，查看您的日志消息。你不需要在你的页面上添加任何东西或者包含任何额外的脚本——这真的毫不费力，比任何一天都要容易。

Mochikit 还充分利用了 JavaScript 的函数式编程特性，真正增强和简化了库的 API。例如，如果您想按名称属性对一组对象进行排序，您可以使用`keyComparator`为您创建排序函数:

```
var sortedByName = people.sort(keyComparator("name"));
```

还有一个有用的`counter`函数。这将创建一个函数，该函数每次被调用都会返回一个递增 1 的值:

```
var nextId = counter();  

nextId(); //=> 1  

nextId(); //=> 2  

nextId(); //=> 3
```

还有一整套 Python 风格的迭代函数，比如`forEach`、`map`和`filter`，它们肯定会被大量使用。

就文档而言，Mochikit 有一些非常好的 API 文档，但是关于这个库的一些非常基础的部分的细节有点欠缺。特别是，在阅读了所有文档、观看了截屏并编写了几个基于 Mochikit 的脚本之后，我仍然不确定哪个版本的库最适合任何用途。我是用打包版还是主`Mochikit.js`？如何加载库的各个部分？然而，Mochikit 确实有一个邮件列表，所以这类问题的答案无疑离我们不远了。总而言之，就 JavaScript 库而言，Mochikit 可能不是你所习惯的，但它设计得非常漂亮，我很期待看到 Bob 将它带往何处。

![1537_yui](img/56a15cebd4cba16cbabf1fb1b186fd22.png)

##### 雅虎！用户界面库

雅虎！UI 库(通常被称为 YUI)是由雅虎开发的供内部使用，但最近在[developer.yahoo.com](http://www.developer.yahoo.com)与大量优秀的开发人员资源一起被开源。

YUI 与其说是一个连贯的库，不如说是一组独特的“实用程序”，它有五个核心脚本，涵盖:

*   动画

*   创建交互式、快速动态网页应用的网页开发技术

*   DOM 操作

*   拖放

*   事件处理

有六个控件，即:

*   日历

*   滑块

*   菜单

*   自动完成

*   树形视图

*   容器类(用它你可以实现所有形式的窗口样式的部件)

你可以从 developer.yahoo.com 网站下载这些脚本。

这种松散耦合的库设计方法有一个明显的优势——例如，当用户只想使用事件系统时，让他们下载 100 kb 左右的库似乎有点大材小用。

每个雅虎！库只依赖于小的 yahoo.js 文件。另一方面，这种方法为开发人员提供了一种不太一致的体验，并且可能会在库中引入一定量的重复。

YUI 令人感到尴尬的一点是，它的名字空间非常大。您对库的每个调用都需要以一个庞大的包字符串为前缀:

```
var myAnim = new YAHOO.util.Anim(  

  'test',   

  { height: {to: 10} },   

  1,   

  YAHOO.util.Easing.easeOut  

);  

myAnim.animate();
```

这看起来相当冗长，我不确定 JavaScript 是否真的需要这种程度的命名空间——通常我们不会在任何一个页面上加载那么多代码。然而，YUI 是一个简单而实用的库，它的主要焦点似乎是尽可能简单地消除浏览器差异。

 ***基础知识*** 

对于那些习惯于原生浏览器事件处理方法的人来说，YUI 的事件处理库应该有一个非常熟悉的 API。然而，它也有一些惊喜。开发者达斯汀·迪亚兹在他的网站上详细解释了一些细节。您可以像这样设置一个简单的侦听器:

```
YAHOO.util.event.addListener('object_id', 'click', callBackFunction);
```

事件库的一个非常强大的特性是事件监听器的延迟附件。本质上，如果您试图将一个侦听器附加到一个由于页面尚未加载而尚不存在的元素，它将在附加到事件之前透明地等待该页面变得可用。这是一个巧妙的解决方案，解决了困扰和挫败许多 DHTML 新手的问题。DOM 库抽象了浏览器的不一致性，允许无缝地设置样式和报告元素属性。然而，尽管它很好地处理了常见的任务，但还是有一些令人惊讶的地方:

*   get 是声名狼藉的`$`函数的 YUI 版本。

*   一个有趣的方法是 generateId，它可以用来以编程方式为元素生成 Id，这样您就可以在脚本的其他地方快速访问它们。不过，现在还不清楚为什么这种方法会被用来代替简单地存储对对象的引用，所以我很想看到它被用在一个真实的脚本中。

连接管理器包含了 YUI 所有的 AJAX 魔力，并且和其他库一样，选择不采用高级方法。它只不过为`XMLHttpRequest`提供了一个跨浏览器接口:

```
YAHOO.util.Connect.asyncRequest(  

  'GET',  

  'http://www.yahoo.com',  

  callback  

);
```

一个亮点是丰富的`callback`接口，它允许您定义一个对象来包装您的`callback`函数以及一些额外的配置。这里有一个`callback`对象的例子:

```
var callback = {  

  success: function(resp) { alert('WOO!!'); }, // called on success  

  failure: function(resp) { alert('BOO!'); }, // called on error  

  argument: arguments, // user defined arguments  

  scope: formController // scope the callbacks are called within  

}
```

 ***高点和低点*** 

雅虎提供的文档！因为图书馆很棒。该网站有正式的 API 文档、大量的例子、邮件列表以及对该库各部分主要特性的一些简短而清晰的解释。然而，与 Dojo 和 Mochikit 一样，该库并没有像 Prototype 一样吸引开发人员社区的想象力，所以目前独立的文章仍然很少。查看关于 YUI 的文章的最好地方是雅虎！开发者，[达斯汀·迪亚兹的网站](http://www.dustindiaz.com/)。

正如我上面暗示的，事件处理实现是 YUI 的主要优势之一，它与库的其他部分分离的事实意味着它可以独立于库的其他部分使用。然而，该库的其余部分虽然非常实用，但并不像 Mochikit、Dojo 和 Prototype 那样包含那么多创新功能，而且由于很长的包字符串，用 YUI 编码有时会感觉相当冗长。

然而，不断增长的组件列表非常丰富。例如，Calendar 组件支持多种语言和多种日期选择，Container 类为您提供了实现各种窗口界面的能力。使用这些组件的一个缺点是它们往往非常依赖于其他库；在讨论这个问题时，狄恩·爱德华兹强调了 treeview 控件的例子，它使用了大约 260K 的 JavaScript。

##### 哪一个赢了？

嗯，这个问题的简短答案是，没有在所有情况下都表现出色的真正突出的解决方案。

Prototype 是最全面的文档——尽管是以分散的方式。它似乎也是目前应用最广泛的库，可能是因为它确实擅长于开发人员最常完成的任务，比如选择节点和处理列表。当然，它将成为 Ruby 开发人员的自然选择，因为它遵循了许多 Ruby 习惯用法。Prototype 的另一个优点是它背后有强大的 Rails 支持，因此，有许多开发人员为 Prototype 提供错误修复和补丁。最后，它提供了丰富的附加库，如 scriptaculous、Rico 和 Behaviour，这使它成为许多开发人员的良好、可靠的选择。

另一方面，Prototype 有一个非常不完善的事件处理框架，这对于一个功能强大的库来说是一个主要问题。此外——这纯粹是个人喜好的问题——Prototype 对待事物的超级务实的方法(比如大量使用 innerHTML 属性)有时看起来有点“肮脏”。

对于较小的项目，YUI 的分离设计和全功能组件可能是一个很大的优势。很容易进入连接管理器或事件库，开始一些基本的任务，而不必经历太多的学习过程。不过，总的来说，它没有太多很酷或强大的功能。

Dojo 绝对是其中的佼佼者——你几乎总是可以依赖它来实现任何功能中最强大的部分。如果您正在计划一个非常 JavaScript 密集型的应用程序，Dojo 对性能的关注绝对是天赐之物。小部件实现对于构建复杂的 ui 也有巨大的潜力。然而它确实很大——无论是从文件大小还是 API 大小来看——所以我不推荐它用于较小的项目。

在我看来，Mochikit 是迄今为止四个中设计得最好、考虑得最周全的，Python/Twisted/Nevow 开发人员肯定会觉得它的 API 非常熟悉。然而，它的文档在某些地方有点单薄(例如，我仍然有点不确定将哪个版本的 Mochikit 发行版放在脚本标签中)。此外，它使用的一些习惯用法和函数式技术可能会让初学者或不精通函数式编程技术的人感到困惑。然而，它确实值得一看。Mochikits 的能力可能会让你吃惊——`createDOM`函数、迭代工具和异步架构是一件艺术品。

## 分享这篇文章

```