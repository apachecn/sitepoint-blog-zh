# Coldfusion MX 和 Flash Remoting 入门

> 原文：<https://www.sitepoint.com/coldfusion-mx-flash-remoting/>

Macromedia ColdFusion MX 的一个很酷的新功能是 Macromedia Flash Remoting。Macromedia Flash Remoting 允许您使用熟悉的 Macromedia Flash 语法和对象，轻松地在 Macromedia Flash 电影中使用 ColdFusion MX 功能。

今天，我们将浏览一个简单的 Macromedia Flash Remoting 示例。SWF 将使用 Macromedia Flash Remoting 调用服务器上的 ColdFusion MX 组件(也称为 CFC)。ColdFusion 组件将返回一个字符串，该字符串将被加载到 Macromedia Flash 中并显示。

##### 入门指南

***1。下载教程文件。***

你需要的所有文件都可以在这里下载[(35kb)](http://webmasterbase.com/examples/startremoting/startremoting.zip)。

在 cf_webrootcommacromediatest 创建一个文件夹。将 ZIP 文件解压到测试文件夹中。请注意，在整个教程中，我们将引用 cf_webroot。

***2。安装所需软件***

我们将在本教程中使用的软件包括:

*   Macromedia Flash MX
*   Macromedia ColdFusion MX
*   Macromedia Flash MX 的 Flash Remoting 组件

您可以下载这里列出的软件的试用版。按照安装向导进行操作，以确保所有软件都已正确安装。

##### 创建服务器端代码

服务器端代码是一个简单的 ColdFusion 组件。它包含在 com/macromedia/test 文件夹中的 HelloWorld.cfc 文件中，请注意，文件名 HelloWorld.cfc 还决定了组件的服务名。所以在这种情况下，服务名是 HelloWorld。如果在文本编辑器中打开 HelloWorld.cfc，您将看到以下内容:

```
<cfcomponent name="HelloWorld"> 

  <cffunction name="sayHello" access="remote"> 

    <cfreturn "Hello World"> 

  </cffunction> 

</cfcomponent>
```

如您所见，创建一个基本组件非常简单——我们只需使用`cfcomponent`标记。尽管该组件代表一个 Macromedia Flash Remoting 服务，但它本身并不做任何事情。

为了使组件起作用，我们给它添加了`cffunction`标签——这是一个方法。因此，组件现在包含一个名为“sayHello”的`cffunction`标签，其中嵌套了`cfreturn`标签。当你调用这个组件时，它返回一个“HelloWorld”字符串到任何一个 URL 或服务等。调用了方法。在一个组件中，您可以拥有任意多的函数，但是在这个例子中，我们只包含一个函数。

还要注意，cffunction 标记中的访问属性值是“`remote`”。如果希望 Flash 电影(或其他远程服务)访问组件方法，则该属性是必需的。

就是这样！我们已经成功创建了一个组件。请注意，相应的文件位于此处
coldfusionmx _ webrootcommacromediateshelloworld . CFC

为什么我指示你把文件放在那个文件夹里？组件不仅仅通过名称来引用，它们还通过位置来引用(在这种情况下，是 coldfusionmx _ webrootcommacromediatest)。因此，通过将组件放在 commacromediatest 文件夹中，您可以:

1.  确保团队中的其他开发人员不会用自己的“HelloWorld”组件覆盖您的“HelloWorld”组件。
2.  采用组件的最佳实践组织。

最后，你现在可能已经注意到，com/macromedia 只不过是反过来的 macromedia.com。同样，你应该为你创建的组件使用你自己的域名，比如 net/foo，如果你的站点叫做 foo.net 的话。

那么，下一步是什么？此时，您可以开始构建 Macromedia Flash 代码来访问您的组件，但首先，让我们制作一个简单的 ColdFusion 页面来确保组件实际工作。如果我们现在测试它，调试任何可能出现的问题将比我们开始使用 Macromedia Flash MX 时更容易。

在 cfusionmx _ webroot/com/macromedia/test/目录中，有一个名为 test.cfm 的文件，其中包含以下代码:

```
<cfoutput> 

  <cfinvoke  

          component="com.macromedia.test.HelloWorld"  

          method="sayHello"  

      returnVariable="message"  

          /> 

      #message# 

</cfoutput>

The file test.cfm uses the cfinvoke tag to call the component and then print the component's output with the cfoutput tag. Let's now take a quick look at the cfinvoke tag's attributes to understand what's going on. 上面写着:

```
component="com.macromedia.test.HelloWorld"
```

指定将调用哪个组件。路径 com.macromedia.test.HelloWorld 指向相对于 cf_webroot 文件夹的 commacromediatestHelloWorld 文件。
以下代码:

```
method="sayHello"
```

指定要在组件中执行的函数或方法。
下一部分:

```
returnVariable="message"
```

指定我们将用来存储从组件返回的任何数据的变量名。我们将使用这个变量从组件方法返回字符串，并使用`cfoutput`标签在 Web 浏览器中显示该变量。
保存 ColdFusion 页面 test.cfm，并在 Web 浏览器中查看它，网址为 http://localhost/com/macromedia/test/test . cfm(根据具体设置的需要，更改域名或添加端口号)。应显示消息“Hello World”。如果没有，或者您收到一个错误，检查您的组件代码，CFML 测试页，以及错别字的 URL。
您刚刚体验了组件的力量！它们允许您创建模块化代码，这些代码可以以多种方式使用，甚至可以被多个程序使用(在这种情况下，就是 ColdFusion MX 和 Macromedia Flash MX)。现在您已经准备好使用 Macromedia Flash MX 连接到您的组件了！

##### 在 Macromedia Flash MX 中创建客户端代码

创建一个新的 Macromedia Flash 电影，然后用 Macromedia Flash MX 打开它。在解压缩本教程的 ZIP 文件的文件夹中，找到名为 gatewayHelloWorld.fla 的 Macromedia Flash 电影。

我们将创建的 Macromedia Flash 电影很简单:它将连接到服务器，调用 HelloWorld.cfc 上的方法(它将从服务器加载数据)，并在输出窗口中显示它。不过，首先让我们看看 Macromedia Flash 电影的动作脚本。下面是代码的分步演练。影片有一帧，如下所示。

ActionScript 代码如下:
 `#include "NetServices.as"  

#include "NetDebug.as"  

function Result()  

{  

  //receives data returned from the method  

  this.onResult = function(result)  

  {  

    trace("Data received from server : " + result);  

  }  

  this.onStatus = function(error)  

  {  

    trace("Error : " + error.description);  

  }  

}  

NetServices.setDefaultGatewayUrl  

("http://localhost:8500/flashservices/gateway");  

var gw = NetServices.createGatewayConnection();  

var server = gw.getService("com.macromedia.test.HelloWorld",   

new Result());  

server.sayHello();`

首先，ActionScript 库出现在应用程序代码中:

```
#include "NetServices.as"   

#Include "NetDebug.as"

The ActionScript libraries are required, as they contain the ActionScript code and objects necessary to communicate with Macromedia Flash Remoting. NetDebug.as is used to debug the communication between Macromedia Flash and the server. When NetDebug.as is included, you can view all the client/server communication by opening the NetConnection panel within Macromedia Flash MX (Window > NetConnection Debugger).接下来，影片创建了一个名为 Result 的新 ActionScript 类。它使用类的实例中的方法来捕获和操作从服务器接收的数据。

```
function Result()  

{  

  this.onResult = function(result)  

  {  

    trace("onResult : " + result);  

  }  

  this.onStatus = function(error)  

  {  

    trace("onStatus called : " + error.description);  

  }  

}
```

这用两个方法创建了一个结果类。第一种方法:

```
this.onResult = function(result)  

{  

  trace("Data received from server : " + result);  

}
```

从服务器上的组件检索响应。每当从服务器加载数据时，它调用`onResult`方法。在这种情况下，应用程序代码使用`trace()`方法将服务器的响应打印到 Macromedia Flash MX 中的输出窗口。
接下来，应用程序代码创建一个名为`onStatus`的函数。如果试图从服务器加载数据时出错，将调用此函数。当错误发生时，它被传递给方法。您可以通过 description 属性访问错误描述，如下所示。在这种情况下，它使用`trace()`方法将错误消息从服务器打印到 Macromedia Flash 的输出窗口。下面的代码演示了这个场景:

```
this.onStatus = function(error)  

{  

  trace("Error : " + error.description);  

}
```

请注意，包含这些方法的对象不需要命名为 Result。

一旦创建了结果类，就可以连接到服务器了。首先，使用以下代码设置 ColdFusion MX 服务器的路径。如果您的 ColdFusion MX 服务器运行在不同的端口或域名上，只需相应地更改此 ActionScript 中的 URL。

```
NetServices.setDefaultGatewayUrl(http://localhost:8500/flashservices   

/gateway);
```

接下来，使用`NetConnection`对象连接到 ColdFusion MX 服务器:

```
var gw = NetServices.createGatewayConnection(); 
```

请注意，Macromedia Flash 电影此时不连接到服务器，而是将此代码返回的对象视为到服务器的连接。接下来，获取对驻留在您要访问的服务器上的组件的引用:

```
var server = gw.getService("com.macromedia.test.HelloWorld",    

new Result()); 
```

注意，有两个参数被传递给了`getService`方法。第一个参数 com.macromedia.test.HelloWorld 是我们组件的路径——它定义了您要连接的组件(您可能需要根据组件的位置更改该参数)。

第二个参数`new Result()`，创建结果对象的一个新实例。将`new Result ()`实例传递给该方法表明，每当服务器发送数据或信息时，该方法应该调用该对象中的函数(`onResult`和`onStatus`方法)。

我们通过传递一个新的`Result()`来传递一个实例给`Result`类，这看起来可能有点奇怪。我们本来可以这样做:

```
var r = new Result();    

var server = gw.getService("com.macromedia.test.HelloWorld", r);
```

然而，在这种情况下，一旦数据被加载，我们不需要使用类的实例，我们不需要在变量中保存它的实例。

`getService()`方法返回一个表示服务器上实际组件的对象。在这种情况下，它存储在一个名为`server`的变量中。现在，如果您想要调用组件中的方法，请通过服务器变量调用它，如下所示:

```
server.sayHello().
```

此时，代码调用服务器上的服务和组件。它是这样工作的:

1.  Flash MX 调用 ColdFusion MX 服务器，将组件名称传递给服务器。
2.  Flash Remoting 根据 Flash 指定的路径定位组件，然后调用组件内的函数(也是由 Flash 指定的)。
3.  组件函数返回一个字符串。
4.  网关接收字符串，并将其传递回 Flash 电影。
5.  Flash 从服务器接收字符串(通过 Flash Remoting)，并将其传递给指定对象的`onResult()`方法(在本例中是`Result`类)。
6.  `onResult`方法处理数据。

##### 运行示例

要运行该示例并测试电影，请使用以下步骤。

1.  验证 ColdFusion MX 服务器正在运行。
2.  Within Flash MX, test the movie by going to Control > Test Movie. "Hello World" appears in the Flash MX output window, as follows:

      image
3.  发布您的 Flash 电影。
4.  在浏览器中查看您的 Flash 电影。ColdFusion 组件在 Flash 电影中生成“Hello World”数据。

##### 结论

现在您知道如何使用 Flash MX、ColdFusion MX 和 Flash Remoting 创建简单的 Flash 电影。当然，显示“Hello World”可能看起来不那么令人印象深刻或有用，但是，请记住“Hello World”只是一个“string”对象类型。您可以轻松地更改 ColdFusion 组件，使其返回其他类型的数据，如数组、结构或数据库查询结果。此外，您可以使用以下服务器端代码来填充数组、结构或数据库查询:

*   ColdFusion 组件(如本例所示)
*   ColdFusion 页面
*   服务器端动作脚本
*   Java 语言(一种计算机语言，尤用于创建网站)
*   网络服务

这些 ColdFusion MX 功能有助于您在网站上利用 Macromedia MX 功能。这些主题是 ColdFusion MX、Macromedia Flash MX 和 Dreamweaver MX 的应用程序开发中心的特色。

<font size="1" color="#aaaaaa" face="Verdana,Arial,Helvetica">经 Macromedia 许可转载</font>

## 分享这篇文章

```

```