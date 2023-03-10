# 用 Flex 为你的 PHP 应用拉皮条！

> 原文：<https://www.sitepoint.com/pimp-php-app-flex/>

如果你想给你的 PHP 应用程序添加一些活力，不用再犹豫了——Adobe 的 Flex 可以让你的界面更加完美。在本文中，Lennart Steinke 向我们展示了如何用 PHP 后端制作一个 Flex 小部件。请仔细阅读，因为[最后有一个测验:](https://www.sitepoint.com/quiz/adobe/pimp-php-app-flex)在有限的时间内，每个参赛者只需做一下测验就可以获得一份免费的 Flex 参考资料，另外三名幸运的获胜者将每人获得一份免费的 Adobe 的 Flex Builder 3！

Flex 是 Adobe 的开源 web 应用程序框架,旨在快速开发 web 或桌面应用程序。它允许您构建软件，然后编译成 Flash (SWF)文件，并可以在任何安装了 Flash 插件的浏览器中运行。最棒的是，使用起来很有趣。你可以很容易地创建好看的应用程序:赏心悦目的效果是内置的，只需几行代码就可以添加到你的应用程序中。这允许你在短时间内创建一个很酷的应用程序。哦，我有没有说过用起来很有趣？

如果你是 Flex 的新手，看看我们之前的 Flex 教程。它将引导您完成安装 Flex Builder 开发环境的过程，并向您介绍 Flex 开发的一些关键概念。

在本文中，我们将更进一步，通过在 Flex 中创建一个使用 PHP 检索产品信息的产品信息小部件，学习如何将 Flex 与 PHP 一起使用。在开始之前，[从 Adobe 获取一份 Flex Builder 3 演示版](http://www.adobe.com/go/flex_trial)-我们将使用它来创建我们的小部件。

##### 示例小部件

我们将要构建的小部件显示了一个带有描述的产品图片和一个指向产品信息页面的链接。它将由以下基本组件组成:

*   显示小部件的 Flex 应用程序
*   包含 Flash 的 HTML 页面
*   后来，PHP 脚本将产品数据表示为 XML 或 JSON

首先，我们将在构建小部件时将产品信息嵌入到 Flex 应用程序中。一旦我们解决了 Flex 部分，我们将更改代码来学习如何从 XML 文件中检索数据。

让我们从使用 Flex Builder 创建一个新的 Flex 项目开始。输入 **ProductWidget** 作为项目名称，确保应用程序类型设置为 Web 应用程序，然后点击 Finish 按钮。

这将创建一个基本项目，包括一个用于加载和显示应用程序的 HTML 模板文件，以及一个基本应用程序——product widget . mxml。除非您更改了 Flex Builder 布局，否则主应用程序文件将在屏幕右侧的文本编辑器中可见。确保编辑器中显示了 ProductWidget.mxml 文件。如果您不确定，请在左侧的文件树中查找名为 ProductWidget.mxml 的文件，然后双击它。这将确保加载正确的文件。

![Your file structure should look something like this](img/309fd66be0cba96618204e12c1878e7e.png)

在该文件中，您将看到一个 Flex 应用程序的开端，包含在`mx:Application`标签中。我们将添加一个包含小部件的框、一个图像占位符和一个包含链接的按钮。在`mx:Application`标记之间，添加以下代码:

```
<mx:VBox top="0" left="0" right="0" bottom="0"> 

  <mx:Image id="image" width="150"/> 

  <mx:LinkButton label="LinkButton" id="link" width="150"/> 

</mx:VBox>	`mx:VBox`是一个布局组件，它将确保图像和链接按钮显示在彼此下方。这两个项目也是 150 像素宽。

点击工具栏中的**运行**按钮(绿色按钮，带白色箭头)以确保一切正常。您的浏览器应该会打开并显示一个看起来相当空的页面，我们之前创建的`LinkButton`放在角落里。

![A bit sparse, but not for long](img/1fb41feb50b40a651037ecf4e42dbca4.png)

下一步，我们将告诉 Flex 应用我们的产品图像、按钮文本和按钮链接。

##### 传递参数

向 Flex 应用程序传递参数的最基本方法是将参数添加到包含我们的小部件的 HTML 模板中。对于我们的示例，我们需要提供三个值:

*   要显示的图像的 URL(`imageURL`)

*   一个产品描述(`productTitle`)

*   产品详细信息的链接(`productLink`)

我们将通过更改位于 html-template 文件夹中的 HTML 模板来实现这一点。默认情况下，该文件包含检测 Flash 插件的代码和一个安装站点的链接，以防客户端需要安装 Flash 插件。一旦我们完成，它还将包含向我们的 Flex 应用程序传递参数的代码。

文件中有两个对函数`AC_FL_RunContent`的调用。确保您使用的是用于调用 SWF 文件的那个(另一个是在检测到过期版本时更新 Flash Player)。它在我的模板的第 77 行左右。该调用包含传递给 Flex 应用程序的所有参数。为了将数据传递给 Flex 应用程序，您需要添加一个名为`flashVars`的附加参数；这个参数可以包含其他几个用 HTML URL 编码的参数，比如一个`GET`调用中的变量。我正在使用[nitewind 23](http://www.sxc.hu/photo/638995)的 stock.xchng 中的一张免费图片，该图片的细节屏幕将作为此次演示的产品:

```
} else if (hasRequestedVersion) {  

  // if we've detected an acceptable version  

  // embed the Flash Content SWF when all tests are passed  

  AC_FL_RunContent(  

    "src", "${swf}",  

    "width", "${width}",  

    "height", "${height}",  

    "align", "middle",  

    "id", "${application}",  

    "quality", "high",  

    "bgcolor", "${bgcolor}",  

    "name", "${application}",  

    "allowScriptAccess","sameDomain",  

    "type", "application/x-shockwave-flash",  

    "pluginspage", "http://www.adobe.com/go/getflashplayer",  

    "flashVars", "imageURL=http://www.sxc.hu/pic/m/n/ni/nitewind23/638995_dead_rubber_ducky.jpg&productTitle=Rubber%20duck&productLink=http://www.sxc.hu/photo/638995"  

  );  

  // ...  

}
```

如果用户在浏览器中启用了 JavaScript，这就足够了。对于不使用 JavaScript 浏览的用户，您还需要在模板的`noscript`块中添加参数。

我们的应用程序将从 HTML 模板接收参数——现在我们需要告诉应用程序如何使用它们。

代码输入。mxml 文件必须放在`mx:Script`块中。我们将在`mx:VBox`的右括号后添加块:

```
<mx:Script>  

  <![CDATA[  

    private var linkURL: String ="";  

    private function onCreationComplete() : void  

    {  

      setData(  

        application.parameters.imageURL,  

        application.parameters.productTitle,   

        application.parameters.productLink   

      );  

    }  

    private function setData(imageURL: String, productTitle:String, productLink:String) : void  

    {  

      image.source = imageURL;  

      link.label = productTitle;  

      linkURL = productLink;  

    }  

    private function onLinkClicked():void  

    {  

      navigateToURL(new URLRequest(linkURL));  

    }  

  ]]>  

</mx:Script>
```

`onCreationComplete`方法读取从 HTML 文件传递来的参数，并调用另一个函数`setData`，该函数将使用从 HTML 模板接收到的参数为我们的代码填充变量。因为这个方法在应用程序一创建就被调用，所以我们可以确定我们的数据在一开始就被设置好了。

`setData`函数有三个参数:`imageURL`、`productTitle`和`productLink`。我们将使用`imageURL`来设置图像的来源，而`productTitle`可以用来设置显示在链接按钮上的标签。`productLink`需要保存在我们刚刚定义的`linkURL`变量中，这样一旦用户点击链接，我们就可以使用它。

最后，`onLinkClicked`函数将导航到变量`linkURL`中定义的链接。在下一步中，我们将把该功能附加到按钮上。

让我们回到之前创建的`mx:VBox`标签，并寻找链接按钮。我们将添加一个点击处理程序，如下所示:

```
<mx:LinkButton label="LinkButton" id="link" click="onLinkClicked()"/>
```

最后，我们需要在应用程序加载后立即完成所有这些工作。我们将向应用程序添加一个处理程序`creationComplete`，以便在元素创建完成时运行我们的`onCreationComplete`函数。寻找顶部的`mx:Application`标签，并添加处理程序:

```
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml"   

  layout="absolute"   

  creationComplete="onCreationComplete()" >
```

你完成了。mxml 文件应该类似于清单 1 中的代码。

完成了。点击运行按钮并尝试一下！结果应该看起来有点像下面这样:

![Rubber duckie, you're the one](img/e5df2caaf8754302600bd6db457cb487.png)

##### 给它加点香料

因为它太简单了，所以让我们在应用程序中添加一小块视觉糖果，并在图像中淡入。这就像将`completeEffect="Fade"`添加到`mx:Image`标签中一样简单:

```
<mx:Image id="image" height="150" completeEffect="Fade"/>
```

现在，再次运行应用程序——您应该会看到鸭子的图像会慢慢淡入。Flex 中有许多预定义的效果，定制它们或创建自己的效果非常容易。有关更多信息，请查看 Adobe LiveDocs 中的[处理效果](http://livedocs.adobe.com/flex/3/html/help.html?content=behaviors_05.html#275817)。

现在，我们有了为 Flex 应用程序提供数据的基本框架。接下来，是时候以更令人兴奋的方式提供数据了:让我们使用 PHP 创建一些 XML 供应用程序使用。

##### 使用 XML 数据

如果您的应用程序可以生成 XML 数据，本教程的下一部分将向您展示如何在 Flex 中处理这些数据。XML 易于创建，易于阅读，Flex 对 XML 的支持也很简洁。这些都是使用它的好理由，所以让我们来看看这样做所涉及的工作。
 ***在我们开始之前:**重要的是要记住，Flash 对于从不同的域访问数据到保存 SWF 的域是非常严格的。您要么需要从 PHP 脚本所在的域运行 Flex 应用程序，要么在 PHP 所在的服务器上创建一个跨域策略文件。有关跨域策略文件和 Flash 安全模型的更多信息，请查看这篇 Adobe Developer Connection 文章[。](http://www.adobe.com/devnet/flashplayer/articles/fplayer9_security.html)* 

因为我们将使用 HTTP 来检索数据，所以我们将使用一个名为`HTTPService`的 Flex 类，它将为我们处理所有繁重的工作。我们将告诉`HTTPService`连接到哪里以及如何处理返回的数据。

为了进行试验，我们需要编写一个简单的 PHP 数据提供程序。在现实世界中，您将从数据库中提取信息，但是对于这个示例，我们将保持简单并硬编码这些值。下面是一个 PHP 脚本，它使用一个函数来获取数据，创建一个变量来保存数据，然后以 XML 格式输出数据:

```
<?php   

  header("Content-type: text/xml");   

  function getData() {   

    $data["productLabel"] = "Rubber duck";   

    $data["productLink"] = "http://www.sxc.hu/photo/638995";   

    $data["imageURL"] = "http://www.sxc.hu/pic/m/n/ni/nitewind23/638995_dead_rubber_ducky.jpg";   

    return $data;   

    }   

  $product = getData();   

?>   

<?php echo '<?xml version="1.0"?>'; ?>   

<data>   

  <product>   

     <label><?php echo $product["productLabel"] ?></label>   

     <link><?php echo $product["productLink"] ?></link>   

     <imageURL><?php echo $product["imageURL"] ?></imageURL>   

  </product>   

</data>
```

在我们的 Flex 应用程序中，我们将通过在代码中添加一个`HTTPService`块来定义一个指向 PHP 页面的`HTTPService`。在这个块中，我们需要为我们的服务定义一个唯一的名称(`id`)，这样我们就可以在 ActionScript 代码、要调用的 URL(`url`)和结果格式(`resultFormat`)中引用它。

需要记住的一点是，调用将是异步的:send 方法将立即返回，服务将在后台调用。那么，我们如何访问这些数据呢？为此，`HTTPService`提供了两个回调方法:一个用于结果成功时(`result`)，一个用于结果失败时(`fault`)。

下面是我们将用来定义我们的`HTTPService`块的代码，我们将把它放在`mx:Script`块之前:

```
<mx:HTTPService    

  id="productService"   

  url="http://www.example.com/service.php"    

  resultFormat="e4x"   

  result="serviceLoaded(event)"   

  fault="serviceError(event)"   

/>
```

当一个`HTTPService`将检索 XML 时，您应该将预期的格式`resultFormat`设置为`e4x` - ECMAScript for XML。使用这种结果类型允许我们在处理返回的 XML 时使用许多方便的 JavaScript 函数。

如果你想知道为什么它被称为 ECMAScript 而不是 JavaScript，那么， [Ecma 国际组织](http://www.ecma-international.org/)定义了 JavaScript 语言标准。这个想法是 JavaScript 的不同实现(例如，不同的浏览器)应该至少定义 Ecma 国际组织定义的功能。这种核心脚本语言也被称为 ECMAScript。

接下来，我们需要定义我们将如何处理从`HTTPService`接收的数据。我们知道结果格式，所以我们简单地将事件结果视为 XML。这允许我们像普通对象一样访问 XML 中的数据。顶级元素(我们示例中的`data`)将被映射为实际的 XML 对象(我们示例中的`xmlData`)，因此我们可以通过使用`xmlData.product`来访问产品级别。由于 XML 可能包含几个产品元素，我们需要指定我们想要的产品元素——在本例中，它总是第一个(`xmlData.product[0]`)。产品下面的元素可以通过它们的元素直接访问。只需将数据传递给我们现有的`setData`函数就可以了。我们将在一个函数中完成所有这些，`serviceLoaded`:

```
private function serviceLoaded(event: ResultEvent) : void   

{   

  var xmlData: XML = event.result as XML;   

  setData(   

    xmlData.product[0].imageURL,    

    xmlData.product[0].label,    

    xmlData.product[0].link    

    );   

}
```

我们还需要定义另一个函数`serviceError`，以备`HTTPService`出现问题时使用。通常，您会想要重试或指定一些其他更有意义的操作，但现在我们将简单地添加一个跟踪:

```
private function serviceError(event: FaultEvent) : void   

{   

  trace("Unable to get XML:" + event.fault.message);   

} 
```

[清单 2](https://www.sitepoint.com/examples/flex-productwidget/listing2.txt) 显示了 XML 版本的完整源代码，以及我们的 HTTPService 和相关函数。

接下来，我们将研究如何使用以 JSON 格式提供的数据。

##### 使用 JSON

我们现在将修改我们的示例，以处理 JSON 中提供的数据 JavaScript 对象符号格式。如果你是这个想法的新手，JSON 网站上有一个关于 JSON [的很好的解释:](http://json.org/)

JSON (JavaScript 对象符号)是一种轻量级的数据交换格式。对人类来说，读和写很容易。机器很容易解析生成。它基于 JavaScript 编程语言的子集，标准 ECMA-262 第三版-1999 年 12 月。JSON 是一种完全独立于语言的文本格式，但是它使用了 C 语言系列的程序员所熟悉的约定，包括 C、C++、C#、Java、JavaScript、Perl、Python 和许多其他语言。这些特性使 JSON 成为理想的数据交换语言。

要在 Flex 中使用 JSON，我们需要合并一个名为 ActionScript 3 Corelib 的库，它包含 JSON 支持、MD5 哈希和一些其他有用的功能，这些功能目前在 Flex 中还没有。你可以从谷歌代码项目页面下载这个库。解压缩文件，并记下它的位置。我们需要告诉 Flex Builder 下一步在哪里找到它。

回到 Flex Builder，右键单击您的 ProductWidget 项目，然后从上下文菜单中选择“属性”。现在单击 Flex 构建路径项，并选择库路径选项卡。您应该处于如下所示的窗口中:

![The Flex Build Path window](img/583fe8bd4f64e7a7c9a3c7231c8de13f.png)

点击添加 SWC 并导航到你解压文件的地方。所需的 SWC 文件可以在库的 lib/子文件夹中找到，它应该被称为 as3corelib.swc。选择该文件后，单击 OK，Flex Builder 现在可以愉快地处理 JSON 编码的数据。

现在让我们检查一下在 PHP 中创建 JSON 格式数据的代码。PHP5 集成了 JSON 支持，但是对于旧版本，你需要一个额外的 PHP 库——我建议你从 PEAR 下载并使用 [Services_JSON 库。](http://pear.php.net/package/Services_JSON)

输出 JSON 文本的 PHP 代码与 XML 示例非常相似。这一次，我们将首先定义一个类来保存数据，然后我们的`getData`函数将创建这个类的一个新实例，并用虚拟数据填充它。最后，我们将输出 JSON 编码的对象:

```
 <?php    

  class Data    

  {    

    public $imageURL;    

    public $productTitle;    

    public $productLink;    

  }    

  function getData()    

  {    

    $data = new Data();    

    $data->imageURL = "http://www.sxc.hu/pic/m/n/ni/nitewind23/638995_dead_rubber_ducky.jpg";    

    $data->productTitle = "Rubber duck";    

    $data->productLink = "http://www.sxc.hu/photo/638995";    

    return $data;    

  }    

  echo json_encode(getData());    

?> 
```

相当简单！

在 Flex 端，我们需要完成几个重要的步骤:首先，我们必须包含我们添加的库中的 JSON 类；接下来，我们将更改`HTTPService`块以指向新服务；最后，我们将修改`serviceLoaded`方法来解码 JSON 文本。

首先，在我们的`mx:Script`块的顶部，我们将指示 Flex 使用 ActionScript 3 Corelib 库中的 JSON 类:

```
import com.adobe.serialization.json.JSON;
```

我们还将告诉`HTTPService`JSON 服务的位置。JSON 结果以文本形式返回，所以我们还需要将`resultFormat`改为`text`:

```
<mx:HTTPService     

  id="productService"    

  url="http://www.example.com/service.php"     

  resultFormat="text"    

  result="serviceLoaded(event)"    

  fault="serviceError(event)"    

/>
```

最后，我们需要修改`serviceLoaded`方法，以便它能够解码 JSON 文本并将其转换成 Flex 对象。下面是`serviceLoaded`的新版本:

```
private function serviceLoaded(event: ResultEvent) : void    

{    

  var rawData:String = String(event.result);    

  var data:Object = JSON.decode(rawData);    

  setData(    

      data.imageURL,     

      data.productTitle,     

      data.productLink    

    );    

}
```

我们最后一个例子的完整源代码可以在清单 3 中看到。

##### 在 HTTPRequests 中使用参数

到目前为止，我们所有的例子都直接请求数据。在现实世界的例子中，您可能需要将参数传递给相关的`services`,比如一个搜索词或一个标签。要在`HTTPService`中设置参数，需要添加一个包含要传递的参数的`mx:Request`标签，以及一个用于请求的方法。如果我们想传递一个名为`search`的参数，它应该是这样的:

```
<mx:HTTPService     

  id="personRequest"     

  url="http://www.example.com/service_json.php"     

  resultFormat="text"     

  result="serviceLoaded(event)"    

  fault="serviceError(event)"    

  method="GET"    

  >    

  <mx:Request>    

    <search>duck</search>    

  </mx:Request>    

</mx:HTTPService>
```

##### 开始拉皮条

如你所见，用 PHP 连接 Flex 非常容易。如果您的 PHP 应用程序公开了一个返回 XML 或 JSON 数据的服务，Flex 几乎可以立即获取这些数据。编码快乐！

##### 恶作剧

感觉自信吗？测试你对这篇文章[的了解程度，并参加我们的测验](https://www.sitepoint.com/quiz/adobe/pimp-php-app-flex)。为了回答这个问题，您可以下载 Tour de Flex，这是一个独特的桌面应用程序，允许您浏览核心 Flex 组件、Adobe AIR 文档和数据集成以及第三方插件等资源。

此外，在 2009 年 5 月 6 日之前提交详细信息的每个人都将有机会赢得一份 Adobe Flex Builder 3。有三份可以送人，那就[试试吧！](https://www.sitepoint.com/quiz/adobe/pimp-php-app-flex)

## 分享这篇文章

```