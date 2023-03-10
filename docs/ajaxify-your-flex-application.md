# Ajax 化您的 Flex 应用

> 原文：<https://www.sitepoint.com/ajaxify-your-flex-application/>

嵌入在网页容器中的 Flash 应用程序通常是孤立运行的，网页和哑容器没什么两样。本文将演示如何利用 ActionScript 外部接口来实现两者之间的双向通信。请密切关注，以便您能够在最后完成测验，并有机会赢得免费的 Adobe CS4 Web Premium 和 Flex Builder 3 Pro。

术语富互联网应用程序仍然是当前 web 开发的热门词汇之一。各种各样的供应商都参与进来，提供他们自己风格的 RIA 开发；Adobe 的 Flash 平台是其中最有趣的平台之一，这并不令我们惊讶。Flash 平台的核心元素显然是它的虚拟机，也称为 Flash Player。利用该运行时的最常见的开发技术是 Adobe 的 Flash 创作环境、Flex Builder 和开源的 Flex SDK，后两者构成了 Flex 框架。

Flash 和 Flex 是非常酷的技术——特别是当你跳出浏览器的界限，考虑在用户桌面上的 AIR 中运行基于 Flash 的 RIA 时。然而，Flash 平台只是当今人们在精彩的网络世界中使用的一种技术，因此，在本教程中，我们将了解在 Flash Player 中运行的基于 Flex 的应用程序与外界通信的一些不同方式。

##### 技术要求

我们在这里讨论的许多内容对 Flash 和 Flex 都有效，因为我们实际上是在讨论 Flash Player 的特性和 API。本教程中的所有示例都使用 Flex 3，因此您可能希望开始运行开源的 Flex 3 SDK 或 Flex Builder 3。

从现在开始，我将使用术语 *Flex application* 作为。使用 Flash 或 Flex 创建的基于 swf 文件的应用程序。对于您来说，将这里提供的信息转换成使用 Flash 创作环境应该是相当简单的。如果您希望进一步探索这一点，使用 Flash CS3 或 CS4 会有所帮助。从 Flash Player 的角度来看，示例代码应该在 Flash Player 版本 9 和 10 中运行良好。

一般来说。基于 swf 的应用程序与 Flash Player 一起在一个称为“容器应用程序”的环境中执行让我们先仔细看看 Flex 应用程序最常见的环境:网页容器(有时称为“HTML 包装器”)。除了使用通用的网页容器环境，Flash Player 还提供了一些其他的部署选项；在本教程的最后，我将提供一些参考资料。

##### HTML 包装器

大多数读者可能都熟悉网页的结构以及在这种环境中嵌入 Flex 应用程序的一般概念。然而，当您开始尝试确保您的 Flex 应用程序在每个浏览器中都可以工作，并在必要时触发用户 Flash Player 的自动更新时，这可能会变得有点棘手。使用 Flash CS 3 和 4 以及 Flex 2 和 3 创建的应用程序需要在版本至少为 9 的 Flash Player 中执行。(这是为了让您的应用程序可以使用新的虚拟机，并正确支持 ActionScript 3 以及其他一些很酷的东西)。

最简单的方法是使用一个已建立的模板，该模板已经有代码来将 Flex 应用程序正确地嵌入到您的网页中。Flex Builder 3 项目附带了一个 HTML 模板，提供了一个很好的起点。下图显示了这样一个项目的文件夹结构。文件夹`html-template`包含模板文件，这些模板文件是在编译 Flex 应用程序时使用 HTML 环境的项目设置自定义和填充的；生成的编译文件位于文件夹`bin-debug`中。理解在`bin-debug`中对文件所做的任何改变在你重新编译你的项目时都会被覆盖是很重要的；因此，只能对`html-template`文件夹中的文件进行更改。

![A Flex Builder project folder structure](img/f2dbb6e15e6b280eefc652b6831bc030.png)

记住这一点，我想解释一下 HTML 包装器最重要的部分。基本上，HTML 包装页面使用 JavaScript 库(`AC_OETags.js`，在页面顶部导入)来找出浏览器客户端上实际可用的 Flash 播放器版本。然后，它将根据结果执行 Flex 应用程序或启动 Flash Player 的智能升级。但是，如果 Flash Player 尚未安装或可用的 Flash Player 版本低于 6.0.65，JavaScript 库将显示替代的 HTML 内容。在 HTML 页面的更深处，您还会发现一个带有`<object>`和`<embed>`标签的`<noscript>`部分；如果客户端上的 JavaScript 不可用或被禁用，则会执行此操作。

让我们仔细看看对`AC_FL_RunContent`函数的调用和出现在更下面的`<object>`标记:

```
AC_FL_RunContent( 

  "src", "FlexAndJS", 

  "width", "100%", 

  "height", "100%", 

  "align", "middle", 

  "id", "FlexAndJS", 

  "quality", "high", 

  "bgcolor", "#869ca7", 

  "name", "FlexAndJS", 

  "allowScriptAccess","sameDomain", 

  "type", "application/x-shockwave-flash", 

  "pluginspage", "http://www.adobe.com/go/getflashplayer" 

); 

... 

<object classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" 

    id="FlexAndJS" width="100%" height="100%" 

    codebase="http://fpdownload.macromedia.com/get/flashplayer/current/swflash.cab"> 

  <param name="movie" value="FlexAndJS.swf" /> 

  <param name="quality" value="high" /> 

  <param name="bgcolor" value="#869ca7" /> 

  <param name="allowScriptAccess" value="sameDomain" /> 

  <embed src="FlexAndJS.swf" quality="high" bgcolor="#869ca7" 

      width="100%" height="100%" name="FlexAndJS" align="middle" 

      play="true" loop="false" quality="high" allowScriptAccess="sameDomain" 

      type="application/x-shockwave-flash" 

      pluginspage="http://www.adobe.com/go/getflashplayer"> 

  </embed> 

</object>
```

##### 使用`FlashVars`将数据传递到 Flex 应用程序中

让我们回到本教程最初的话题。我们想让我们的 Flex 应用程序与 HTML 包装器通信。最简单的方法是使用一种叫做`FlashVars`的方法。`FlashVars`是一个非常类似于将数据作为 HTTP GET 参数从页面发送到页面的概念；实际上，我们正在将数据的键/值对传递给 Flex 应用程序。

为此，我们需要向 HTML 中的两个位置添加一个`FlashVars`属性。属性的值是一个 URL 编码的参数对列表，比如:`name=kai&site=ventego-creative.co.nz&...`等等。将它嵌入到上面的代码示例中将会获得以下结果:

```
AC_FL_RunContent( 

  "src", "FlexAndJS", 

  ... 

  **"FlashVars", "name=kai&site=ventego-creative.co.nz"**); 

... 

<object classid="clsid:D27CDB6E-AE6D-11cf-96B8-444553540000" 

    id="FlexAndJS" width="100%" height="100%" 

    codebase="http://fpdownload.macromedia.com/get/flashplayer/current/swflash.cab"> 

  <param name="movie" value="FlexAndJS.swf" /> 

  ... 

  **<param name="FlashVars" value="name=kai&site=ventego-creative.co.nz" />** 

  <embed src="FlexAndJS.swf" quality="high" bgcolor="#869ca7" 

      ... 

      **FlashVars="name=kai&site=ventego-creative.co.nz"**> 

  </embed> 

</object>
```

```
FlashVars data can be easily used in the Flex application by referring to the parameters object in Application.application. For example, to retrieve the content of the variable site that has been passed in via FlashVars, you'd use a snippet similar to the example below in the ActionScript part of your Flex application:

```

```
public var theSite:String = Application.application.parameters.site;
```

显然，包装器和 Flex 应用程序之间的这种通信方式非常不灵活(您只能在键/值对中使用序列化的平面数据)，并且无法提供双向通信。尽管如此，当应用程序需要在开始时传入(非关键)数据时，`FlashVars`还是经常被使用。

##### `ExternalInterface`

现在我们需要在 Flex 应用程序及其包装器之间引入一个真正的双向通信通道:Flash Player 的外部 API，也称为`ExternalInterface`。`ExternalInterface`类是 ActionScript 3 中外部 API 的表示。在 Flash 的早期版本(直到版本 8)中，`fscommand`函数用于提供对外部 API 的访问。

它可以应用于以下用例:

*   检索关于 Flex 应用程序容器的信息
*   从 Flex 中的 ActionScript 调用并执行容器中的代码
*   从容器调用并执行 Flex 应用程序中的 ActionScript 代码

外部 API 是在 ActionScript 3 中使用的 Flash Player 的子系统。当我在上面的列表中提到“调用和执行容器中的代码”时，我实际上是指在我们的 HTML 包装页面中执行 JavaScript 代码。因此，我们将介绍一种方法来设置和运行从 HTML 和 JavaScript 到 Flex 和 ActionScript 的双向函数调用。

在我向您展示一些代码之前，我们先来谈谈一些值得了解的东西:

2.  首先是一些坏消息:您有时可能很难访问 ExternalInterface 类。好消息是，在大多数现代浏览器上，您很少会遇到有关 ExternalInterface 可用性的问题。如果客户端浏览器属于以下情况之一，则不会有问题:

*   Windows 上的 Internet Explorer 5+
*   支持 NPRuntime 接口的浏览器(例如 Firefox 1+，Safari 1.3+，Netscape 8+，Opera 9+)

检查外部接口可用性的推荐方法是测试`ExternalInterface.available`的值；如果可用，它将返回`true`。这个结果实际上忽略了告诉你浏览器的 JavaScript 设置的状态(即用户的 JavaScript 是否启用)；它只会告诉您，从概念上讲，您的应用程序可以利用外部 API。

请记住，您应该在 ActionScript 中将以下通用结构应用于外部 API 调用:

```
if(ExternalInterface.available)  

{  

  // Execute ExternalInterface calls in here.  

}

```

*   如果 HTML 标签(生成的`<object>`和`<embed>`标签)嵌套在 HTML 表单中，那么在 HTML 包装器中从 Flex 和 ActionScript 到 JavaScript 的`ExternalInterface`调用将无法工作。
*   在 Internet Explorer 中，如果`<object>`标签的 id 属性包含可被解释为 JavaScript 运算符的字符(例如，`-`)，那么对包装器的 Flex/ActionScript 调用将无法正常工作。

##### 从 Flex 调用 JavaScript 代码

我将演示我之前提到的第一个用例:检索容器信息。让我们来看一个使用`ExternalInterface`类来显示其周围容器的`navigator.userAgent`属性的 Flex 应用程序:

```
<?xml version="1.0" encoding="utf-8"?>  

<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" layout="vertical" verticalAlign="middle" creationComplete="init();">  

  <mx:Script>  

    <![CDATA[  

      import mx.controls.Alert;  

      private function init():void   

      {  

        if (ExternalInterface.available)   

        {  

          var userAgent:String = ExternalInterface.call(  

              "navigator.userAgent.toString");  

Â Â Â Â Â Â Â Â Â Â Alert.show(userAgent, "UserAgent information:");  

Â Â Â Â Â Â Â Â }  

Â Â Â Â Â Â }  

Â Â Â Â ]]>  

Â Â </mx:Script>  

  <mx:Button label="display user agent (again)" click="init();" />  

</mx:Application>
```

简单的逻辑封装在`init`函数中，我们可以看到`ExternalInterface.call`实际上执行了对 HTML 包装器的调用。在这种情况下，我们只是为`navigator.userAgent`属性调用了`toString`函数。

让我们进行下一步:如果我们在页面上有一些 JavaScript 代码，我们想从我们的 Flex 应用程序中执行呢？实际上，这相当简单，由`ExternalInterface`类的`call`方法再次为我们完成这项工作:

```
...  

<mx:Script>  

  <![CDATA[  

    private function callJavaScript():void   

    {  

      ExternalInterface.call("sayHelloWorld");  

    }  

  ]]>  

</mx:Script>  

...
```

在这个例子中，我们向`call`提供了我们希望执行的 JavaScript 函数的名称。相应的 JavaScript 函数显然必须包含在包装器中；在下面的例子中，我们基本上是在 Flash 播放器中触发一个 JavaScript 警告弹出窗口的显示。下面是 JavaScript 代码:

```
<script type="text/javascript">  

  function sayHelloWorld()   

  {  

    alert("Hello World from JavaScript");  

  }  

</script>
```

通过`ExternalInterface`类从 ActionScript 向 JavaScript 函数传递参数遵循非常相似的语法。实参作为附加参数传递给 call 方法:

```
...  

<mx:Script>  

  <![CDATA[  

    private function callJavaScript():void   

    {      var a:int = 4;  

      var b:int = 4711;  

      var calcResult:int =   

          ExternalInterface.call("doCalculation",a,b);  

    }  

  ]]>  

</mx:Script>  

...
```

JavaScript 函数可能如下所示:

```
<script type="text/javascript">  

  function doCalculation(number1, number2)   

  {  

    return number1 * number2;  

  }  

</script>
```

请注意，如果 JavaScript 调用失败或者您的 JavaScript 函数没有合适的返回值，那么`ExternalInterface`调用的结果将是`null`。

如果您在 Flash Player 中处理安全沙箱，您可能会遇到`SecurityError`异常。在开发过程中，以及从开发到测试和生产的过程中，您可以做两件事来避免麻烦:

1.  在 HTML 页面的`<object>`和`<embed>`标签中设置一个合适的`allowScriptAccess`属性值。

3.  在真实环境中开发——在通过`http://`协议从(本地)HTTP 服务器(如 Apache 或 IIS)传送到浏览器的页面中构建和测试您的应用程序，而不是在您的浏览器中使用`file://`协议前缀。

使用外部 API 的好处是，它允许我们使用大多数普通数据类型，以及一些复杂类型，如数组。

##### 从 HTML 和 JavaScript 调用 ActionScript 代码

正如我之前指出的，`ExternalInterface`提供了一个双向的沟通渠道。因此，我们能够从嵌入 HTML 包装器的 JavaScript 代码中调用 Flex 应用程序中的 ActionScript 代码。这个过程比反过来要复杂一些，因为我们实际上必须设置一些回调处理程序。

让我们再次尝试显示警告弹出窗口。这一次，我们希望使用一个 HTML 按钮作为触发器，在 Flex 应用程序中显示一个警告窗口，该窗口本身显示当前日期和时间。第一步是回调，因为我们需要告诉 Flex 应用程序，如果从包装器发送特定信号，要运行哪个 ActionScript 方法。回调在 Flex 应用程序的`init`方法中创建:

```
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" ... creationComplete="init();" ...>  

  <mx:Script>  

    <![CDATA[  

      import mx.controls.Alert;  

      private var alert:Alert;  

      private function init():void   

      {  

        ExternalInterface.addCallback("showAlert", showAlert);  

      }  

      private function showAlert(msg:String):void   

      {  

        var now:Date = new Date();  

        alert = Alert.show(msg,now.toLocaleDateString());  

        alert.status = now.toLocaleTimeString();  

      }  

  ]]>  

</mx:Script>  

</mx:Application>
```

我们上面使用的`addCallBack`方法公开了一个可以被 JavaScript 调用的函数名，`"showAlert"`；这将调用 ActionScript 方法`showAlert`。

在 HTML 页面上，我们定义了单击按钮`asAlert`将执行的函数，以及获取 Flash 电影对象引用所需的另一个帮助函数`thisMovie`:

```
<script type="text/javascript">  

  function thisMovie(movieName)   

  {  

    if (navigator.appName.indexOf("Microsoft") != -1)   

    {  

      return window[movieName];  

    }   

    else   

    {  

      return document[movieName];  

    }  

  }  

  function asAlert(value)   

  {  

    thisMovie("alertFromFlex").showAlert(value);  

  }  

</script>
```

如您所见，函数`asAlert`利用助手函数`thisMovie`返回对包含 Flash 电影的 HTML DOM 元素的引用，并调用其 showAlert 方法，传入一个字符串值。

为了让上面的代码正常工作，你需要确保这一点:传递给`thisMovie`函数(这里是`"alertFromFlex"`)的参数需要与`<object>`标签的`id`属性，以及用于在页面中嵌入 Flex 应用程序的`<embed>`标签的`name`属性相同。

请注意使用 JavaScript 与 Flex 应用程序通信时可能遇到的一个潜在问题:很难预测在页面呈现期间 Flex 应用程序可用的确切时间点。因此，使用可从 ActionScript 查询的浏览器窗口的`onload`事件将名为`jsReady`的标志设置为`true`可能是个好主意。如果标志的值是`true`，我们可以安全地假设页面加载已经完成，我们可以在 ActionScript 中设置回调处理程序。

##### 接下来去哪里？

虽然本文应该可以帮助您起步，但是 Flash Player 的外部 API 还有很多其他的可能性。例子是无限的，可以包括复杂的表单元素和 Flex 内置的控件，它们必须嵌入到现有的 HTML 表单中；Flex 中的图像上传控件和管理系统必须与周围的 HTML 页面交互；或者将您的 Flex 应用程序与现有的第三方 JavaScript APIs 挂钩。

在文章中，我提到了使用非 HTML 页面包装器的可能性。通常这是 Flash Player ActiveX 控件，嵌入在用其他技术开发的独立应用程序中。Adobe 提供了一些关于如何使用 C#和. NET 来实现这一目标的基本信息。强烈推荐的关于将 ActiveX 控件嵌入到您自己的应用程序中的教程可以在 [richapps.de](http://www.richapps.de/) 上找到。

另外，如果你真的想把 Flex 和 JavaScript 联系起来，一定要深入了解一个叫做 Flex/AJAX Bridge 的库。这个名字有点误导，因为它实际上是一个使两种技术之间的通信更容易的库，几乎没有关注 Ajax。它基本上抽象了底层的`ExternalInterface`调用，让您可以轻松地在 Flex 和包装器之间传递复杂的对象——比如对 UI 组件的引用。只有一小部分人知道这个工具包的存在，但它是随 Flex SDK 和 Flex Builder 一起提供的。你会在文件夹`frameworks/javascript/fabridge`中找到它。

##### 测试你的知识

还记得这些吗？通过[做测验](https://www.sitepoint.com/quiz/adobe/ajaxify-your-flex-application/)来测试一下这篇文章的内容。提交您的答案有机会赢得免费的 Adobe CS4 Web Premium 和 Flex Builder 3 Pro。[立即参加测验！](https://www.sitepoint.com/quiz/adobe/ajaxify-your-flex-application/)

## 分享这篇文章