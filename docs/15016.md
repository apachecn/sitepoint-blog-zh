# 创造和消费。NET Web 服务的 5 个简单步骤文章

> 原文：<https://www.sitepoint.com/net-web-services-5-steps-4/>

##### 第五步——炫耀

恭喜你，因为你现在已经建立了你的第一个完全成熟的。NET XML 数据 Web 服务，不需要我可能添加的任何 IDE 程序，只需要使用记事本这样简单的工具。现在有谁真的能说这是一个巨大的考验吗？我不这么认为。

此外，如果您想知道其他人将如何“发现”您的 Web 服务，那么不用再看了:使用 XML Web 服务“发现”,您可以公布您的 Web 服务并公开它的位置和其他信息，旁边还有一个. discomap 文件提供指向您的文件的链接。

XML Web Service discovery，简称 DISCO，是微软的 Web 服务发现工具。它“发现”Web 服务的 URL，并将该信息保存在本地服务器上的文件中。方法如下:

disco http://localhost/suppliers . asmx？迪斯科舞厅

这将创建一个包含 WSDL 文件的静态发现文件，该文件包含与您的 Web 服务相关的所有重要信息。与静态发现文件相对的是动态发现。vsdisco 文件，它记录了位于该 URL 中的所有可用的 Web 服务。

还记得第 2 步中用“？WSDL "？现在，对于一个已经“发现”的文件，您可以使用:

```
wsdl.exe /l:CS /n:WService /out:bin/GetSuppliers.cs     

  http://localhost/suppliers.wsdl
```

来创建您的源文件，进而创建用于 Web 服务访问的 DLL。

当你检查。disco 文件，您会注意到它包含以下信息:

```
<contractRef ref="http://localhost/suppliers.asmx?wsdl"     

  docRef="http://localhost/suppliers.asmx"     

   />
```

这包含到您的 Web 服务的链接，以便其他人可以发现它，以及 XSD 模式和服务描述。使用这些信息，其他人可以解析 WSDL 文档，构建代理源文件，并在本地实现这个 Web 服务！

如果你有什么想分享的，请到位于 http://uddi.microsoft.com 的微软 UDDI(通用描述、发现和集成)商业注册(UBR)节点，该节点目前由微软、IBM 和 Ariba 支持。在这里，您可以找到其他可用的 Web 服务及其详细信息，注册您的 Web 服务，并了解更多有关整个业务的信息。

***有点“异步”***

在结束本文之前，我需要提到一个从应用程序调用 Web 服务的额外方法。默认形式的 Web 服务被称为“同步”。因此，应用程序及其固有的 Web 服务必须同时完成。因此，在应用程序可以完全显示结果之前，应用程序中较快的方法必须等待较慢的方法完成，这可能会给客户端造成不必要的延迟。同样，由于 Web 服务调用使用端口 80 进行通信，它们有时会导致这种类型的延迟。你将如何补救这种情况？当然，有一点不同步！

进行异步方法调用有四种常见的方式。在这里，我们将演示如何利用`WaitHandle`方法等待我们的异步调用，该方法确定服务调用何时完成。异步调用的其他三种可能方法包括:

1.  在我们的`BeginSuppliers`方法中，`BeginInvoke`返回的`IasyncResult`的`IsCompleted`属性

*   异步调用完成时执行回调方法*   最后，在调用`EndInvoke`之前，并行任何处理或代码，直到调用完成

假设异步调用可以执行给定的任务，而不会影响周围的其他函数，让我们实现我们选择的技术。回到步骤 2，您会记得我们是如何为我们的。asmx Web 服务文件。

如果你花时间检查它，你会注意到，除了我们的主`ShowSuppliers`数据集函数，还列出了两个额外的函数:`BeginShowSuppliers`和`EndShowSuppliers`。这些异步函数将使我们能够产生实现`WaitHandle`类的`WaitOne()`方法的异步 Web 服务调用，下面是方法。

我们做这些都是从。我们在步骤 4 中创建的 aspx 页面。下面是附加代码:

```
//Instantiate DLL    

GetInfo supInfo = new GetInfo();    

// Begin the Asynchronous call to ShowSuppliers    

IAsyncResult AsyncWS = supInfo.BeginShowSuppliers(Catg, null, null);    

//We wait for the callback    

AsyncWS.AsyncWaitHandle.WaitOne();    

//We return the data bypassing the initial ShowSuppliers Method    

DataSet MyData = supInfo.EndShowSuppliers(AsyncWS);
```

请注意，只添加了几行代码。唯一改变的是，我们在实例化 dll 之后添加了异步方法。在这之后，我们创建一个`IasyncResult`对象的实例，它将让我们知道 Web 服务过程何时完成。

```
IAsyncResult AsyncWS = supInfo.BeginShowSuppliers(Catg, null, null);
```

这里，我们调用了异步的`BeginShowSuppliers`方法，该方法接受我们的初始下拉列表参数和另外两个强制参数，在本例中，这两个参数没有包含在内，而是被替换为`null`。第二个参数通常会调用负责在`BeginShowSuppliers`完成后调用另一个方法的`AsyncCallBack`对象，这与我们的例子相反。第三个参数将包含关于异步操作的信息。

然后我们接下来实现`AsyncWaitHandle`，它允许我们合并和处理不同种类的同步技术。有几个`AsyncWaitHandles`可供我们选择。使用下面包含的`WaitOne`方法，我们等待`WaitHandle`接收回调信号。其他可用的方法包括:WaitAll()，它等待所有元素接收回调信号；以及`WaitAny()`，它等待任何一个元素接收回调信号。两者都使用指定的数组作为重载的元素参数之一，并使用一个整数表示特定的等待时间间隔和/或时间跨度。在任何情况下，所有`WaitHandles`都有多个可用的过载表，可以在中进一步查看。NET SDK 文档。

```
//We wait for the default callback    

AsyncWS.AsyncWaitHandle.WaitOne();
```

关于上面那行，有一个简短的题外话:尽管我们的`WaitOne`方法在默认形式下可以很好地工作，但是它可能会被重载。举例来说，我们的`WaitOne`处理程序一旦在派生类中被重写，就会阻塞当前线程，直到当前的`WaitHandle`收到信号。它允许的两个参数，与其他两个`Wait`方法也接受的参数类似，是:

1.  等待经过设定的毫秒数，并且
2.  一个布尔值，true 或 false，指定是否在等待前退出同步域。

一旦等待结束，我们通过从`WaitHandle`获得 OK 的`EndShowSuppliers`方法返回结果。

`AsyncWaitHandle`方法是一个资源释放器，它批准了下面的`supInfo.EndShowSuppliers`方法，并依次获取我们的数据，而不会让应用程序的其他部分变得不耐烦。

```
//We return the data    

DataSet MyData = supInfo.EndShowSuppliers(AsyncWS);
```

因此，在应对 HTTP 网络流量可能导致的速度限制时，我们可以实现我们的 Web 服务，而不用担心应用程序的其余部分会不耐烦地等待它全部完成。在某种程度上的异步性，我们的应用程序将运行它的整个过程，与我们可能悠闲的 Web 服务相结合，当它完成时简单地跳转。

总之，我们的`BeginShowSuppliers`方法一旦初始化，就会立即返回(虽然在这种情况下不是这样，因为我们使用了`WaitHandler`)来通知您适用的回调函数该过程已经完成，并且可以调用`EndShowSuppliers`方法并返回结果。

现在您已经知道了:快速浏览异步 Web 服务。正如你可能想知道的，是的，这可能比我们讨论的要复杂得多。然而，以你现在所学的量，它应该不那么令人生畏了。

##### 结论

意识到你可以多快地创造出一个漂亮的，实用的。NET XML 数据 Web 服务——而且是一个异步的服务——应该会让您喜笑颜开。此外，一定要探索所有的。NET 文档，以获得更多的详细信息，这些信息包括额外的调整、实现错误处理、安全性、缓存以及在创建和使用 Web 服务时应该记住的其他方面。里面有很多东西。NET 文档和在线文档，它们对这一切进行了更详细的描述。

创建和使用您自己的 Web 服务，通过 Internet 发现其他可用的服务，并像使用和利用驻留在本地服务器上的应用程序一样使用它们，真的非常容易！随着时间的流逝。NET 显示出自己是一个更加令人惊奇和极其强大的平台。下次见，开心。NETing！

**Go to page:** [1](/net-web-services-5-steps) | [2](/net-web-services-5-steps-2/) | [3](/net-web-services-5-steps-3/) | [4](/net-web-services-5-steps-4/)

## 分享这篇文章