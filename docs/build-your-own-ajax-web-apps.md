# 构建自己的 AJAX Web 应用程序

> 原文：<https://www.sitepoint.com/build-your-own-ajax-web-apps/>

现在你已经准备好学习 AJAX 了。但是，到底是什么呢？术语 AJAX 指的是一组松散的技术，用于创建动态的、交互式的 web 内容。

AJAX 这个术语最初是由 Adaptive Path 的 Jesse James Garrett 在他的文章 AJAX:A New Approach To Web Applications 中提出的，是“Asynchronous JavaScript And XML”的首字母缩写。这有点拗口，但它只是描述了一种使用 JavaScript 从 web 服务器刷新页面内容而无需重新加载整个页面的技术。这与更新网页的传统方法不同，传统方法要求浏览器刷新整个页面，以便显示内容的任何更改。

类似的技术已经以这样或那样的形式存在了一段时间(通常是在一些聪明的黑客的帮助下实现的)。但是，XMLHttpRequest 类在浏览器中的可用性越来越强，吸引人的术语 AJAX 的出现，以及一些备受瞩目的例子的出现，如 [Google Maps](http://maps.google.com/) 、 [Gmail](http://mail.google.com/) 、 [Backpack](http://www.backpackit.com/) 和 [Flickr](http://flickr.com/) ，都使得这类高度交互的 web 应用程序开始在开发世界中获得牵引力。

随着 AJAX 这个术语变得越来越普遍，它的定义已经扩展到更一般地指基于浏览器的应用程序，这些应用程序的行为比传统的 web 应用程序更加动态。这种新的 AJAX web 应用程序更广泛地使用了交互技术，如就地编辑文本、拖放和 CSS 动画或过渡来影响用户界面的变化。本教程将解释这些技术，并向您展示如何开发自己的 AJAX web 应用程序。

本教程摘自我的新书 [*构建你自己的 AJAX Web 应用*](https://www.sitepoint.com/premium/library) 。在这里介绍的三章中，我们将讨论 AJAX 的基础知识，并在深入 XMLHttpRequest 的奇妙世界之前了解它是如何工作的。在我们使用它、探索它的内部工作方式、发出请求并异步更新我们的应用程序页面之后，我们开始开发我们的第一个真正的 AJAX 应用程序。

这将是一段相当长的旅程，所以我希望你已经准备好去冒险了！如果你想离线阅读这些章节，请下载。他们的 pdf 版本。但是现在，让我们对 AJAX 有一个坚实的基础。

##### 第一章。AJAX:概述

他要逃跑了，白痴！派遣战争火箭阿贾克斯！带回他的尸体！

*—卡拉将军，飞侠哥顿*

##### AJAX Web 应用程序

AJAX 对于许多 web 开发项目来说是一个很好的解决方案——它可以让 web 应用程序加速前进，并接管大量以前几乎完全由桌面应用程序占据的领域。

尽管如此，重要的是要记住，AJAX 不是一种神奇的仙尘，你可以把它洒在你的应用程序上，让它变得又快又酷。像任何其他新的开发技术一样，AJAX 并不难被误用，唯一比糟糕、乏味、老派的 web 应用程序更糟糕的是糟糕、执行糟糕的 AJAX web 应用程序。

当您以正确的方式将其应用到 web 应用程序的正确部分时，AJAX 可以显著增强用户对应用程序的体验。AJAX 可以提高应用程序的交互性和速度，最终使应用程序更容易、更有趣、更直观。

通常，AJAX 应用程序被描述为“像浏览器中的桌面应用程序”这是一个相当准确的描述——AJAX web 应用程序比传统的老式 web 应用程序响应速度快得多，并且它们可以提供类似于桌面应用程序的交互性。

但是 AJAX web 应用程序仍然是一个远程应用程序，其行为不同于可以访问本地存储的桌面应用程序。作为 AJAX 开发人员，您的部分工作是编写响应迅速且易于使用的应用程序，尽管应用程序和远程服务器之间必须进行通信。幸运的是，AJAX 工具箱为您提供了许多出色的技术来实现这一点。

 *超越简单、静态 HTML 页面的第一批 web 开发任务之一是使用来自后端数据存储的数据在 web 服务器上动态构建页面的技术。

回到 web 开发的“糟糕的旧时代”，创建这种动态的、数据库驱动的内容的唯一方法是在服务器端构建整个页面，使用 CGI 脚本(很可能是用 Perl 编写的)，或者一些可以解释脚本语言的服务器组件(如微软的 Active Server Pages)。即使是对该页面的一个单独的更改也需要从浏览器到服务器的往返——只有这样，新的内容才能呈现给用户。

在那些日子里，web 应用程序用户界面的正常模型是一个 web 表单，用户可以填写并提交给服务器。服务器将处理提交的表单，并将一个全新的页面发送回浏览器进行显示。因此，举例来说，完成一个多步骤的、基于 web 的“向导”将要求用户提交一个表单——从而提示在浏览器和服务器之间进行一次往返——每一步。

诚然，这是静态网页上的一个巨大进步，但离向最终用户呈现真正的“应用程序”体验还相差甚远。

***史前阿贾克斯***

早期的 web 开发人员立即开始寻找技巧来扩展这种简单的基于表单的模型的功能，因为他们努力创建响应性和交互性更强的 web 应用程序。这些技巧虽然相当临时和粗糙，但却是 web 开发人员朝着我们在今天的 AJAX 应用程序中看到的交互性迈出的第一步。但是，尽管这些技巧和变通方法通常提供了可维护的、有效的解决方案，但最终的代码并不好看。

**嵌套框架集**

为了显示内容的最小变化，必须重新加载整个页面，解决这个问题的一种方法是在其他框架集中嵌套框架集，通常有几层深度。这种技术允许开发人员只更新屏幕的选定区域，甚至模仿标签式导航界面的行为，在这种界面中，用户点击屏幕一部分的标签会改变另一部分的内容。

这种技术导致了可怕的、不可维护的代码，其中有大量像 EmployeeEditWizardMiddleLowerRight.asp 这样名字的页面。

**隐藏的`iframe`**

在像 Internet Explorer 4 这样的浏览器中添加了`iframe`使事情变得不那么痛苦了。完全隐藏 iframe 的能力导致了另一种巧妙方法的发展:开发人员使用隐藏的 iframe 向服务器发出 HTTP 请求，然后使用 JavaScript 和 DHTML 将内容插入页面。这提供了许多与现代 AJAX 相同的功能，包括从表单提交数据而无需重新加载页面的能力——这是通过将表单提交给隐藏的 iframe 实现的。结果由服务器返回给`iframe`，页面的 JavaScript 可以访问它。

这种方法的最大缺点(除了它毕竟是一个黑客之外)是在主文档和 iframe 中的文档之间来回传递数据的烦人负担。

**远程脚本**

另一种早期的类似 AJAX 的技术，通常被称为远程脚本，涉及到设置一个`<script>`标签的`src`属性来加载包含动态生成的 JavaScript 的页面。

这比隐藏的`iframe`攻击更干净，因为服务器上生成的 JavaScript 会直接加载到主文档中。然而，使用这种技术只能实现简单的 GET 请求。

##### AJAX 的酷在哪里

这就是为什么 AJAX 开发对 web 开发来说是一个巨大的飞跃:web 开发人员可以以更小的块与服务器通信，并根据服务器对这些请求的响应有选择地更新页面的特定区域，而不是必须将所有内容一次性、大量地发送给服务器，然后等待服务器发回新页面进行呈现。这就是 AJAX 首字母缩略词中异步一词的由来。

通过考虑异步系统的反面——同步系统，可能最容易理解异步系统的概念。在同步系统中，一切都按顺序发生。如果赛车是一个同步系统，那将是一件非常无聊的事情。第一辆发车的赛车将是第一辆冲过终点线的赛车，然后是第二辆发车的赛车，以此类推。没有超车，如果一辆车坏了，后面的车辆将被迫停下来等待机械师进行修理。

传统的 web 应用程序使用同步系统:在你请求第二个页面之前，你必须等待服务器给你发送系统的第一个页面，如图 1.1 所示。

![1542_fig1.1](img/fe58c95fdb132bdd997924fe0802fb53.png)
*图 1.1。传统的 web 应用是一个同步系统*

异步赛车会更令人兴奋。处于杆位的赛车可能会在第一个弯道被超越，而从发车区后面发车的赛车可能会迂回穿过场地，以第三名的成绩越过终点线。AJAX 应用程序中来自浏览器的 HTTP 请求正是以这种方式工作的。正是这种根据需要向服务器发出大量小请求的能力使得 AJAX 开发如此酷。图 1.2 显示了一个 AJAX 应用程序向 web 服务器发出异步请求。

![1542_fig1.2](img/46d8f0f65ef76ab88b0bcc75c3d78ee9.png)
*图 1.2。AJAX web 应用程序是一个异步系统*

最终的结果是应用程序的响应速度更快，因为用户等待请求处理的时间大大减少了，并且不必在查看结果之前等待整个新网页通过网络出现，并由浏览器呈现。

##### AJAX 技术

用于构建 AJAX web 应用程序的技术包含许多不同的编程领域，因此 AJAX 开发既不像常规应用程序开发那样简单，也不像传统的 web 开发那样容易。

另一方面，AJAX 开发包含了如此多的不同技术，这使得它变得更加有趣。下面是一个简短的列表，列出了协同工作来创建 AJAX web 应用程序的技术:

*   可扩展标记语言
*   W3C DOM
*   半铸钢ˌ钢性铸铁(Cast Semi-Steel)
*   XMLHttpRequest
*   Java Script 语言

在本章的其余部分中，我们将了解这些技术，并讨论它们在 AJAX web 应用程序中扮演的角色。

***数据交换和标记:XML***

XML (XML 代表可扩展标记语言——没有人在教科书之外这样称呼它。)是 AJAX 获得字母“x”的地方。这是幸运的，因为如果技术缩略语包含字母“x”，它们会自动被视为更酷。)

***数据交换通用语***

XML 通常作为 AJAX 应用程序中浏览器和服务器之间通信的异步 HTTP 请求中使用的主要数据格式。这个角色发挥了 XML 作为中立且相当简单的数据交换格式的优势，也意味着如果需要的话，重用或重新格式化内容相对容易。

当然，为了便于浏览器和服务器之间的交换，还有许多其他方法来格式化数据(比如 CSV(逗号分隔值)、JSON (JavaScript 对象表示法)或简单的纯文本)，但 XML 是最常用的方法之一。

***XML 作为标记***

AJAX 应用程序中的网页由 XHTML 标记组成，这实际上只是 XML 的一种形式。XHTML 作为 HTML 的继承者，和它很像。熟悉老式 HTML 的任何开发人员都很容易掌握它，但是它拥有有效 XML 的所有好处。使用 XHTML 有很多好处:

*   它提供了许多用于查看、编辑和验证 XML 的标准工具和脚本库。
*   它与更新的、XML 兼容的浏览器向前兼容。
*   它可以使用 HTML 文档对象模型(DOM)或 XML DOM。
*   在非浏览器代理中查看更容易。

开发社区中一些比较迂腐的人坚持认为人们还不应该使用 XHTML。他们坚信，既然 XHTML 是真正的 XML，就根本不应该使用它，除非它可以与适当的 HTTP `Content-Type`头`application/xhtml+xml` ( `text/xml`和`application/xml`也可以，尽管它们描述性较差)一起使用，目前，浏览器对它的支持仍然有限。(Internet Explorer 6 和 7 完全不支持。)

在实践中，您可以用`text/html`的`Content-Type`将 XHTML 提供给浏览器，因为所有主流浏览器都能正确地将所有 XHTML 文档呈现为文本/html。尽管浏览器会将您的代码视为普通的 HTML，但其他程序仍然可以将其解释为 XML，因此没有任何实际理由不使用它来“保护”您的标记。

如果您不同意我的观点，您可以选择使用旧的 HTML 4.01 标准进行开发。这仍然是一个可行的 web 标准，并且是开发 web 应用程序时完全合理的选择。

*XHTML 和这本书*

本书中的大多数代码示例将使用 XHTML 1.0 Strict。iframe 元素不是严格可用的，所以我们展示的几个使用 iframe 的代码示例将是 XHTML 1.0 过渡版的。

万维网联盟维护了一个关于 HTML 和 XHTML 之间差异的 FAQ。

***W3C 文档对象模型***

文档对象模型(DOM)是 XML 和 HTML 文档的面向对象表示，并提供了用于更改这些文档的内容、结构和样式的 API。

最初，像 Netscape Navigator 和 Internet Explorer 这样的特定浏览器提供了不同的专有方法来使用 JavaScript 操作 HTML 文档。DOM 起源于万维网联盟(W3C)的努力，它提供了一种平台和浏览器中立的方式来完成相同的任务。

DOM 将 XML 或 HTML 文档的结构表示为对象层次结构，这是标准 XML 工具进行分析的理想选择。

**DOM 操作方法**

JavaScript 提供了一个大型 API 来处理这些 DOM 结构，包括解析和操作文档。这是我们在 AJAX 应用程序中看到的对网页进行较小的、一点一点修改的主要方法之一。(另一种方法是简单地改变元素的`innerHTML`属性。这种方法在任何标准中都没有很好的文档，尽管它得到了主流浏览器的广泛支持。)

**DOM 事件**

DOM 的另一个重要功能是，它为 JavaScript 提供了一种将事件附加到 web 页面上的元素的标准方法。这使得更丰富的用户界面成为可能，因为它让用户有机会超越简单的链接和表单元素与页面进行交互。

拖放功能就是一个很好的例子，它允许用户在屏幕上拖动页面的各个部分，并将它们放到适当的位置来触发特定的功能。这种特性过去只存在于桌面应用程序中，但是现在由于 DOM 的存在，它在浏览器中也能很好地工作。

***演示:CSS***

CSS(级联样式表)提供了一种统一的方法来控制 web 应用程序中用户界面元素的外观。您可以使用 CSS 来更改页面外观的几乎任何方面，从字体大小、颜色和间距到元素的位置。

在 AJAX 应用程序中，CSS 的一个很好的用途是提供用户界面反馈(使用 CSS 驱动的动画和过渡)，或者指示用户可以与之交互的页面部分(例如，通过鼠标悬停触发颜色或外观的变化)。例如，您可以使用 CSS 转换来指示应用程序的某个部分正在等待服务器上正在处理的 HTTP 请求。

CSS 操作在 AJAX 这个术语的广义定义中占有重要地位——在各种视觉过渡和效果中，以及在拖放和就地编辑功能中。

***沟通:`XMLHttpRequest`***

`XMLHttpRequest`，一个 JavaScript 类，有一个非常容易使用的接口，向 web 服务器发送和接收 HTTP 请求和响应。`XMLHttpRequest`类使得真正的 AJAX 应用程序开发成为可能。用`XMLHttpRequest`发出的 HTTP 请求就像浏览器发出加载页面或提交表单的普通请求一样，但是用户不必离开当前加载的网页。

微软首先在 Windows 版的 Internet Explorer 5 中实现了作为 ActiveX 对象的`XMLHttpRequest`。从版本 1.0 开始，Mozilla 项目在 Mozilla 浏览器中提供了一个带有兼容 API 的 JavaScript 本地版本。(当然火狐里也有。)苹果从 1.2 版本开始在 Safari 中加入了`XMLHttpRequest`。

来自服务器的响应 XML 文档或文本字符串——可以传递给 JavaScript 以供开发人员使用，通常用于更新 web 应用程序用户界面的某些部分。

***综合起来:JavaScript***

JavaScript 是将 AJAX 应用程序结合在一起的粘合剂。它在 AJAX 开发中扮演多种角色:

*   控制使用`XMLHttpRequest`发出的 HTTP 请求
*   根据使用的数据交换格式，使用 DOM 操作方法、XSLT 或自定义方法解析从服务器返回的结果
*   通过使用 DOM 操作方法将内容插入到网页中，通过更新元素的`innerHTML`属性，或者通过更改元素的 CSS 属性，在用户界面中呈现结果数据

由于 JavaScript 在轻量级 web 编程中的长期使用历史(以及在缺乏经验的程序员手中)，它并没有被许多传统应用程序开发人员视为“严肃的编程语言”，尽管事实上，它是一种功能齐全的动态语言，能够支持面向对象的编程方法。

随着 AJAX 开发技术扩展了基于浏览器的应用程序的能力和功能，JavaScript 作为“玩具语言”的误解正在迅速改变。由于 AJAX 的出现，JavaScript 现在似乎正在经历某种复兴，可用于 AJAX 开发的 JavaScript 工具包和库数量的爆炸式增长就是事实的证明。

##### 摘要

在这一章中，我们对 AJAX 及其相关技术做了一个快速的概述。我们看到了一些可怕的编码扭曲，在过去糟糕的日子里，开发人员不得不忍受这些扭曲来创建类似于交互式 UI 的东西，我们也看到了 AJAX 如何在这些方法上提供了巨大的改进。掌握了 AJAX 的构建模块——XML、DOM、CSS、XMLHttpRequest 和 JavaScript(将它们联系在一起)——您就拥有了开始构建动态且可访问的 AJAX 站点所需的一切。

##### 第二章。基本 XMLHttpRequest

我迫不及待地想要分享这个新的奇迹，人们都将看到它的光芒，让他们都创作自己的音乐，牧师们在这个夜晚赞美我的名字。

*—狂奔，发现*

正是`XMLHttpRequest`赋予了 AJAX 真正的力量:从浏览器发出异步 HTTP 请求并下拉小块内容的能力。

很长时间以来，Web 开发人员一直在使用技巧和方法来实现这一点，同时受到令人讨厌的限制:看不见的 iframe hack 迫使我们在父文档和`iframe`中的文档之间来回传递数据，甚至“远程脚本”方法也仅限于对包含 JavaScript 的页面发出 GET 请求。

使用 XMLHttpRequest 的现代 AJAX 技术对这些蹩脚的方法进行了巨大的改进，允许您的应用程序在不完全重载页面的情况下发出 GET 和 POST 请求。

在本章中，我们将直接进入并构建一个简单的 AJAX web 应用程序——一个简单的站点监控应用程序，它将 web 服务器上的页面 pings 到一个定时的时间表。但是在我们开始发出异步 HTTP 请求来轮询服务器之前，我们需要通过处理所有浏览器的不兼容性来简化 XMLHttpRequest 类的使用，例如在一个可重用的代码库中，XMLHttpRequest 对象的不同实例化方式。

##### 一个简单的 AJAX 库

简化使用`XMLHttpRequest`类的一种方法是使用现有的代码库。由于 AJAX 开发越来越受欢迎，有几十个库、工具包和框架可以使用，使得`XMLHttpRequest`更容易使用。

但是，由于创建`XMLHttpRequest`类实例的代码相当简单，并且使用它的 API 也很容易理解，我们将只编写一个非常简单的 JavaScript 库来处理我们需要的基本内容。

逐步完成创建自己的库的过程将确保您知道`XMLHttpRequest`类是如何工作的，并且当您决定使用它们时，将帮助您从那些其他工具包或库中获得更多。

***首发`Ajax`班***

我们将从创建一个名为`Ajax`的基本类开始，在其中我们将包装`XMLHttpRequest`类的功能。

我从未用 JavaScript 做过面向对象的编程——救命！

在这一节中，我们将开始用 JavaScript 创建类和对象。如果你以前从来没有这样做过，不要担心——只要你知道面向对象编程的基础，这很简单。

在 JavaScript 中，我们不像在 Java、C++或。网络语言；我们只需编写一个构造函数来创建该类的一个实例。我们需要做的就是:

*   提供一个构造函数——这个函数的名字就是你的类的名字
*   使用关键字 this 向正在构造的对象添加属性，后跟一个句点和属性的名称
*   使用 JavaScript 的特殊函数构造器语法，以与添加属性相同的方式向对象添加方法

下面是创建一个名为`HelloWorld`的简单类的代码:

```
function HelloWorld() {  

  this.message = 'Hello, world!';  

  this.sayMessage = function() {  

    window.alert(this.message);  

  };  

}
```

JavaScript 的面向对象编程框架是非常轻量级的，但是一旦你掌握了它的窍门，它的功能会出奇的好。JavaScript 中没有更高级的面向对象特性，比如继承和多态，但是 AJAX 应用程序的客户端很少需要这些特性。这些特性有用的复杂业务逻辑应该总是在 web 服务器上，并使用`XMLHttpRequest`类来访问。

在这个例子中，我们创建了一个名为`HelloWorld`的类，它有一个属性(`message`)和一个方法(`sayMessage`)。要使用这个类，我们只需调用构造函数，如下所示:

```
var hw = new HelloWorld();  

hw.sayMessage();  

hw.message = 'Goodbye';  

hw.sayMessage();
```

这里，我们创建了一个`HelloWorld`(称为`hw`)的实例，然后使用这个对象显示两条消息。我们第一次给`sayMessage`打电话，默认“你好，世界！”将显示消息。然后，在将我们的对象的`message`属性更改为“再见”后，我们调用`sayMessage`并显示“再见”。

如果这在目前没有太大意义，也不要担心。随着我们逐步构建我们的`Ajax`类，这将变得更加清晰。

下面是我们的`Ajax`类的构造函数的开始:

```
Example 2.1\. ajax.js (excerpt)  

function Ajax() {  

  this.req = null;  

  this.url = null;  

  this.method = 'GET';  

  this.async = true;  

  this.status = null;  

  this.statusText = '';  

  this.postData = null;  

  this.readyState = null;  

  this.responseText = null;  

  this.responseXML = null;  

  this.handleResp = null;  

  this.responseFormat = 'text', // 'text', 'xml', or 'object'  

  this.mimeType = null;  

}
```

这段代码仅仅定义了我们在`Ajax`类中需要的属性，以便处理`XMLHttpRequest`对象。现在，让我们给我们的对象添加一些方法。我们需要一些功能，将建立一个`XMLHttpRequest`对象，并告诉它如何为我们提出请求。

***创建一个`XMLHttpRequest`对象***

首先，我们将添加一个`init`方法，它将为我们创建一个`XMLHttpRequest`对象。不幸的是，`XMLHttpRequest`在 Firefox 中的实现略有不同(在本书中，每当我解释 Firefox 中的工作原理时，我指的是所有基于 Mozilla 的浏览器，包括 Firefox、Mozilla、Camino 和 SeaMonkey)、Safari 和 Opera，而不是在 Internet Explorer 的原始实现中(有趣的是，Internet Explorer 第 7 版现在支持与 Firefox 相同的接口，这有望在未来简化 AJAX 开发)，所以如果您不是针对特定的浏览器，您必须尝试以多种不同的方式实例化该对象。Firefox 和 Safari 使用名为`XMLHttpRequest`的类创建`XMLHttpRequest`对象，而 Internet Explorer 版本 6 和更早版本使用内置于微软脚本引擎中的名为`ActiveXObject`的特殊类。尽管这些类有不同的构造函数，但它们的行为方式是相同的。

*跨浏览器代码*

幸运的是，大多数现代浏览器(Internet Explorer 6、Firefox 1.0、Safari 1.2 和 Opera 8，或任何这些浏览器的更新版本)总体上都很好地遵循了 web 标准，因此您不必在 AJAX 代码中进行大量特定于浏览器的分支。

这通常使得基于浏览器的 AJAX 应用程序比桌面应用程序更快地跨平台开发和部署。随着 AJAX 应用程序可用的能力和功能的增加，从用户界面的角度来看，桌面应用程序提供的优势越来越少。

`init`方法如下所示:

```
Example 2.2\. ajax.js (excerpt)  

this.init = function() {  

  if (!this.req) {  

    try {  

      // Try to create object for Firefox, Safari, IE7, etc.  

      this.req = new XMLHttpRequest();  

    }  

    catch (e) {  

      try {  

        // Try to create object for later versions of IE.  

        this.req = new ActiveXObject('MSXML2.XMLHTTP');  

      }  

      catch (e) {  

        try {  

          // Try to create object for early versions of IE.  

          this.req = new ActiveXObject('Microsoft.XMLHTTP');  

        }  

        catch (e) {  

          // Could not create an XMLHttpRequest object.  

          return false;  

        }  

      }  

    }  

  }  

  return this.req;  

};
```

`init`方法检查了创建一个`XMLHttpRequest`对象的每一种可能的方式，直到它成功地创建了一个。然后这个对象被返回给调用函数。

*优雅地降级*

保持与旧浏览器的兼容性(我所说的“旧”是指任何比我在前面提到的“现代浏览器”更旧的浏览器)需要大量额外的代码工作，因此定义应用程序应该支持哪些浏览器至关重要。

如果您知道您的应用程序将通过不支持`XMLHtmlRequest`类的旧浏览器(例如，Internet Explorer 4 和更早版本，Netscape 4 和更早版本)接收大量流量，您将需要完全不使用它，或者编写您的代码以使它适度降级。这意味着，你不能让你的功能在功能较弱的浏览器中消失，而是要编码确保这些浏览器的用户能收到功能相当的东西，尽管可能是交互性较差或易于使用的格式。

也有可能你的网站会吸引那些禁用 JavaScript 的用户。如果你想迎合这些用户，你应该在默认情况下提供一个替代的、老派的界面，然后你可以使用 JavaScript 为现代浏览器动态修改它。

***发送请求***

我们现在有了一个创建`XMLHttpRequest`的方法。所以让我们写一个函数，用它来发出请求。我们这样开始 doReq 方法:

```
Example 2.3\. ajax.js (excerpt)  

this.doReq = function() {  

  if (!this.init()) {  

    alert('Could not create XMLHttpRequest object.');  

    return;  

  }  

};
```

`doReq`的第一部分调用`init`来创建`XMLHttpRequest`类的一个实例，如果不成功，会显示一个快速警告。

**设置请求**

接下来，我们的代码调用`this.req`上的`open`方法——我们的`XMLHttpRequest`类的新实例——开始设置 HTTP 请求:

```
Example 2.4\. ajax.js (excerpt)  

this.doReq = function() {  

  if (!this.init()) {  

    alert('Could not create XMLHttpRequest object.');  

    return;  

  }  

  this.req.open(this.method, this.url, this.async);  

};
```

`open`方法有三个参数:

**1。method**–该参数标识我们将使用的 HTTP 请求方法的类型。最常用的方法是 GET 和 POST。

*方法区分大小写*

根据 HTTP 规范(RFC 2616)，这些请求方法的名称是区分大小写的。因为规范中描述的方法被定义为全部大写，所以您应该总是确保您键入的方法全部是大写字母。

**2。URL**–该参数标识被请求的页面(或者如果方法是 POST，则标识发布到的页面)。

*跨域*

正常的浏览器安全设置不允许您向另一个域发送 HTTP 请求。例如，从 ajax.net 提供的页面不能向 remotescripting.com 发送请求，除非用户允许这样的请求。

**3。异步标志**–如果该参数设置为`true`，您的 JavaScript 将继续正常执行，同时等待对请求的响应。随着请求状态的改变，事件被触发，以便您可以处理请求状态的改变。

如果将参数设置为`false`，JavaScript 将停止执行，直到服务器返回响应。这种方法的优点是比使用回调函数简单一点，因为您可以在代码中发送请求后直接开始处理响应，但是最大的缺点是，当请求被发送并在服务器上被处理以及响应被接收时，您的代码会暂停。因为与服务器异步通信的能力是 AJAX 应用程序的全部要点，所以应该设置为`true`。

在我们的`Ajax`类中，方法和异步属性被初始化为合理的缺省值(GET 和 true)，但是你必须设置目标 URL，当然。

**设置`onreadystatechange`事件处理程序**

在服务器上处理 HTTP 请求时，readyState 属性的更改会指示其进度。此属性是一个整数，表示下列状态之一，按请求开始到结束的顺序列出:

*   `0`:未初始化-`open`尚未被调用。
*   `1`:正在加载-`send`尚未调用。
*   `2`:loaded-`send`已被调用，但响应尚不可用。
*   `3`:interactive——正在下载响应，responseText 属性保存部分数据。
*   `4`:completed–响应已加载，请求已完成。

一个`XMLHttpRequest`对象通过触发一个`readystatechange`事件告诉你状态的每一个变化。在该事件的处理程序中，检查请求的`readyState`，当请求完成时(即当`readyState`变为`4`，您可以处理服务器的响应。

我们的`Ajax`代码的基本轮廓如下所示:

```
Example 2.5\. ajax.js (excerpt)  

this.doReq = function() {  

  if (!this.init()) {  

    alert('Could not create XMLHttpRequest object.');  

    return;  

  }  

  this.req.open(this.method, this.url, this.async);  

  *var self = this; // Fix loss-of-scope in inner function  

  this.req.onreadystatechange = function() {  

    if (self.req.readyState == 4) {  

      // Do stuff to handle response  

    }  

  };*  

};
```

我们稍后将讨论如何“处理响应”。现在，请记住，您需要在发送请求之前设置这个事件处理程序。

**发送请求**

使用`XMLHttpRequest`类的`send`方法启动 HTTP 请求，如下所示:

```
Example 2.6\. ajax.js (excerpt)  

this.doReq = function() {  

  if (!this.init()) {  

    alert('Could not create XMLHttpRequest object.');  

    return;  

  }  

  this.req.open(this.method, this.url, this.async);  

  var self = this; // Fix loss-of-scope in inner function  

  this.req.onreadystatechange = function() {  

    if (self.req.readyState == 4) {  

      // Do stuff to handle response  

    }  

  };  

  *this.req.send(this.postData);*  

};
```

send 方法有一个参数，用于`POST`数据。当请求是一个简单的不传递任何数据给服务器的`GET`时，就像我们当前的请求，我们将这个参数设置为 null。

*丧失范围`this`和*

 *您可能已经注意到`onreadystatechange`包含了一个看起来很奇怪的变量赋值:

```
Example 2.7\. ajax.js (excerpt)  

var self = this; // Fix loss-of-scope in inner function
```

这个新变量`self`是对使用异步事件处理程序的 JavaScript 开发人员经常遇到的“范围丢失”问题的解决方案。异步事件处理程序通常与`XMLHttpRequest`以及像`setTimeout`或`setInterval`这样的函数一起使用。

`this`关键字在面向对象的 JavaScript 代码中被用作引用“当前对象”的简写。这里有一个简单的例子——一个叫做`ScopeTest`的类:

```
function ScopeTest() {  

  this.message = 'Greetings from ScopeTest!';  

  this.doTest = function() {  

    alert(this.message);  

  };  

}  

var test = new ScopeTest();  

test.doTest();
```

这段代码将创建一个`ScopeTest`类的实例，然后调用该对象的`doTest`方法，这将显示消息“来自 ScopeTest 的问候！”简单吧？

现在，让我们给我们的`ScopeTest`类添加一些简单的`XMLHttpRequest`代码。我们将向您的 web 服务器主页发送一个简单的`GET`请求，当收到响应时，我们将显示`this.message`和`self.message`的内容。

```
function ScopeTest() {  

  this.message = 'Greetings from ScopeTest!';  

  this.doTest = function() {  

    // This will only work in Firefox, Opera and Safari.  

    this.req = new XMLHttpRequest();  

    this.req.open('GET', '/index.html', true);  

    var self = this;  

    this.req.onreadystatechange = function() {  

      if (self.req.readyState == 4) {  

        var result = 'self.message is ' + self.message;  

        result += 'n';  

        result += 'this.message is ' + this.message;  

        alert(result);  

      }  

    }  

    this.req.send(null);  

  };  

}  

var test = new ScopeTest();  

test.doTest();
```

那么，显示的是什么消息呢？答案如图 2.1 所示。

我们可以看到`self.message`是我们期待的问候消息，但是`this.message`发生了什么呢？

使用关键字`this`是引用“执行这段代码的对象”的一种便捷方式但是这有一个小问题——当从对象外部调用它时，它的含义会改变。这是所谓的执行上下文的结果。对象内部的所有代码都在同一个执行上下文中运行，但是从其他对象(如事件处理程序)运行的代码在调用对象的执行上下文中运行。这意味着，当你编写面向对象的 JavaScript 时，你将不能使用`this`关键字来引用事件处理程序代码中的对象(就像上面的`onreadystatechange`)。这个问题被称为范围丢失。

如果这个概念对你来说还不是 100%清楚，不要太担心。我们将在下一章看到这个问题的实际演示。同时，请记住，如果你在代码示例中看到变量 self，它是用来处理范围丢失问题的。*  *![1542_fig2.1](img/4ab6121fcda7796f5dea049a5f19658c.png)
*图 2.1。ScopeTest 类显示的消息*

***处理响应***

现在我们准备编写一些代码来处理服务器对我们的 HTTP 请求的响应。还记得我们在`onreadystatechange`事件处理程序中留下的“处理响应”注释吗？我们将，是时候我们写一些代码来做这些事情了！该函数需要做三件事:

1.  确定响应是否是一个错误。

3.  按照要求的格式准备回复。

5.  将响应传递给所需的处理函数。

将下面的代码包含在我们的`Ajax`类的内部函数中:

```
Example 2.8\. ajax.js (excerpt)  

this.req.onreadystatechange = function() {  

  *var resp = null;*  

  if (self.req.readyState == 4) {  

    *switch (self.responseFormat) {  

      case 'text':  

        resp = self.req.responseText;  

        break;  

      case 'xml':  

        resp = self.req.responseXML;  

        break;  

      case 'object':  

        resp = req;  

        break;  

    }  

    if (self.req.status >= 200 && self.req.status <= 299) {  

      self.handleResp(resp);  

    }  

    else {  

      self.handleErr(resp);  

    }*  

  }  

};
```

当响应完成时，在我们的`XMLHttpRequest`对象的 status 属性中返回一个指示请求是否成功的代码。status 属性包含已完成请求的 HTTP 状态代码。如果请求的页面丢失，可能是代码 404；如果服务器端脚本出错，可能是代码 500；如果请求成功，可能是代码 200，依此类推。这些代码的完整列表在 [HTTP 规范(RFC 2616)](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10) 中提供。

不擅长数字？

如果您在记住代码方面有困难，不要担心:您可以使用 statusText 属性，它包含一条简短的消息，告诉您有关错误的更多细节(例如，“未找到”、“内部服务器错误”、“确定”)。

我们的`Ajax`类将能够以三种不同的格式提供来自服务器的响应:作为普通的 JavaScript 字符串，作为可通过 W3C XML DOM 访问的 XML 文档对象，以及作为用于发出请求的实际的`XMLHttpRequest`对象。这些由`Ajax`类的`responseFormat`属性控制，可以设置为`text`、`xml`或`object`。

响应的内容可以通过我们的`XMLHttpRequest`对象的两个属性来访问:

*   `responseText`–该属性包含来自服务器的普通字符串形式的响应。如果出现错误，它将包含 web 服务器的错误页面 HTML。只要返回一个响应(也就是说，`readyState`变成 4)，这个属性就会包含数据，尽管它可能不是您所期望的。
*   `responseXML`–该属性包含一个 XML 文档对象。如果响应不是 XML，该属性将为空。

我们的`Ajax`类将其`responseFormat`属性初始化为文本，因此默认情况下，您的响应处理程序将从服务器接收 JavaScript 字符串形式的内容。如果您正在处理 XML 内容，您可以将`responseFormat`属性更改为`xml`，这将提取 XML 文档对象。

如果您想变得更有趣，还有一个选项可以使用:您可以将实际的`XMLHttpRequest`对象本身返回到您的处理函数。这使您可以直接访问诸如 status 和`statusText`属性之类的东西，并且在您想要不同地处理特定类的错误的情况下可能是有用的——例如，在 404 错误的情况下完成额外的日志记录。

**设定正确的`Content-Type`**

所有主流浏览器中的`XMLHttpRequest`实现都要求正确设置 HTTP 响应的`Content-Type`,以便将响应作为 XML 处理。用内容类型`text/xml`(或`application/xml`，甚至`application/xhtml+xml`)返回的格式良好的 XML 将正确填充`XMLHttpRequest`对象的`responseXML`属性；非 XML 内容类型将导致该属性的值为`null`或`undefined`。

然而，Firefox、Safari 和 Internet Explorer 7 提供了一种绕过`XMLHttpRequest`对 XML 文档挑剔的方法:`XMLHttpRequest`类的`overrideMimeType`方法。我们简单的`Ajax`类通过`setMimeType`方法挂钩到这一点:

```
Example 2.9\. ajax.js (excerpt)  

this.setMimeType = function(mimeType) {  

  this.mimeType = mimeType;  

};
```

这个方法设置了`mimeType`属性。

然后，在我们的`doReq`方法中，我们简单地在`try ... catch`块中调用`overrideMimeType`，就像这样:

```
Example 2.10\. ajax.js (excerpt)  

req.open(this.method, this.url, this.async);  

if (this.mimeType) {  

  try {  

    req.overrideMimeType(this.mimeType);  

  }  

  catch (e) {  

    // couldn't override MIME type  --  IE6 or Opera?  

  }  

}  

var self = this; // Fix loss-of-scope in inner function
```

在您无法控制 web 应用程序前端和后端的环境中，能够覆盖来自不合作服务器的`Content-Type`头非常重要。这是事实，因为今天的许多应用程序从许多不同的领域或来源访问服务和内容。然而，由于这种技术不能在 Internet Explorer 6 或 Opera 8 中工作，您可能会发现它不适合在您今天的应用程序中使用。

**响应处理器**

根据 HTTP 1.1 规范，任何代码在 200 和 299 之间的响应都是成功的响应。

我们定义的`onreadystatechange`事件处理程序查看 status 属性来获取响应的状态。如果代码在成功响应的正确范围内，`onreadystatechange`事件处理程序将响应传递给响应处理程序方法(由`handleResp`属性设置)。

当然，响应处理程序需要知道响应是什么，所以我们将把响应作为参数传递给它。我们将在稍后讨论 doGet 方法时看到这个过程的实际应用。

由于 handler 方法是用户定义的，所以代码还会进行粗略的检查，以确保在尝试执行该方法之前已经正确设置了该方法。

**错误处理器**

如果 status 属性指示请求有错误(即，它在 200 到 299 代码范围之外)，服务器的响应将被传递给 handleErr 属性中的错误处理程序。我们的 Ajax 类已经为错误处理程序定义了一个合理的默认值，所以我们在调用它之前不必确保它已经定义好了。

`handleErr`属性指向如下所示的函数:

```
Example 2.11\. ajax.js (excerpt)  

this.handleErr = function() {  

  var errorWin;  

  try {  

    errorWin = window.open('', 'errorWin');  

    errorWin.document.body.innerHTML = this.responseText;  

  }  

  catch (e) {  

    alert('An error occurred, but the error message cannot be '  

      + 'displayed. This is probably because of your browser's '  

      + 'pop-up blocker.n'  

      + 'Please allow pop-ups from this web site if you want to '  

      + 'see the full error messages.n'  

      + 'n'  

      + 'Status Code: ' + this.req.status + 'n'  

      + 'Status Description: ' + this.req.statusText);  

  }  

};
```

此方法检查以确保弹出窗口未被阻止，然后尝试在新的浏览器窗口中显示服务器错误页面内容的完整文本。这段代码使用了一个`try ... catch`块，因此如果用户阻止了弹出窗口，我们可以向他们显示一个精简版本的错误消息，并告诉他们如何访问更详细的错误消息。

对于初学者来说，这是一个不错的默认设置，尽管您可能希望向最终用户显示较少的信息——这完全取决于您的偏执程度。如果您想使用自己的自定义错误处理程序，可以像这样使用`setHandlerErr`:

```
Example 2.12\. ajax.js (excerpt)  

this.setHandlerErr = function(funcRef) {  

  this.handleErr = funcRef;   

}
```

**或者，一个真正的处理程序**

您可能希望使用一个函数来处理成功的响应和错误。在我们的`Ajax`类中有一个方便的方法`setHandlerBoth`，它为我们轻松地设置了这一点:

```
Example 2.13\. ajax.js (excerpt)  

this.setHandlerBoth = function(funcRef) {  

  this.handleResp = funcRef;  

  this.handleErr = funcRef;  

};
```

任何作为参数传递给`setHandlerBoth`的函数都将处理成功的响应和错误。

这种设置对于将您的类的`responseFormat`属性设置为 object 的用户可能很有用，这将导致用于发出请求的`XMLHttpRequest`对象——而不仅仅是`responseText`或`responseXML`属性的值——被传递给响应处理程序。

***中止请求***

有时候，你会从自己的经验中知道，一个网页将需要很长时间来加载。你的网页浏览器有一个停止按钮，但是你的`Ajax`类呢？这就是`abort`方法发挥作用的地方:

```
Example 2.14\. ajax.js (excerpt)  

this.abort = function() {  

  if (this.req) {  

    this.req.onreadystatechange = function() { };  

    this.req.abort();  

    this.req = null;  

  }  

};
```

该方法将`onreadystate`事件处理程序更改为一个空函数，在`XMLHttpRequest`类的实例上调用`abort`方法，然后销毁您创建的实例。这样，为被中止的请求专门设置的任何属性都会被重置。下次发出请求时，将调用`init`方法，这些属性将被重新初始化。

那么，为什么我们需要改变`onreadystate`事件处理程序呢？许多`XMLHttpRequest`的实现会在调用 abort 时触发 onreadystate 事件，以表明请求的状态已经改变。更糟糕的是，这些事件完成时的`readyState`为 4，这表明所有事情都按预期完成了(如果您想一想，这在一定程度上是正确的:只要我们调用 abort，所有事情都应该停止，并且我们的`XMLHttpRequest`实例应该准备好发送另一个请求，如果我们需要的话)。显然，我们不希望在中止请求时调用我们的响应处理程序，所以我们在调用`abort`之前删除了现有的处理程序。

 *给定我们到目前为止拥有的代码，Ajax 类只需要两件事情就可以发出请求:

*   目标 URL
*   响应的处理函数

让我们提供一个名为`doGet`的方法来设置这两个属性，并开始请求:

```
Example 2.15\. ajax.js (excerpt)  

this.doGet = function(url, hand, format) {  

  this.url = url;  

  this.handleResp = hand;  

  this.responseFormat = format || 'text';  

  this.doReq();  

};
```

您会注意到，除了两个预期的参数`url`和`hand`，该函数还有第三个参数:`format`。这是一个可选参数，允许我们更改传递给处理函数的服务器响应的格式。

如果我们不为 format 传入一个值，`Ajax`类的`responseFormat`属性将默认为一个文本值，这意味着您的处理程序将被传递`responseText`属性的值。相反，您可以将`xml`或`object`作为格式传递，这会将传递给响应处理程序的参数更改为 XML DOM 或`XMLHttpRequest`对象。

***举例:一个简单的测试页面***

终于到了把我们学过的东西放在一起的时候了！让我们创建这个`Ajax`类的一个实例，并用它来发送请求和处理响应。

现在我们的类的代码在一个名为`ajax.js`的文件中，任何我们想要使用`Ajax`类的网页都需要包含带有`<script type="text/javascript" src="ajax.js">`标签的 Ajax 代码。一旦我们的页面可以访问 Ajax 代码，我们就可以创建一个`Ajax`对象。

```
Example 2.16\. ajaxtest.html (excerpt)  

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  

<html >  

  <head>  

    <meta http-equiv="Content-Type"  

        content="text/html; charset=iso-8859-1" />  

    <title>A Simple AJAX Test</title>  

    <script type="text/javascript" src="ajax.js"></script>  

    <script type="text/javascript">  

      var ajax = new Ajax();  

    </script>  

  </head>  

  <body>  

  </body>  

</html>
```

这个脚本给了我们一个崭新的`Ajax`类的实例。现在，让我们让它做一些有用的事情。

要用我们的`Ajax`类发出最基本的请求，我们可以这样做:

```
Example 2.17\. ajaxtest.html (excerpt)  

<script type="text/javascript">  

  var hand = function(str) {  

    alert(str);  

  }  

  var ajax = new Ajax();  

  ajax.doGet('/fakeserver.php', hand);  

</script>
```

这创建了我们的`Ajax`类的一个实例，它将向一个名为`fakeserver.php`的页面发出一个简单的`GET`请求，并将结果作为文本传递回 hand 函数。如果`fakeserver.php`返回您想要使用的 XML 文档，您可以这样做:

```
Example 2.18\. ajaxtest.html (excerpt)  

<script type="text/javascript">  

  var hand = function(str) {  

    // Do XML stuff here  

  }  

  var ajax = new Ajax();  

  ajax.doGet('/fakeserver.php', hand);  

</script>
```

在这种情况下，您希望绝对确保 somepage.php 确实在提供有效的 XML，并且它的`Content-Type` HTTP 响应头被设置为`text/xml`(或者其他合适的值)。

**创建页面**

现在我们已经创建了`Ajax`对象，并为请求设置了一个简单的处理函数，是时候将代码付诸实践了。

**假服务器页面**

在上面的代码中，您可以看到请求的目标 URL 被设置为一个名为`fakeserver.php`的页面。要使用这个演示代码，您需要从同一个支持 PHP 的 web 服务器上为`ajaxtest.html`和`fakeserver.php`提供服务。你也可以从一个 IIS web 服务器上用一些简单的 ASP 来做这件事。假服务器页面是一个超级简单的页面，使用下面的 PHP 代码模拟 web 服务器的不同响应时间:

```
Example 2.19\. fakeserver.php  

<?php  

header('Content-Type: text/plain');  

sleep(rand(3, 12));  

print 'ok';  

?>
```

这就是这段小代码所做的一切:它等待 3 到 12 秒，然后打印 ok。

`fakeserver.php`代码将响应的`Content-Type`报头设置为`text/plain`。根据您返回的页面内容，您可能会选择另一个`Content-Type`作为您的响应。例如，如果您将一个 XML 文档传递回调用者，您自然会希望使用`text/xml`。

这在 ASP 中同样适用，尽管有些特性(如睡眠)不太容易实现，如下面的代码所示:

```
Example 2.20\. fakeserver.asp  

<%  

Response.ContentType = "text/plain"  

' There is no equivalent to sleep in ASP.  

Response.Write "ok"  

%>
```

在整本书中，我们所有的服务器端例子都将使用 PHP 编写，尽管它们也可以使用 ASP、ASP.NET、Java、Perl 或者任何可以通过 web 服务器提供内容的语言编写。

*使用`setMimeType`方法*

假设您有一个响应，您知道它包含一个想要解析为 XML 的有效 XML 文档，但是服务器坚持以文本/纯文本的形式提供给您。您可以通过添加对`setMimeType`的额外调用，在 Firefox 和 Safari 中将响应解析为 XML，如下所示:

```
var ajax = new Ajax();  

ajax.setMimeType('text/xml');  

ajax.doGet('/fakeserver.php', hand, 'xml');
```

当然，只有当您确定来自服务器的响应将是有效的 XML，并且您可以确定浏览器是 Firefox 或 Safari 时，才应该使用这种方法。

**点击页面**

现在是关键时刻了！点击你的本地网络服务器，加载`ajaxtest.html`，看看你会得到什么。如果一切正常，会有一会儿的延迟，然后你会看到一个标准的 JavaScript 警告，如图 2.2 所示，简单地说 ok。

![1542_fig2.2](img/9d920bf874721efc3b6093058e2b37b0.png)
*图 2.2。确认您的`Ajax`类正在按预期工作*

现在一切都很好，我们的`Ajax`类运行正常，是时候进入下一步了。

##### 示例:一个简单的 AJAX 应用程序

好吧，那么使用 AJAX 的强大功能来产生一个小小的 JavaScript 警告框，显示`"ok"`可能并不是你购买这本书时的想法。让我们对示例代码进行一些修改，使 XMLHttpRequest 更加有用。同时，我们将创建我在本章开始时提到的那个简单的监控应用程序。该应用程序将 ping 一个网站，并报告获得响应所需的时间。

***打基础***

我们将从一个链接到两个 JavaScript 文件的简单 HTML 文档开始:`ajax.js`，它包含我们的库，和`appmonitor1.js`，它包含我们的应用程序的代码。

```
Example 2.21\. appmonitor1.html  

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  

<html >  

  <head>  

    <meta http-equiv="Content-Type"  

        content="text/html; charset=iso-8859-1" />  

    <title>App Monitor</title>  

    <script type="text/javascript" src="ajax.js"></script>  

    <script type="text/javascript" src="appmonitor1.js"></script>  

  </head>  

  <body>  

    <div id="pollDiv"></div>  

  </body>  

</html>
```

您会注意到页面主体中几乎没有任何内容，只有一个`div`元素。这是非常典型的依赖 AJAX 功能的 web 应用程序。通常，AJAX 应用程序的大部分内容都是由 JavaScript 动态创建的，所以我们在页面源代码中看到的标记通常比在非 AJAX web 应用程序中看到的少得多，因为非 AJAX web 应用程序的所有内容都是由服务器生成的。然而，如果 AJAX 不是应用程序的绝对必要部分，那么应该提供应用程序的普通 HTML 版本。

我们将以一些简单的内容开始我们的`appmonitor1.js`文件，这些内容利用了我们的`Ajax`类:

```
Example 2.22\. appmonitor1.js (excerpt)  

var start = 0;  

var ajax = new Ajax();  

var doPoll = function() {  

  start = new Date();  

  start = start.getTime();  

  ajax.doGet('/fakeserver.php?start=' + start, showPoll);  

}  

window.onload = doPoll;
```

我们将使用 start 变量来记录每个请求开始的时间——这个数字将用于计算每个请求需要多长时间。我们让 start 成为一个全局变量，这样我们就不必用额外的代码来为定时请求弄乱我们的`Ajax`类的工作——我们可以在调用`Ajax`对象之前和之后立即设置 start 的值。

`ajax`变量只是保存了我们的`Ajax`类的一个实例。

`doPoll`函数实际上使用`Ajax`类发出 HTTP 请求。您应该可以从我们最初的测试页面中识别出对`doGet`方法的调用。

请注意，我们已经向目标 URL 添加了一个查询字符串，该字符串将起始值作为参数。我们实际上不会在服务器上使用这个值；我们只是用它作为一个随机值来处理 Internet Explorer 的过度缓存。IE 缓存所有用`XMLHttpRequest`发出的`GET`请求，禁用该“特性”的一种方法是在查询字符串中附加一个随机值。start 中的毫秒值可以是随机值的两倍。这种方法的一个替代方法是使用`XMLHttpRequest`类的`setRequestHeader`方法来设置请求的`If-Modified-Since`头。

最后，我们通过将`doPoll`附加到`window.onload`事件来启动一切。

***处理结果同`showPoll`***

我们传递给`doGet`的第二个参数告诉`Ajax`类将响应传递给函数`showPoll`。下面是该函数的代码:

```
Example 2.23\. appmonitor1.js (excerpt)  

var showPoll = function(str) {  

  var pollResult = '';  

  var diff = 0;  

  var end = new Date();  

  if (str == 'ok') {  

    end = end.getTime();  

    diff = (end - start) / 1000;  

    pollResult = 'Server response time: ' + diff + ' seconds';  

  }  

  else {  

    pollResult = 'Request failed.';  

  }  

  printResult(pollResult);  

  var pollHand = setTimeout(doPoll, 15000);  

}
```

这非常简单:该函数需要一个参数，如果一切正常，这个参数应该是从`fakeserver.php`返回的字符串`ok`。如果响应是正确的，代码会进行快速计算，找出响应需要多长时间，并创建一条包含结果的消息。它将该消息传递给 pollResult 进行显示。

在这个非常简单的实现中，除了预期的响应之外的任何事情都会导致一个相当简洁且无用的消息:请求失败。当我们在下一章升级这个应用程序时，我们将使我们对错误情况的处理更加健壮。

一旦`pollResult`被设置，它就被传递给`printResult`函数:

```
Example 2.24\. appmonitor1.js (excerpt)  

function printResult(str) {  

  var pollDiv = document.getElementById('pollDiv');  

  if (pollDiv.firstChild) {  

    pollDiv.removeChild(pollDiv.firstChild);  

  }  

  pollDiv.appendChild(document.createTextNode(str));  

}
```

`printResult`功能在页面中单独的`div`内显示从`showPoll`发送的消息。

注意上面代码中的测试，它用于查看我们的`div`是否有任何子节点。这将检查是否存在任何文本节点，包括我们在之前的迭代中添加到这个`div`中的文本，或者包含在页面标记的`div`中的文本，然后删除它们。如果不删除现有的文本节点，代码只会将新的结果作为新的文本节点追加到页面中:您将显示一个很长的文本字符串，更多的文本将不断追加到该字符串中。

*为什么不用`innerHTML`？*

您可以简单地更新`div`的`innerHTML`属性，就像这样:

```
document.getElementById('pollDiv').innerHTML = str;
```

属性不是 web 标准，但是所有主流浏览器都支持它。而且，正如您所看到的，它只有一行代码(与 DOM 方法所需的四行代码相比)，有时它比 DOM 方法更容易使用。这两种在页面上显示内容的方式本质上都不是更好。

在某些情况下，您可能最终会根据这两种方法的渲染速度差异来选择一种方法(`innerHTML`可能比 DOM 方法更快)。在其他情况下，您可能会根据代码的清晰程度，甚至个人喜好来做出决定。

***再次启动流程***

最后，`showPoll`通过使用`setTimeout`在 15 秒内调度对原始`doPoll`函数的调用，重新开始整个过程，如下所示:

```
Example 2.25\. appmonitor1.js (excerpt)  

var pollHand = setTimeout(doPoll, 15000);
```

代码不断调用`doPoll`函数的事实意味着一旦页面加载，HTTP 请求轮询`fakeserver.php`页面将继续这样做，直到该页面被关闭。`pollHand`变量是间隔 ID，允许您跟踪未决操作，并使用`clearTimeout`取消它。

`setTimeout`调用的第一个参数`doPoll`，是指向应用程序主函数的指针；秒表示请求之间必须经过的时间长度，以秒为单位。

***完整示例代码***

这是我们第一次试运行这个简单的监控应用程序的所有代码。

```
Example 2.26\. appmonitor1.js  

var start = 0;  

var ajax = new Ajax();  

var doPoll = function() {  

  start = new Date();  

  start = start.getTime();  

  ajax.doGet('/fakeserver.php?start=' + start, showPoll);  

}  

window.onload = doPoll;  

var showPoll = function(str) {  

  var pollResult = '';  

  var diff = 0;  

  var end = new Date();  

  if (str == 'ok') {  

    end = end.getTime();  

    diff = (end - start)/1000;  

    pollResult = 'Server response time: ' + diff + ' seconds';  

  }  

  else {  

    pollResult = 'Request failed.';  

  }  

  printResult(pollResult);  

  var pollHand = setTimeout(doPoll, 15000);  

}  

function printResult(str) {  

  var pollDiv = document.getElementById('pollDiv');  

  if (pollDiv.firstChild) {  

    pollDiv.removeChild(pollDiv.firstChild);  

  }  

  pollDiv.appendChild(document.createTextNode(str));  

}
```

为了遵循良好的软件工程原则，我将 JavaScript 代码从标记中分离出来，并将它们放在两个不同的文件中。

我将对本书的所有示例代码采用类似的方法，将每个示例的标记、JavaScript 代码和 CSS 分离到单独的文件中。这个小小的监控 app 太基础了，连 CSS 文件都没有。我们将在下一章添加一些样式来使它看起来更好。

***运行 App***

尝试在浏览器中加载该页面。将它放入 web 服务器的根目录中，并在浏览器中打开该页面。

如果`fakeserver.php`页面响应正常，您将会看到如图 2.3 所示的内容。

![1542_fig2.3](img/d397e144add12138f2314c787ff6e28d.png)
*图 2.3。运行简单监控应用程序*

##### 进一步阅读

这里有一些在线资源，可以帮助你学习本章中的技术和概念。

***JavaScript 的对象模型***

*   [http://docs.sun.com/source/816-6409-10/obj.htm](http://docs.sun.com/source/816-6409-10/obj.htm)
*   [http://docs.sun.com/source/816-6409-10/obj2.htm](http://docs.sun.com/source/816-6409-10/obj2.htm)

查看 Sun Microsystems 主办的 JavaScript 1.3 版客户端 JavaScript 指南中关于对象的这两章。第一章解释了理解如何在 JavaScript 中使用对象所需的所有基本概念。第二篇文章更深入地介绍了 JavaScript 的基于原型的继承模型，允许您利用 JavaScript 的面向对象编码的更多功能。

[这是用 JavaScript 对象创建私有实例变量的简要介绍](http://www.crockford.com/javascript/private.html)。它将帮助您更深入地了解 JavaScript 的基于原型的继承方案。

***`XMLHttpRequest`***

这里有一个来自苹果开发者连接的很好的参考页面。它很好地概述了 XMLHttpRequest 类，并提供了其方法和属性的参考表。

[本文](http://jibbering.com/2002/4/httprequest.html)最初发布于 2002 年，现在会继续更新新信息。它包括关于发出 HEAD 请求(而不仅仅是 GET 或 POST)的信息，以及 JavaScript Object Notation (JSON)和 SOAP。

[这是 XULPlanet 对 Firefox 中的`XMLHttpRequest`实现的详尽参考](http://www.xulplanet.com/references/objref/XMLHttpRequest.html)。

[这里有另一个很好的概述](http://kb.mozillazine.org/XMLHttpRequest)，它也展示了`XMLHttpRequest`对象的一些不常用的方法，比如`overrideMimeType`、`setRequestHeader`和`getResponseHeader`。同样，这篇参考文献关注的是 Firefox 中的实现。

[这是微软在 MSDN 的](http://msdn.microsoft.com/library/en-us/xmlsdk/html/xmobjxmlhttprequest.asp)关于`XMLHttpRequest`实现的文档。

##### 摘要

```
XMLHttpRequest is at the heart of AJAX. It gives scripts within the browser the ability to make their own requests and get content from the server. The simple AJAX library we built in this chapter provided a solid understanding of how XMLHttpRequest works, and that understanding will help you when things go wrong with your AJAX code (whether you're using a library you've built yourself, or one of the many pre-built toolkits and libraries listed in Appendix A, AJAX Toolkits). The sample app we built in this chapter gave us a chance to dip our toes into the AJAX pool -- now it's time to dive in and learn to swim.

##### 第三章。AJAX 中的“A”

<q>It's flying over our heads in a million pieces.</q>

*——威利·旺卡威利·旺卡&巧克力工厂*

AJAX 中的“A”代表“异步”，虽然它不像字母“X”那么酷，但“A”是 AJAX 开发如此强大的原因。正如我们在第 1 章“AJAX:概述”中所讨论的，AJAX 异步更新界面部分的能力让开发人员对我们构建的应用程序的交互性有了更好的控制，并在一定程度上推动 web 应用程序进入以前桌面应用程序的领域。

在 web 应用程序的早期，用户通过填写表单并提交表单来与数据进行交互。然后他们会等一会儿，看着浏览器的“页面加载”动画，直到服务器返回一个全新的页面。浏览器和服务器之间的每个数据事务都很大而且很明显，这使得用户很容易弄清楚发生了什么，以及他们的数据处于什么状态。

随着 AJAX 风格的开发变得越来越流行，用户可以期待更具交互性的“敏捷”用户界面。这对用户来说是一件好事，但对开发人员来说却是一个新的挑战。在 AJAX 应用程序中，用户以特定的方式修改数据，因此用户和应用程序很容易混淆数据的状态。

这两个问题的解决方案是显示应用程序的状态，让用户知道应用程序正在做什么。这使得应用程序看起来响应速度非常快，并为用户提供了关于他们的数据发生了什么的重要指导。AJAX web 应用程序开发的关键部分是区分好的 AJAX 应用程序和坏的 AJAX 应用程序。

##### 计划的应用程序增强

为了创建一个简洁的用户界面，让用户充分了解应用程序的状态，我们将使用上一章开发的监控脚本，并为其添加一些重要的功能。下面是我们要添加的内容:

*   系统管理员配置轮询间隔和超时阈值的一种方式

*   启动和停止监控过程的简单方法

*   先前请求的响应时间条形图；用户可配置历史列表中的条目数量

*   当应用程序正在发出请求时的用户通知

*   请求超时的优雅处理

图 3.1 显示了我们完成所有增强后运行的应用程序的样子。

这个应用程序的代码被分成三个文件:在`appmonitor2.html`中的标记，在`appmonitor2.js`中的 JavaScript 代码，以及在`appmonitor2.css`中的样式。首先，我们将所有需要的文件链接到`appmonitor2.html`:

```
Example 3.1\. appmonitor2.html (excerpt)   

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"   

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">   

<html >   

  <head>   

    <meta http-equiv="Content-Type"   

        content="text/html; charset=iso-8859-1" />   

    <title>App Monitor</title>   

    <script type="text/javascript" src="ajax.js"></script>   

    <script type="text/javascript" src="appmonitor2.js"></script>   

    <link rel="stylesheet" href="appmonitor2.css"   

        type="text/css" />   

  </head>   

  <body>   

  </body>   

</html>
```

![1542_fig3.1](img/8624629cc2b42aa5c0fe99f02fb95e64.png) 
 *图 3.1。正在运行的应用程序*

##### 组织代码

所有这些新功能都会给我们的应用程序增加很多复杂性，所以这是一个在我们的代码中建立某种组织的好时机(这是一个比把一切都留在全局范围内好得多的选择)。毕竟，我们正在构建一个全功能的 AJAX 应用程序，所以我们希望对它进行适当的组织。

我们将使用面向对象的设计原则来组织我们的应用程序。当然，我们将从为我们的应用程序创建一个基类——`Monitor`类开始。

通常，我们会用 JavaScript 创建这样一个类:

```
function Monitor() {   

  this.firstProperty = 'foo';   

  this.secondProperty = true;   

  this.firstMethod = function() {   

    // Do some stuff here   

  };   

}
```

这是一个很好的普通构造函数，我们可以很容易地用它来创建一个`Monitor`类(如果我们想的话，也可以创建一堆)。

 ***损失范围同`setTimeout` *** 

不幸的是，在我们的应用程序中，事情并不那么容易。我们将在我们的应用程序中使用很多对`setTimeout`(以及`setInterval`)的调用，所以创建 JavaScript 类的普通方法可能会给我们的`Monitor`类带来麻烦。

`setTimeout`函数对于延迟一段代码的执行非常方便，但是它有一个严重的缺点:它在不同于对象的执行上下文中运行代码。(我们在上一章稍微谈到了这个问题，叫做范围损失。)

这是一个问题，因为对象关键字`this`在新的执行上下文中有了新的含义。所以，当你在课堂上使用它时，它会突然失忆——它不知道它是什么！

这可能有点难以理解；让我们快速演示一下，这样您就可以实际看到这种烦恼。你可能还记得我们在上一章看到的`ScopeTest`类。首先，它是一个简单的类，只有一个属性和一个方法:

```
function ScopeTest() {   

  this.message = "Greetings from ScopeTest!";   

  this.doTest = function() {   

    alert(this.message);   

  };   

}   

var test = new ScopeTest();   

test.doTest();
```

这段代码的结果是一个可预测的 JavaScript 警告框，显示文本“来自 ScopeTest 的问候！”

让我们改变 doTest 方法，让它使用`setTimeout`在一秒钟内显示消息。

```
function ScopeTest() {   

  this.message = "Greetings from ScopeTest!";   

  this.doTest = function() {   

    var onTimeout = function() {   

      alert(this.message);   

    };   

    setTimeout(onTimeout, 1000);   

  };   

}   

var test = new ScopeTest();   

test.doTest();
```

这个版本的代码产生的警告框将显示“未定义”，而不是我们的问候消息因为我们用`setTimeout`调用了`onTimeout`，`onTimeout`在一个新的执行上下文中运行。在那个执行上下文中，这不再是指`ScopeTest`的一个实例，所以`this.message`没有任何意义。

处理这个范围丢失问题的最简单方法是将`Monitor`类变成一种特殊的类，称为单例。

**带 JavaScript 的单线图**

之所以称之为“singleton ”,是因为在任何时候都只存在该类的“单个”实例。将一个类变成单例非常容易:

```
var ScopeTest = new function() {   

  this.message = "Greetings from ScopeTest!";   

  this.doTest = function() {   

    var onTimeout = function() {   

      alert(this.message);   

    };   

    setTimeout(onTimeout, 1000);   

  };   

}
```

使用关键字 new before function 创建一个“一次性”构造函数。它创建了`ScopeTest`的一个实例，并且完成了:您不能用它来创建更多的`ScopeTest`对象。

要调用这个 singleton 对象的`doTest`方法，必须使用该类的实际名称(因为只有它的一个实例):

```
ScopeTest.doTest();
```

这很好，但是我们还没有解决范围丢失的问题。如果您现在尝试这段代码，您将得到与之前看到的相同的“未定义”消息，因为这并不引用`ScopeTest`的实例。然而，使用单例给了我们一个简单的方法来解决这个问题。我们所要做的就是在`onTimeout`中使用对象的实际名称，而不是关键字`this`:

```
var ScopeTest = new function() {   

  this.message = "Greetings from ScopeTest!";   

  this.doTest = function() {   

    var onTimeout = function() {   

      alert(ScopeTest.message);   

    };   

    setTimeout(onTimeout, 1000);   

  };   

}
```

只有一个`ScopeTest`的实例，我们使用它的实际名称而不是`this`，所以不会混淆这里指的是`ScopeTest`的哪个实例。

当您执行这段代码时，您将看到预期的值“来自 ScopeTest 的问候！”在 JavaScript 警告框中。

现在，我厌倦了在整个对象代码中使用实际的对象名，我喜欢尽可能使用这样的快捷关键字。所以，通常我会创建一个变量`self`来代替`this`，并将其指向每个方法顶部的对象名，就像这样:

```
var onTimeout = function() {   

  var self = ScopeTest;   

  alert(self.message);   

};
```

在这么短的方法中，这看起来有点傻，但是在较长的代码块中，有一个类似这样的简写解决方案是很好的，可以用来引用对象。我用`self`，但是如果你愿意，你也可以用`me`，或者`heyYou`，或者`darthVader`。

 ***创建监视器对象*** 

既然我们已经有了一个代码组织的计划，它将修复从`setTimeout`开始的范围丢失问题，那么是时候创建我们的基类`Monitor`了:

```
Example 3.2\. appmonitor2.js (excerpt)   

var Monitor = new function(){   

  this.targetURL = null;   

  this.pollInterval = null;   

  this.maxPollEntries = null;   

  this.timeoutThreshold = null;   

  this.ajax = new Ajax();   

  this.start = 0;   

  this.pollArray = [];   

  this.pollHand = null;   

  this.timeoutHand = null;   

  this.reqStatus = Status;   

}
```

前四个属性`targetURL`、`pollInterval`、`maxPollEntries`和`timeoutThreshold`将作为类初始化的一部分进行初始化。它们将采用应用程序配置中定义的值，这一点我们将在下一节中讨论。

以下是其他属性的简要概述:

*   `ajax`–Ajax 类的实例，它向我们监控的服务器发出 HTTP 请求。

*   `start`–用于记录最后一次请求发送的时间。

*   `pollArray`–保存服务器响应时间的数组；常数`MAX_POLL_ENTRIES`决定了这个数组中的项数。

*   `pollHand`、`timeoutHand`—`setTimeout`返回的间隔 id 调用两个不同的进程——主轮询进程和超时监视器，后者控制每个请求的用户定义的超时时间。

*   `reqStatus`–用于当请求正在进行时通知用户的状态动画。实现这一点的代码相当复杂，所以我们将编写另一个单例类来处理它。属性指向该类的单个实例。

##### 配置和初始化应用程序

看到这个应用程序的网站管理员可能会认为它很酷，但他或她首先想要的是一种简单的方法来配置应用程序的轮询间隔，或应用程序向其监控的站点发出请求之间的时间间隔。使用全局常量配置轮询间隔很容易。

为了使这个脚本的任何用户设置轮询间隔都非常简单，我们将把这段代码放在一个脚本元素中的`appmonitor2.html`的头部:

```
Example 3.3\. appmonitor2.html (excerpt)   

<script type="text/javascript">   

  // URL to monitor   

  var TARGET_URL = '/fakeserver.php';   

  // Seconds between requests   

  var POLL_INTERVAL = 5;   

  // How many entries bars to show in the bar graph   

  var MAX_POLL_ENTRIES = 10;   

  // Seconds to wait for server response   

  var TIMEOUT_THRESHOLD = 10;   

</script>
```

你会注意到这些变量名都是大写的。这表明它们应该像常量一样工作，即在代码早期设置的值，并且不会随着代码的执行而改变。常量是许多编程语言的一个特性，但不幸的是，JavaScript 不是其中之一。(较新版本的 JavaScript 允许用 constkeyword 设置实常量，但这一功能没有得到广泛支持(即使是许多现代浏览器)。)注意，这些常量与我们类的前四个属性直接相关:`targetURL`、`pollInterval`、`maxPollEntries`和`timeoutThreshold`。这些属性将在我们类的`init`方法中初始化:

```
Example 3.4\. appmonitor2.js (excerpt)   

this.init = function() {   

  var self = Monitor;   

  self.targetURL = TARGET_URL;   

  self.pollInterval = POLL_INTERVAL;   

  self.maxPollEntries = MAX_POLL_ENTRIES;   

  self.timeoutThreshold = TIMEOUT_THRESHOLD;   

  self.toggleAppStatus(true);   

  self.reqStatus.init();   

};
```

除了初始化我们的类的一些属性，`init`方法还调用了两个方法:`toggleAppStatus`，它负责启动和停止轮询，以及`reqStatus`对象的`init`方法。reqStatus 是我们刚才讨论过的`Status` singleton 类的实例。

这个`init`方法被绑定到页面的`window.onload`事件，如下所示:

```
Example 3.5\. appmonitor2.js (excerpt)   

window.onload = Monitor.init;
```

##### 设置用户界面

这个应用程序的第一个版本在页面加载时启动，一直运行到浏览器窗口关闭。在这个版本中，我们希望给用户一个按钮，他们可以使用这个按钮来打开或关闭轮询过程。`toggleAppStatus`方法为我们处理这个问题:

```
Example 3.6\. appmonitor2.js (excerpt)   

this.toggleAppStatus = function(stopped) {   

  var self = Monitor;   

  self.toggleButton(stopped);   

  self.toggleStatusMessage(stopped);   

};
```

好吧，所以`toggleAppStatus`并不真的做这个工作，但是它调用做这个工作的方法:`toggleButton`，它把开始按钮变成停止按钮，反之亦然，还有`toggleStatusMessage`，它更新应用程序的状态消息。让我们仔细看看这些方法。

***`toggleButton`法*** 

该方法在主应用程序的“停止”和“启动”状态之间切换。它使用 DOM 操作方法动态创建适当的按钮，为其分配正确的文本和 onclick 事件处理程序:

```
Example 3.7\. appmonitor2.js (excerpt)   

this.toggleButton = function(stopped) {   

  var self = Monitor;   

  var buttonDiv = document.getElementById('buttonArea');   

  var but = document.createElement('input');   

  but.type = 'button';   

  but.className = 'inputButton';   

  if (stopped) {   

    but.value = 'Start';   

    but.onclick = self.pollServerStart;   

  }   

  else {   

    but.value = 'Stop';   

    but.onclick = self.pollServerStop;   

  }   

  if (buttonDiv.firstChild) {   

    buttonDiv.removeChild(buttonDiv.firstChild);   

  }   

  buttonDiv.appendChild(but);   

  buttonDiv = null;   

};
```

这个方法的唯一参数`stopped`可以是`true`，表示轮询已经停止，也可以是`false`，表示轮询已经开始。

正如您在该方法的代码中看到的，按钮被创建，并被设置为如果应用程序停止则显示 Start，如果应用程序当前正在轮询服务器则显示 Stop。它还将`pollServerStart`或`pollServerStop`指定为按钮的 onclick 事件处理程序。这些事件处理程序将分别启动或停止轮询过程。

当从`init`(通过`toggleAppStatus`)调用该方法时，`stopped`被设置为`true`，因此当应用程序启动时，按钮将显示 Start。

由于这段代码调用 ID 为`buttonArea`的`div`，现在让我们将它添加到我们的标记中:

```
Example 3.8\. appmonitor2.html (excerpt)   

<body>   

  <div id="buttonArea"></div>   

</body>
```

***`toggleStatusMessage`法*** 

程序员或工程师可能只需要显示一个带有“开始”或“停止”字样的按钮来判断应用程序的状态，但大多数普通人需要一个更清晰、更明显的信息来判断应用程序正在发生什么。

这个应用程序的升级版本将在页面顶部显示一条状态消息，告诉用户应用程序的整体状态(停止或运行)以及轮询过程的状态。为了显示应用程序的状态，我们将在应用程序的状态栏中放置一条清晰的消息，说明应用程序状态:已停止或应用程序状态:正在运行。

在我们的标记中，让我们在按钮出现的位置上方插入状态消息。我们将在标记中只包含消息的“应用状态”部分。消息的其余部分将被插入到 ID 为`currentAppState`的 span 中:

```
Example 3.9\. appmonitor2.html (excerpt)   

<body>   

  <div id="statusMessage">App Status:    

      <span id="currentAppState"></span>   

  </div>   

  <div id="buttonArea"></div>   

</body>
```

`toggleStatusMessage`方法在可以在`currentAppState`范围内显示的单词之间切换:

```
Example 3.10\. appmonitor2.js (excerpt)   

this.toggleStatusMessage = function(stopped) {   

  var statSpan = document.getElementById('currentAppState');   

  var msg;   

  if (stopped) {   

    msg = 'Stopped';   

  }   

  else {   

    msg = 'Running';   

  }   

  if (statSpan.firstChild) {   

    statSpan.removeChild(statSpan.firstChild);   

  }   

  statSpan.appendChild(document.createTextNode(msg));   

};
```

一旦设置好 UI，应用程序就准备好开始轮询和记录响应时间了。

 ***检查正在进行的工作*** 

既然你已经走了这么远，能看到你的作品在工作是很好的，对吗？不幸的是，在我们的应用程序中仍然有许多未解决的问题——我们已经简单地提到了一个名为 Status 的单例类，但是我们还没有创建它，我们仍然需要编写事件处理程序。但是不要害怕！我们可以通过几个类和函数存根快速启动并运行应用程序。

我们将从用一个空方法创建 Status singleton 类开始。

```
Example 3.11\. appmonitor2.js (excerpt)   

var Status = new function() {   

  this.init = function() {   

    // don't mind me, I'm just a stub ...   

  };   

}
```

由于`Status`类被`Monitor`类使用，我们必须在`Monitor`之前声明`Status`。

然后，我们将按钮的`onclick`事件处理程序添加到`Monitor`类中。我们将让他们显示警告对话框，这样我们就知道如果幕后发生了什么事情，将会发生什么。

```
Example 3.12\. appmonitor2.js (excerpt)   

this.pollServerStart = function() {   

  alert('This will start the application polling the server.');   

};   

this.pollServerStop = function() {   

  alert('This will stop the application polling the server.');   

};
```

有了这两个简单的存根，您的应用程序现在应该可以进行测试了。

![1542_fig3.2](img/bbd927f15cff2b148683fba4e5c2a885.png) 
 *图 3.2。卑微的开始*

当你点击图 3.2 所示的开始按钮时，你会看到一个警告框，提示你有更好的事情要做。让我们开始兑现这些承诺吧。

##### 轮询服务器

第一步是充实开始按钮的`onclick`事件处理程序，`pollServerStart`:

```
Example 3.13\. appmonitor2.js (excerpt)   

this.pollServerStart = function() {   

  var self = Monitor;   

  self.doPoll();   

  self.toggleAppStatus(false);   

};
```

这段代码会立即调用`doPoll`方法，就像我们在第 2 章“基本 XMLHttpRequest”中构建的 app monitor 一样，它将负责发出一个 HTTP 请求来轮询服务器。一旦请求被发送，代码就调用`toggleAppStatus`，传递 false 来表示轮询正在进行中。

投票间隔在哪里？

您可能想知道，为什么在谈论了设置轮询间隔之后，我们的代码会直接向服务器发出请求；延时在哪里？答案是，我们不希望第一次请求就有时间延迟。如果用户点击这个按钮，在任何事情发生之前有 10 秒钟的延迟，他们会认为这个应用程序坏了。我们希望在应用程序运行时，所有后续请求之间有延迟，但是当用户第一次单击该按钮时，我们希望轮询立即开始。

这个版本的应用监视器中的`doPoll`和我们在上一章中看到的唯一区别是使用了`self`作为类属性的前缀，以及对`setTimeout`的调用。看一看:

```
Example 3.14\. appmonitor2.js (excerpt)   

this.doPoll = function() {   

  var self = Monitor;   

  var url = self.targetURL;   

  var start = new Date();   

  self.reqStatus.startProc();   

  self.start = start.getTime();   

  self.ajax.doGet(self.targetURL + '?start=' + self.start,   

      self.showPoll);   

  self.timeoutHand = setTimeout(self.handleTimeout,   

      self.timeoutThreshold * 1000);   

};
```

一旦超时阈值过去，我们对`setTimeout`的调用指示浏览器调用`handleTimeout`。我们还跟踪返回的时间间隔 ID，因此当`showPoll`收到响应时，我们可以取消对`handleTimeout`的调用。

下面是`showPoll`方法的代码，它处理来自服务器的响应:

```
Example 3.15\. appmonitor2.js (excerpt)   

this.showPoll = function(str) {   

  var self = Monitor;   

  var diff = 0;   

  var end = new Date();   

  clearTimeout(self.timeoutHand);   

  self.reqStatus.stopProc(true);   

  if (str == 'ok') {   

    end = end.getTime();   

    diff = (end - self.start) / 1000;   

  }   

  if (self.updatePollArray(diff)) {   

    self.printResult();   

  }   

  self.doPollDelay();   

};
```

这个方法做的第一件事是取消在`doPoll`结束时对`handleTimeout`的延迟调用。在这之后，我们告诉我们的`Status`类的实例停止它的动画(稍后我们将会看到细节)。

在这些调用之后，`showPoll`检查以确保响应是正常的，然后计算响应从服务器返回需要多长时间。`Ajax`类的错误处理能力应该处理来自服务器的错误，所以我们的脚本不应该返回除了`ok`之外的任何东西……尽管确认一下也无妨！

一旦计算出响应时间，`showPoll`用`updatePollArray`记录该响应时间，然后用`printResult`显示结果。我们将在下一节研究这两种方法。

最后，我们在`doPollDelay`中安排另一个轮询，这是一个非常简单的方法，一旦轮询间隔过去，就安排对`doPoll`的另一个调用:

```
Example 3.16\. appmonitor2.js (excerpt)   

this.doPollDelay = function() {   

  var self = Monitor;   

  self.pollHand = setTimeout(self.doPoll,   

      self.pollInterval * 1000);   

};
```

为了检查我们到目前为止的进展，我们需要再添加一些存根方法。首先，让我们将`startProc`和`stopProc`添加到`Status`类中:

```
Example 3.17\. appmonitor2.js (excerpt)   

var Status = new function() {   

  this.init = function() {   

    // don't mind me, I'm just a stub ...   

  };   

  this.startProc = function() {   

    // another stub function   

  };   

  this.stopProc = function() {   

    // another stub function   

  };   

}
```

让我们也给我们的`Monitor`类添加一些存根方法:

```
Example 3.18\. appmonitor2.js (excerpt)   

this.handleTimeout = function() {   

  alert("Timeout!");   

};   

this.updatePollArray = function(responseTime) {   

  alert("Recording response time: " + responseTime);   

};   

this.printResult = function() {   

  // empty stub function   

};
```

现在我们准备测试我们的进展。在你的网络浏览器中打开`appmonitor2.html`，点击开始，然后等待`fakeserver.php`从睡眠中醒来并把 ok 发送回你的页面。

您可以预期两种结果之一:要么您的页面收到响应，您会看到类似于图 3.3 所示的对话框，要么您会看到图 3.4 所示的超时消息。

![1542_fig3.3](img/9a5c89f332db8c95f33e2fdb4bc22318.png) 
 *图 3.3。AJAX 应用程序收到的响应*

如果您收到如图 3.4 所示的超时消息，请不要担心。请记住，在我们的 AJAX 应用程序中，我们的超时阈值当前被设置为 10 秒，而`fakeserver.php`当前睡眠的时间是从 3 秒到 12 秒之间随机选择的。如果随机数大于或等于 10，AJAX 应用程序将报告超时。

![1542_fig3.4](img/d6b02f9952d04830f7cd5e5f343c8ce0.png) 
 *图 3.4。你的 AJAX 应用放弃了希望*

目前，我们还没有实现停止轮询的方法，所以您需要通过重新加载页面或关闭浏览器窗口来停止轮询。

##### 处理超时

如果您已经运行了我们到目前为止编写的代码，您可能已经注意到，即使报告了超时，您也会看到一条消息报告请求的响应时间。这是因为 handleTimeout 目前只是一个简单的存根。让我们看看如何在这个存根上构建，这样我们就不会有这个副作用了。

```
handleTimeout is basically a simplified version of showPoll: both methods are triggered by an asynchronous event (a call to setTimeout and an HTTP response received by an XMLHttpRequest object respectively), both methods need to record the response time (in a timeout's case, this will be 0), both methods need to update the user interface, and both methods need to trigger the next call to doPoll. Here's the code for handleTimeout:

```

```
Example 3.19\. appmonitor2.js (excerpt)   

this.handleTimeout = function() {   

  var self = Monitor;   

  if (self.stopPoll()) {   

    self.reqStatus.stopProc(true);   

    if (self.updatePollArray(0)) {   

      self.printResult();   

    }   

    self.doPollDelay();   

  }   

};
```

这里，`handleTimeout`调用 stopPoll 来停止我们的应用程序轮询服务器。它记录发生了超时，更新用户界面，最后通过`doPollDelay`建立对`doPoll`的另一个调用。我们将停止轮询的代码移到了一个单独的函数中，因为我们以后需要重新访问它并增强它。目前，`stopPoll`方法仅仅通过`Ajax`类的`abort`方法中止 HTTP 请求；然而，有一些情况是这个函数不能处理的。我们将在稍后创建完整的代码来停止轮询过程时解决这些问题，但是出于处理超时的目的，`stopPoll`是可以的。

```
Example 3.20\. appmonitor2.js (excerpt)   

this.stopPoll = function() {   

  var self = Monitor;   

  if (self.ajax) {   

    self.ajax.abort();   

  }   

  return true;   

};
```

现在，当我们重新加载应用程序时，超时完全按照我们的预期执行。

##### 响应时间条形图

现在，我们的新版本监控应用程序的肉！我们希望应用程序显示过去响应时间的列表，而不仅仅是最近一次的单个条目，并且我们希望以一种快速、易读的方式显示该列表。运行条形图显示是这项工作的完美工具。

***`pollArray`***中的跑步列表

所有的响应时间将进入一个数组，该数组存储在`Monitor`类的`pollArray`属性中。我们用直观命名的`updatePollArray`方法来更新这个数组。这是一个非常简单的方法，看起来像这样:

```
Example 3.21\. appmonitor2.js (excerpt)   

this.updatePollArray = function(pollResult) {   

  var self = Monitor;   

  self.pollArray.unshift(pollResult);   

  if (self.pollArray.length > self.maxPollEntries) {   

    self.pollArray.pop();   

  }   

  return true;   

};
```

代码非常简单，尽管我们在其中使用的一些函数的名称有点令人困惑。

一个`Array`对象的`unshift`方法将一个新项目放入数组的第一个元素，并将数组的其余内容移动一个位置，如图 3.5 所示。

![1542_fig3.5](img/eab57ce999cee13fb7612937f458c2ad.png) 
 *图 3.5。使用`unshift`插入水果*

当数组超过用户定义的最大长度时，`updatePollArray`通过从末尾“弹出”一项来截断它。这是通过`pop`方法实现的，它只是删除数组的最后一项。(注意，方法名`pop`可能看起来很奇怪，但是一旦你理解了一种叫做堆栈的数据结构，它就更有意义了，堆栈中存储了许多条目，这些条目只能按照它们被添加到堆栈中的相反顺序来访问。我们将一个项目“推入”堆栈以添加它，并从堆栈中“弹出”一个项目以检索它。`pop`方法最初是为使用数组作为堆栈的开发人员设计的，但是在这里我们只是为了删除数组中的最后一项而改变了它的用途。)我们将项目附加到数组的顶部并从底部移除项目的原因是，在我们的显示中，我们希望最新的条目出现在顶部，而较旧的条目逐渐向下移动到底部。

 ***显示结果*** 

一旦我们更新了`pollArray`中的结果，我们就可以使用`printResult`方法显示它们。这实际上是很酷的部分:用户将直接体验到我们的 AJAX 应用程序和需要整个页面刷新来更新内容的老式应用程序之间的区别。

*渲染页面片段*

在 AJAX 行话中，保存响应时间列表的页面块被称为部分页面。这是指网页中独立于页面其余部分更新的区域。

为响应服务器的异步请求而更新网页的块被称为“部分呈现页面”

`printResult`方法遍历`pollArray`，并使用 DOM 方法在 ID 为`pollResults`的`div`中绘制投票结果列表。我们首先将`div`添加到我们的标记中:

```
Example 3.22\. appmonitor2.html (excerpt)    

<body>    

  <div id="statusMessage">App Status:     

    <span id="currentAppState"></span>    

  </div>    

  <div id="pollResults"></div>    

  <div id="buttonArea"></div>    

</body>
```

现在我们已经为`printResult`方法做好了准备:

```
Example 3.23\. appmonitor2.js (excerpt)    

this.printResult = function() {    

  var self = Monitor;    

  var polls = self.pollArray;    

  var pollDiv = document.getElementById('pollResults');    

  var entryDiv = null;    

  var messageDiv = null;    

  var barDiv = null;    

  var clearAll = null;    

  var msgStr = '';    

  var txtNode = null;    

  while (pollDiv.firstChild) {    

    pollDiv.removeChild(pollDiv.firstChild);    

  }    

  for (var i = 0; i < polls.length; i++) {    

    if (polls[i] == 0) {    

      msgStr = '(Timeout)';    

    }    

    else {    

      msgStr = polls[i] + ' sec.';    

    }    

    entryDiv = document.createElement('div');    

    messageDiv = document.createElement('div');    

    barDiv = document.createElement('div');    

    clearAll = document.createElement('br');    

    entryDiv.className = 'pollResult';    

    messageDiv.className = 'time';    

    barDiv.className = 'bar';    

    clearAll.className = 'clearAll';    

    if (polls[i] == 0) {    

      messageDiv.style.color = '#933';    

    }    

    else {    

      messageDiv.style.color = '#339';    

    }    

    barDiv.style.width = (parseInt(polls[i] * 20)) + 'px';    

    messageDiv.appendChild(document.createTextNode(msgStr));    

    barDiv.appendChild(document.createTextNode('u00A0'));    

    entryDiv.appendChild(messageDiv);    

    entryDiv.appendChild(barDiv);    

    entryDiv.appendChild(clearAll);    

    pollDiv.appendChild(entryDiv);    

  }    

};
```

这里还挺多的，我们一步一步来看这个方法。

```
Example 3.24\. appmonitor2.js (excerpt)    

while (pollDiv.firstChild) {    

  pollDiv.removeChild(pollDiv.firstChild);    

}
```

在初始化一些变量之后，这个方法从`pollDiv`中删除所有的东西:`while`循环重复使用`removeChild`从`pollDiv`中删除所有的子节点。

接下来是一个简单的 for 循环，它遍历更新后的结果数组并显示它们。

我们为数组中每一项的结果生成一条消息。正如您在下面看到的，超时(记录为 0)会生成一条消息`(Timeout)`。

```
Example 3.25\. appmonitor2.js (excerpt)    

if (polls[i] == 0) {    

  msgStr = '(Timeout)';    

}    

else {    

  msgStr = polls[i] + ' sec.';    

}
```

接下来，我们使用 DOM 方法为列表中的每个条目动态添加标记。实际上，我们用 JavaScript 为列表中的每个条目构造了以下 HTML:

```
<div class="pollResult">    

    <div class="time" style="color: #339;">8.031 sec.</div>    

    <div class="bar" style="width: 160px;">&nbsp;</div>    

    <br class="clearAll"/>    

</div>
```

条形`div`的宽度会发生变化，以反映实际的响应时间，超时显示为红色，但除此之外，列表中的所有条目都是相同的。请注意，您必须在`div`中放置一些东西来显示它的背景颜色。即使给`div`一个固定的宽度，如果`div`为空，背景色也不会显示。这很烦人，但是很容易解决:我们可以用一个不间断的空格字符填充`div`。

让我们来看看我们将用来插入这个标记的代码:

```
Example 3.26\. appmonitor2.js (excerpt)    

entryDiv = document.createElement('div');    

messageDiv = document.createElement('div');    

barDiv = document.createElement('div');    

clearAll = document.createElement('br');    

entryDiv.className = 'pollResult';    

messageDiv.className = 'time';    

barDiv.className = 'bar';    

clearAll.className = 'clearAll';    

if (polls[i] == 0) {    

  messageDiv.style.color = '#933';    

}    

else {    

  messageDiv.style.color = '#339';    

}    

barDiv.style.width = (parseInt(polls[i] * 20)) + 'px';    

messageDiv.appendChild(document.createTextNode(msgStr));    

barDiv.appendChild(document.createTextNode('u00A0'));    

entryDiv.appendChild(messageDiv);    

entryDiv.appendChild(barDiv);    

entryDiv.appendChild(clearAll);    

pollDiv.appendChild(entryDiv);
```

如果您从未使用过 DOM 操纵函数，这段代码可能看起来很复杂，但它确实非常简单。我们使用名副其实的`createElement`方法来创建元素；然后我们给每个元素对象的属性赋值。

就在`if`语句之后，我们可以看到根据生成每个响应所用的秒数来设置条`div`的像素宽度的代码。我们将这个时间数字乘以 20 来得到一个合理的宽度，但是您可能希望使用一个更大或更小的数字，这取决于页面上有多少水平空间可用。

为了给元素添加文本，我们将`createTextNode`与`appendChild`结合使用，后者也用于将元素放入其他元素中。

 *`createTextNode`和不间断空格*

在上面的代码中，我们使用`u00A0`创建了一个不间断的空格。如果我们试图在这里使用普通的`&nbsp;`实体，`createTextNode`将试图通过将&符号转换为`&amp;`来“提供帮助”；这样做的结果是`&nbsp;`显示在你的页面上。解决方法是使用转义的 unicode 不换行空格:`u00A0`。

![1542_fig3.6](img/a0128446f8e7c30a490ca23f43071aa8.png) 
 *图 3.6。应用程序开始成形*

最后一段代码将所有 div 元素放在一起，然后将 pollResult div 放在 pollResults div 中。图 3.6 显示了正在运行的应用程序。

“等一下，”你可能会想。“我们应该看到的条形图在哪里？”

第一个条在那里，但是它是以白底白字显示的，这是非常无用的。让我们通过应用程序的 CSS 使它可见:

```
Example 3.27\. appmonitor2.css (excerpt)    

.time {    

  width: 6em;    

  float: left;    

}    

.bar {    

  background: #ddf;    

  float: left;    

}    

.clearBoth {    

  clear: both;    

}
```

CSS 中的主要兴趣点是对`time`和`bar div`元素的`float: left`声明，它们组成了时间列表和条形图中的彩色条。使它们向左浮动是它们并排出现的原因。然而，为了使这种定位技术起作用，具有`clearBoth`类的元素必须紧跟在这两个`div`之后出现

这是您可以看到 AJAX 运行的地方。它使用了所有这些不同技术的一些片段——T0、W3C DOM 和 CSS——连接在一起并由 JavaScript 控制。程序员经常遇到 CSS 和在代码中构建界面元素的最大问题。

作为一名 AJAX 程序员，你可以尝试依靠一个库来为你处理 CSS，或者你可以学到足够的东西来完成工作。认识一个乐于回答这个话题的聪明人，或者有一本关于 CSS 的好书(例如，SitePoint 的*CSS 选集:101 个基本技巧、诀窍&黑客*)是很方便的。

![1542_fig3.7](img/91e7ec2126b13db22ef836fa55342f1c.png) 
 *图 3.7。我们条形图的开端*

现在我们的 CSS 已经就绪，我们可以在应用程序显示中看到条形图，如图 3.7 所示。

 ***停止应用*** 

在应用程序运行之后，`pollServerStart`方法的最后一个动作是调用`toggleAppStatus`来切换应用程序的外观。`toggleAppStatus`将状态显示更改为 App Status: Running，将开始按钮切换为停止按钮，并将`pollServerStop`方法附加到按钮的`onclick`事件。

`pollServerStop`方法停止正在进行的轮询过程，然后切换回应用程序，使它看起来像是被正确地停止了:

```
Example 3.28\. appmonitor2.js (excerpt)    

this.pollServerStop = function() {    

  var self = Monitor;    

  if (self.stopPoll()) {    

    self.toggleAppStatus(true);    

  }    

  self.reqStatus.stopProc(false);    

};
```

这段代码重用了我们在本章前面添加的`stopPoll`方法。此时，该方法所做的只是中止当前的 HTTP 请求，这在我们处理超时时是没问题的。然而，这个方法还需要处理另外两种情况。

第一种情况发生在轮询间隔期间调用方法时(即，在我们收到对 HTTP 请求的响应之后，但在发送下一个请求之前)。在这个场景中，我们需要取消对`doPoll`的延迟调用。

该方法必须能够处理的第二种情况是在发送请求之后、接收响应之前调用`stopPoll`。在这种情况下，需要取消超时处理程序。

当我们跟踪两个调用的间隔 id 时，我们可以修改`stopPoll`来处理对`clearTimeout`的两个调用:

```
Example 3.29\. appmonitor2.js (excerpt)    

this.stopPoll = function() {    

  var self = Monitor;    

  clearTimeout(self.pollHand);    

  if (self.ajax) {    

    self.ajax.abort();    

  }    

  clearTimeout(self.timeoutHand);    

  return true;    

};
```

现在，您应该能够通过单击条形图下方的 Start/Stop 按钮来停止和启动轮询过程。

##### 状态通知

AJAX 异步更新内容的能力，以及更新可能只影响页面的一小部分的事实，使得状态通知的显示成为 AJAX 应用程序设计和开发的关键部分。毕竟，你的应用程序的用户需要知道应用程序在做什么。

在过去的 web 开发中，当整个页面必须重新加载以反映其内容的任何变化时，终端用户完全清楚应用程序何时与服务器通信。但是我们的 AJAX web 应用程序可以在后台与服务器对话，这意味着用户看不到完整的页面重新加载，否则这将表明发生了一些事情。

那么，AJAX 应用程序的用户如何知道页面正在与服务器通信呢？与浏览器 chrome 中显示的老式旋转地球仪或挥舞旗帜动画不同，AJAX 应用程序通常会在小动画或视觉过渡的帮助下通知用户正在进行处理。通常用 CSS 来实现，这些过渡抓住用户的眼睛——而不会分散注意力！—并提供关于应用程序正在做什么的提示。好的 AJAX 应用程序设计的一个重要方面是开发这类通知。

 ***状态动画*** 

因为我们已经在应用程序的顶部有了一个小栏，告诉用户应用程序是在运行还是停止，这是一个显示更多状态信息的合理位置。

像旋转球或奔跑的狗这样的动画是表明应用程序正忙的一种很好的方式——通常，您会希望显示一个使用运动来表示活动的图像。然而，我们不希望使用一个会将用户的注意力从列表上引开的提示，或者在人们试图阅读结果时分散他们的注意力，所以我们将只使用如图 3.8 所示的缓慢、脉动的动画。

这个动画具有轻量级的额外优势，并且易于在 CSS 中实现——不需要 Flash 播放器，也没有笨重的 GIF 图像需要一帧一帧地下载。

白色条的最右边是未使用的空间，这使它成为这种通知的理想位置:它在用户界面的顶部，所以很容易看到，但它在右边，所以它不在试图阅读结果列表的人的旁边。

![1542_fig3.8](img/93657d126dea67e26289c4351fee74da.png) 
 *图 3.8。我们的脉动状态动画*

为了制作这个动画，我们将在文档中的状态消息`div`下面添加一个 ID 为`pollingMessage`的`div`:

```
Example 3.30\. appmonitor2.html (excerpt)    

<body>    

  <div id="statusMessage">App Status:     

    <span id="currentAppState"></span>    

  </div>    

  <div id="pollingMessage"></div>    

  <div id="pollResults"></div>    

  <div id="buttonArea"></div>    

</body>
```

向您的样式表添加一个 CSS 规则来定位这个`div`:

```
Example 3.31\. appmonitor2.css (excerpt)    

#pollingMessage {     

  float: right;    

  width: 80px;    

  padding: 0.2em;    

  text-align: center;    

}
```

该动画现在位于页面的右侧。

当你在浏览器中打开页面时，你将看不到动画——此刻它只是一个白底白框。如果你愿意，可以给`pollingMessage`添加一些内容，看看它的位置。

 *`setInterval`和*范围的丧失

JavaScript setInterval 是处理重复出现的任务的一种显而易见的简单方法——例如，控制脉冲动画。

使用`setInterval`的所有 CSS 旋转都会产生一些相当有趣和庞大的代码。因此，正如我之前提到的，将状态动画的代码放到它自己的类中是有意义的——我们可以从`Monitor`类中引用和使用它。

一些读到这里的聪明的开发人员可能已经猜到了`setInterval`和`setTimeout`面临着同样的范围丢失问题:对象关键字`this`丢失了。因为在我们的监控应用程序中，我们只需要处理一个状态动画，所以采取权宜之计，让我们的`Status`类成为单例类是有意义的，就像我们对`Monitor`类所做的那样。

 ***设置`Status` *** 

让我们首先向我们已经编写的状态存根添加一些属性，以便让前面的代码工作:

```
Example 3.32\. appmonitor2.js (excerpt)    

var Status = new function() {    

  this.currOpacity = 100;    

  this.proc = 'done'; // 'proc', 'done' or 'abort'    

  this.procInterval = null;    

  this.div = null;    

  this.init = function() {    

    // don't mind me, I'm just a stub ...    

  };    

  this.startProc = function() {    

    // another stub function    

  };    

  this.stopProc = function() {    

    // another stub function    

  };    

}
```

`Status`对象有四个属性:

*   `currOpacity`属性跟踪`pollingMessage` `div`的不透明度。我们使用`setInterval`来快速改变这个`div`的不透明度，这就产生了脉冲和渐变的效果。

*   `proc`属性是一个三态开关，它指示 HTTP 请求是当前正在进行、已经成功完成还是在完成前被中止。

*   `procInterval`属性用于存储控制动画的`setInterval`进程的间隔 ID。我们将使用它来停止正在运行的动画。

*   `div`属性是对`pollingMessage` `div`的引用。`Status`类操纵`pollingMessage` `div`的 CSS 属性来创建动画。

**初始化**

需要一个`init`方法将`div`属性绑定到`pollingMessage`:

```
Example 3.33\. appmonitor2.js (excerpt)    

this.init = function() {    

  var self = Status;    

  self.div = document.getElementById('pollingMessage');    

  self.setAlpha();    

};
```

`init`方法还包含对名为`setAlpha`的方法的调用，这是我们稍后将看到的 IE 变通方法所需要的。

 ***浏览器内存泄露*** 

用作类属性的 DOM 元素引用(指向`div`、`td`或`span`元素等的变量)是 Internet Explorer 中内存泄漏的一个众所周知的原因。如果您销毁一个类的实例而没有清除这些属性(通过将它们设置为`null`)，内存将不会被回收。

让我们给我们的`Monitor`类添加一个处理`window.onunload`事件的清理方法，如下所示:

```
Example 3.34\. appmonitor2.js (excerpt)    

window.onunload = Monitor.cleanup;
```

该方法通过调用类的`cleanup`方法并将`reqStatus`属性设置为`null`来清理`Status`类:

```
Example 3.35\. appmonitor2.js (excerpt)    

this.cleanup = function() {    

  var self = Monitor;    

  self.reqStatus.cleanup();    

  self.reqStatus = null;    

};
```

`Status`类中的`cleanup`方法执行 IE 内务处理:

```
Example 3.36\. appmonitor2.js (excerpt)    

this.cleanup = function() {    

  Status.div = null;    

};
```

如果我们不将`div`引用设置为`null`，那么 Internet Explorer 将把分配给该变量的内存保留在一个死夹中，每次重新加载页面时，您都会看到内存使用气球。

实际上，对于我们的小应用程序来说，这不是什么大问题，但是对于拥有大量 DHTML 的大型 web 应用程序来说，这可能会成为一个严重的问题。养成清理代码中 DOM 引用的习惯是很好的，这样就不会成为问题。

***`displayOpacity`法*** 

`Status`类中的核心代码位于`displayOpacity`方法中。这包含了改变`pollingMessage` `div`的 CSS 属性所必需的特定于浏览器的代码。代码如下:

```
Example 3.37\. appmonitor2.js (excerpt)    

this.displayOpacity = function() {    

  var self = Status;    

  var decOpac = self.currOpacity / 100;    

  if (document.all && typeof window.opera == 'undefined') {    

    self.div.filters.alpha.opacity = self.currOpacity;    

  }    

  else {    

    self.div.style.MozOpacity = decOpac;    

  }    

  self.div.style.opacity = decOpac;    

};
```

对象的`currOpacity`属性表示`pollingMessage` `div`应该设置的不透明度。我们的实现使用 Internet Explorer 使用的从 0 到 100 的整数标度，而不是 Mozilla 和 Safari 预期的从 0 到 1 的分数标度。这个选择只是个人喜好；如果你更喜欢使用小数值，那就用一切方法吧。

在该方法中，您将看到对`document.all`的测试——只有 IE 和 Opera 支持该属性——以及对`window.opera`的测试，不出所料，只有 Opera 支持该属性。因此，只有 IE 应该执行这个 if 语句的 if 子句。在`if`语句的这个 IE 分支中，专有的`alpha.opacity`属性用于设置不透明度，而在`else`子句中，我们使用更老的`MozOpacity`属性，它受更老的基于 Mozilla 的浏览器支持。

最后，该方法以符合标准的方式设置不透明度:使用`opacity`属性，这最终将在所有符合标准的浏览器中得到支持。

 *IE 抓到你了*

Internet Explorer 版本 6 是一个较旧的浏览器，在试图呈现基于不透明度的 CSS 更改时会遇到一些问题。

幸运的是，第一个问题很容易通过添加我们的`pollingMessage` CSS 规则来解决:

```
Example 3.38\. appmonitor2.css (excerpt)    

#pollingMessage {    

  float: right;    

  width: 80px;    

  padding: 0.2em;    

  text-align: center;    

  background: #fff;    

}
```

背景属性的添加解决了 Internet Explorer 的第一个具体问题。如果我们想在 IE 中改变一个元素的不透明度，我们必须设置它的背景颜色，否则文本会显示出锯齿状的边缘。请注意，将背景设置为透明不起作用:它必须设置为特定的颜色。

如果你想让你的 CSS 文件有效，第二个问题就有点棘手了。IE 不会让你改变`style.alpha.opacity`，除非它首先在样式表中声明。现在，如果您不介意阻止您的样式表被 W3C 验证器传递，很容易通过添加另一个声明来解决这个问题:

```
Example 3.39\. appmonitor2.css (excerpt)    

#pollingMessage {    

  float: right;    

  width: 80px;    

  padding: 0.2em;    

  text-align: center;    

  background: #fff;    

  *filter: alpha(opacity = 100);*    

}
```

不幸的是，这种方法会在不支持该专有属性的浏览器中生成 CSS 警告，比如 Firefox 1.5，它默认在 JavaScript 控制台中显示 CSS 警告。比在全局样式表中插入特定于 IE 的样式信息更好的解决方案是使用 JavaScript 将声明添加到 IE 中的`pollingMessage` `div`的`style`属性中。这就是在`init`中调用的`setAlpha`方法所实现的。下面是该方法的代码:

```
Example 3.40\. appmonitor2.js (excerpt)    

this.setAlpha = function() {    

  var self = Status;    

  if (document.all && typeof window.opera ==    

      'undefined') {    

    var styleSheets = document.styleSheets;    

    for (var i = 0; i < styleSheets.length; i++) {    

      var rules = styleSheets[i].rules;    

      for (var j = 0; j < rules.length; j++) {    

        if (rules[j].selectorText ==    

            '#pollingMessage') {    

          rules[j].style.filter =    

              'alpha(opacity = 100)';    

          return true;    

        }    

      }    

    }    

  }    

  return false;    

};
```

这段代码只在 Internet Explorer 中执行，它使用`document.styleSheets`数组遍历链接到当前页面的每个样式表。它使用`rules`属性访问每个样式表中的规则，并通过查看`selectorText`属性找到我们想要的样式。一旦它在`rules`数组中有了正确的样式，它就给`filter`属性它需要的值来改变不透明度。

*歌剧中的不透明？*

不幸的是，在撰写本文时，即使是最新版本的 Opera(8.5 版)也不支持 CSS 不透明，因此这样的动画在该浏览器中不起作用。然而，这项功能是为 Opera 版本 9 设计的。

 ***运行动画*** 

处理动画的代码由五个方法组成:前三个方法控制“处理…”动画，而剩下的两个方法控制“完成”动画。控制“处理…”动画的三种方法是:

*   `startProc`，设置“正在处理…”动画，并通过`setInterval`安排对`doProc`的重复调用

*   `doProc`，监控该类的属性，并适当设置“正在处理…”动画的当前帧

*   `stopProc`，表示“正在处理…”动画应该停止

控制“完成”动画的两个参数是:

*   `startDone`设置“完成”动画，并通过`setInterval`安排对`doDone`的重复呼叫

*   `doDone`设置“完成”动画的当前帧，并在动画完成后终止动画

**开机**

设置和启动动画是`startProc`方法的工作:

```
Example 3.41\. appmonitor2.js (excerpt)    

this.startProc = function() {    

  var self = Status;    

  self.proc = 'proc';    

  if (self.setDisplay(false)) {    

    self.currOpacity = 100;    

    self.displayOpacity();    

    self.procInterval = setInterval(self.doProc, 90);    

  }    

};
```

将`proc`属性设置为`proc`(处理)后，这段代码调用`setDisplay`方法，该方法设置`pollingMessage` `div`的颜色和内容。接下来我们来仔细看看`setDisplay`。

一旦代码设置了`pollingMessage` `div`的颜色和内容，它就将`div`的不透明度初始化为 100(完全不透明)，并调用`displayOpacity`使该设置生效。

最后，这个方法调用`setInterval`来安排动画过程的下一步。注意，和`setTimeout`一样，`setInterval`调用返回一个区间 ID。我们将它存储在`procInterval`属性中，这样我们可以在以后停止这个过程。

“处理中…”和“完成”动画共享`setDisplay`方法:

```
Example 3.42\. appmonitor2.js (excerpt)    

this.setDisplay = function(done) {    

  var self = Status;    

  var msg = '';    

  if (done) {    

    msg = 'Done';    

    self.div.className = 'done';    

  }    

  else {    

    msg = 'Processing...';    

    self.div.className = 'processing';    

  }    

  if (self.div.firstChild) {    

    self.div.removeChild(self.div.firstChild);    

  }    

  self.div.appendChild(document.createTextNode(msg));    

  return true;    

};
```

由于`pollingMessage` `div`的“处理中…”和“完成”状态之间的唯一区别是它的颜色和文本，使用这个通用函数在`pollingMessage` `div`的两种状态之间切换是有意义的。颜色是通过将类分配给`pollingMessage`T5 来控制的，所以我们需要将`done`和`processing`类的 CSS 类规则添加到样式表中:

```
Example 3.43\. appmonitor2.css (excerpt)    

.processing {    

  color: #339;    

  border: 1px solid #339;    

}    

.done {    

  color:#393;    

  border:1px solid #393;    

}
```

**使其停止**

平滑地停止动画需要一些特定的时间。我们不希望动画在脉冲中间突然停止。我们希望在自然中断时停止它，此时“处理中…”图像的不透明度降至零。

因此，停止动画的`stopProc`方法实际上并没有停止它本身——它只是设置了一个标志来告诉动画过程，当它到达一个方便的点时，是时候停止了。这很像许多程序员在一天结束时接到妻子和丈夫的电话，提醒他们在代码中到达逻辑停止点时回家。

因为这里很少发生动作，所以这个方法非常简短:

```
Example 3.44\. appmonitor2.js (excerpt)    

this.stopProc = function(done) {    

  var self = Status;    

  if (done) {    

    self.proc = 'done';    

  }    

  else {    

    self.proc = 'abort';    

  }    

};
```

这个方法必须区分两种类型的停止:成功完成的请求(`done`)和来自用户的停止应用程序的请求(`abort`)。

`doProc`方法使用这个标志来决定是否显示“完成”消息，或者只是停止。

**用`doProc`** 运行动画

以 90 毫秒的间隔调用的`doProc`方法改变`pollingMessage` `div`的不透明度，以产生处理动画的脉冲效果。代码如下:

```
Example 3.45\. appmonitor2.js (excerpt)    

this.doProc = function() {    

  var self = Status;    

  if (self.currOpacity == 0) {    

    if (self.proc == 'proc') {    

      self.currOpacity = 100;    

    }    

    else {    

      clearInterval(self.procInterval);    

      if (self.proc == 'done') {    

        self.startDone();    

      }    

      return false;    

    }    

  }    

  self.currOpacity = self.currOpacity - 10;    

  self.displayOpacity();    

};
```

这个方法非常简单——它的主要目的只是在每次调用时将`pollingMessage` `div`的不透明度降低 10%。

第一个 if 语句查看`div`是否已经完全淡出。如果有，并且动画应该还在运行，它会将不透明度重置为 100(完全不透明)。每 90 毫秒执行一次这段代码会产生一种平滑的效果，其中`pollingMessage` `div`淡出、重新出现、再淡出——这种熟悉的脉冲效果表明应用程序正忙于做一些事情。

如果动画不应该继续运行，我们通过调用`clearInterval`来停止动画，然后，如果 proc 属性完成，我们通过调用`startDone`来触发“完成”动画。

**用`startDone`** 启动“完成”动画

`startDone`方法用于“完成”动画的目的与`startProc`方法用于“处理中…”动画的目的相同。它看起来也和`startProc`非常相似:

```
Example 3.46\. appmonitor2.js (excerpt)    

this.startDone = function() {    

  var self = Status;    

  if (self.setDisplay(true)) {    

    self.currOpacity = 100;    

    self.displayOpacity();    

    self.procInterval = setInterval(self.doDone, 90);    

  }    

};
```

这一次，我们将`true`传递给`setDisplay`，这会将文本更改为“Done ”,颜色更改为绿色。

然后我们用`setInterval`设置对`doDone`的调用，这实际上执行了淡出。

**最后的褪色**

`doDone`的代码比`doProc`的代码简单得多。它不需要像`doProc`那样不断处理直到被告知停止。它只是不断减少`pollingMessage` `div`的不透明度 10%，直到它达到零，然后停止自己。相当简单的东西:

```
Example 3.47\. appmonitor2.js (excerpt)    

this.doDone = function() {    

  var self = Status;    

  if (self.currOpacity == 0) {    

    clearInterval(self.procInterval);    

  }    

  self.currOpacity = self.currOpacity - 10;    

  self.displayOpacity();    

};
```

![1542_fig3.9](img/e1fffbc2ba320a403198f0a0e7a560cd.png) 
 *图 3.9。带有脉冲状态指示器的应用*

最后，我们准备在浏览器中测试这段代码。在您的浏览器中打开`appmonitor2.html`,点击开始按钮，您应该会在浏览器视窗的右上角附近看到一个脉冲处理…消息，如图 3.9 所示。

小心轮询间隔！

现在我们已经有一个动画在页面中运行了，我们需要小心不要在前一个动画停止之前再次启动它。因此，强烈建议您不要将`POLL_INTERVAL`设置为小于两秒的任何值。

##### 设计显示器的样式

现在我们已经启动并运行了我们的应用程序，让我们使用 CSS 来使它看起来更好。我们需要添加以下标记来实现我们想要的布局:

```
Example 3.48\. appmonitor2.html (excerpt)    

<body>    

  <div id="wrapper">    

    <div id="main">    

      <div id="status">    

        <div id="statusMessage">App Status:     

          <span id="currentAppState"></span>    

        </div>    

        <div id="pollingMessage"></div>    

        <br class="clearBoth" />    

      </div>    

      <div id="pollResults"></div>    

      <div id="buttonArea"></div>    

    </div>    

  </div>    

</body>
```

如你所见，我们添加了三个`div`来悬挂我们的样式，以及一个换行符来清除浮动的应用程序状态消息和动画。该页面的完整 CSS 如下:样式化的界面如图 3.10 所示:

```
Example 3.49\. appmonitor2.css    

body, p, div, td, ul {    

  font-family: verdana, arial, helvetica, sans-serif;    

  font-size:12px;    

}    

#wrapper {    

  padding-top: 24px;    

}    

#main {    

  width: 360px;    

  height: 280px;    

  padding: 24px;    

  text-align: left;    

  background: #eee;    

  border: 1px solid #ddd;    

  margin:auto;    

}    

#status {    

  width: 358px;    

  height: 24px;    

  padding: 2px;    

  background: #fff;    

  margin-bottom: 20px;    

  border: 1px solid #ddd;    

}    

#statusMessage {    

  font-size: 11px;    

  float: left;    

  height: 16px;    

  padding: 4px;    

  text-align: left;    

  color: #999;    

}    

#pollingMessage {    

  font-size: 11px;    

  float: right;    

  width: 80px;    

  height: 14px;    

  padding: 4px;    

  text-align: center;    

  background: #fff;    

}    

#pollResults {    

  width: 360px;    

  height: 210px;    

}    

#buttonArea {    

  text-align: center;    

}    

.pollResult {     

  padding-bottom: 4px;    

}    

.time {    

  font-size: 11px;    

  width: 74px;    

  float: left;    

}    

.processing {    

  color: #339;    

  border: 1px solid #333399;    

}    

.done {    

  color: #393;    

  border: 1px solid #393;    

}    

.bar {    

  background: #ddf;    

  float: left;    

}    

.inputButton {    

  width: 8em;    

  height: 2em;    

}    

.clearBoth {    

  clear: both;    

}
```

![1542_fig3.10](img/8624629cc2b42aa5c0fe99f02fb95e64.png) 
 *图 3.10。已完成的应用程序监视器*

##### 摘要

我们的第一个工作应用程序展示了如何使用 AJAX 向服务器发出多个请求，而无需用户离开当前加载的页面。它还给出了我们在异步执行多个任务时必须处理的那种复杂性的一幅相当真实的图画。这种复杂性的一个很好的例子是我们使用`setTimeout`来为 XMLHttpRequest 请求计时。这个例子提供了一个很好的机会来研究开发 AJAX 应用程序时会遇到的一些常见问题，比如范围丢失和连接超时，并提供了实用的解决方案来帮助您处理这些问题。

以上是摘自 [*构建你自己的 AJAX Web 应用*](https://www.sitepoint.com/premium/library) 的摘录——别忘了，你可以在[下载这篇文章。pdf 格式](https://www.sitepoint.com/show-modal-popup-after-time-delay/)。这本书总共有八章，到它结束时，读者将已经建立了许多功能齐全的网络应用程序，包括一个多人可以实时玩的在线象棋游戏——这本书的[目录](https://www.sitepoint.com/premium/library)有完整的细节。

## 分享这篇文章

```***