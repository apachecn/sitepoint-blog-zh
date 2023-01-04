# 用 PHP 和 SOAP 创建 Web 服务，第 1 部分

> 原文：<https://www.sitepoint.com/web-services-with-php-and-soap-1/>

作为应用程序开发人员，为各种平台开发软件和服务的能力是一项必要的技能，但并不是每个人都使用相同的语言或平台，编写代码来支持所有这些是不可行的。如果有一个标准，允许我们编写一次代码，并允许其他人从他们自己的软件轻松地与它交互就好了。幸运的是有……它的名字叫肥皂。(SOAP 曾经是简单对象访问协议的首字母缩写，但是从 1.2 版本开始，该协议被简单地命名为 SOAP。)

SOAP 允许您构建可互操作的软件，并允许其他人通过网络利用您的软件。它定义了发送和接收远程过程调用(RPC)的规则，比如请求和响应的结构。因此，SOAP 不依赖于任何特定的操作系统或编程语言。重要的是，有人可以用自己选择的语言来表达和解析 SOAP 消息

在这个关于 web 服务的两部分系列的第一部分中，我将讨论 SOAP 规范以及创建 SOAP 消息所涉及的内容。我还将演示如何使用优秀的 NuSOAP 库来创建 SOAP 服务器和客户机，以说明 SOAP 的流程。在第二部分中，我将讨论 WSDL 文件的重要性，如何使用 NuSOAP 轻松地生成它们，以及客户如何使用 WSDL 文件来更好地理解您的 web 服务。

## SOAP 消息的结构

SOAP 是基于 XML 的，所以它被认为是人类可读的，但是有一个必须遵守的特定模式。让我们首先分解一个 SOAP 消息，去掉它的所有数据，只看组成 SOAP 消息的特定元素。

```
<?xml version="1.0"?>
<soap:Envelope
 xmlns:soap="https://www.w3.org/2001/12/soap-envelope"
 soap:encodingStyle="https://www.w3.org/2001/12/soap-encoding">
 <soap:Header>
  ...
 </soap:Header>
 <soap:Body>
  ...
  <soap:Fault>
   ...
  </soap:Fault>
 </soap:Body>
</soap:Envelope>
```

这可能看起来只是一个普通的 XML 文件，但是使它成为 SOAP 消息的是根元素信封，其名称空间`soap`为`https://www.w3.org/2001/12/soap-envelope`。`soap:encodingStyle`属性决定了文件中使用的数据类型，但是 SOAP 本身没有默认的编码。

`soap:Envelope`是强制的，但是下一个元素`soap:Header`是可选的，并且通常包含与认证和会话处理相关的信息。SOAP 协议不提供任何内置的身份验证，但是允许开发人员将它包含在这个 header 标记中。

接下来是必需的`soap:Body`元素，它包含实际的 RPC 消息，包括方法名，以及在响应的情况下，方法的返回值。`soap:Fault`元素是可选的；如果存在，它保存 SOAP 消息的任何错误消息或状态信息，并且必须是`soap:Body`的子元素。

现在您已经了解了 SOAP 消息的基本组成，让我们看看 SOAP 请求和响应消息可能是什么样子。先说一个请求。

```
<?xml version="1.0"?>
<soap:Envelope
 xmlns:soap="https://www.w3.org/2001/12/soap-envelope"
 soap:encodingStyle="https://www.w3.org/2001/12/soap-encoding">
 <soap:Body xmlns:m="http://www.yourwebroot.com/stock">
  <m:GetStockPrice>
   <m:StockName>IBM</m:StockName>
  </m:GetStockPrice>
 </soap:Body>
</soap:Envelope>
```

以上是获取特定公司股票价格的 SOAP 请求消息示例。在`soap:Body`中，您会注意到特定于应用程序的`GetStockPrice`元素。它不是一个 SOAP 元素，它的名字来自服务器上的函数，这个请求将调用这个函数。`StockName`也是应用程序特有的，是函数的参数。

响应消息类似于请求:

```
<?xml version="1.0"?>
<soap:Envelope
 xmlns:soap="https://www.w3.org/2001/12/soap-envelope"
 soap:encodingStyle="https://www.w3.org/2001/12/soap-encoding">
 <soap:Body xmlns:m="http://www.yourwebroot.com/stock">
  <m:GetStockPriceResponse>
   <m:Price>183.08</m:Price>
  </m:GetStockPriceResponse>
 </soap:Body>
</soap:Envelope>
```

在 soap:Body 元素中，有一个包含返回数据的`GetStockPriceResponse`元素和一个`Price`子元素。正如您所猜测的，`GetStockPriceResponse`和`Price`都是特定于这个应用程序的。

既然您已经看到了请求和响应的示例，并且理解了 SOAP 消息的结构，那么让我们安装 NuSOAP 并构建一个 SOAP 客户机和服务器来演示如何生成这样的消息。

## 构建 SOAP 服务器

在您的服务器上安装并运行 NuSOAP 再简单不过了；只需访问[sourceforge.net/projects/nusoap](http://sourceforge.net/projects/nusoap)，在你的 web 根目录下下载并解压软件包，就大功告成了。要使用这个库，只需在代码中包含`nusoap.php`文件。

对于服务器，假设我们被赋予了构建一个服务的任务，该服务提供给定产品类别的产品列表。服务器应该从请求中读取类别，查找任何与类别匹配的产品，并以 CSV 格式将列表返回给用户。

使用以下代码在 web 根目录下创建一个名为`productlist.php`的文件:

```
<?php
require_once "nusoap.php";

function getProd($category) {
    if ($category == "books") {
        return join(",", array(
            "The WordPress Anthology",
            "PHP Master: Write Cutting Edge Code",
            "Build Your Own Website the Right Way"));
	}
	else {
            return "No products listed under that category";
	}
}

$server = new soap_server();
$server->register("getProd");
$server->service($HTTP_RAW_POST_DATA);
```

首先，包含 nusoap.php 文件是为了利用 NuSOAP 库。然后，定义`getProd()`函数。之后，soap_server 类的一个新实例被实例化，`getProd()`函数用它的`register()`方法注册。

这就是创建您自己的 SOAP 服务器所需要的一切——很简单，不是吗？在现实世界中，您可能会从数据库中查找图书列表，但是因为我想关注 SOAP，所以我模仿`getProd()`返回了一个硬编码的书名列表。

如果你想在服务器中包含更多的功能，你只需要定义额外的功能(或者甚至是类中的方法),并像上面那样注册每一个。

现在我们有了一个工作的服务器，让我们构建一个客户机来利用它。

## 构建 SOAP 客户端

创建一个名为`productlistclient.php`的文件，并使用下面的代码:

```
<?php
require_once "nusoap.php";
$client = new nusoap_client("http://localhost/nusoap/productlist.php");

$error = $client->getError();
if ($error) {
    echo "<h2>Constructor error</h2><pre>" . $error . "</pre>";
}

$result = $client->call("getProd", array("category" => "books"));

if ($client->fault) {
    echo "<h2>Fault</h2><pre>";
    print_r($result);
    echo "</pre>";
}
else {
    $error = $client->getError();
    if ($error) {
        echo "<h2>Error</h2><pre>" . $error . "</pre>";
    }
    else {
        echo "<h2>Books</h2><pre>";
        echo $result;
        echo "</pre>";
    }
}
```

我们再次将`nusoap.php`和`require_once`包含在一起，然后创建一个新的`nusoap_client`实例。构造函数获取新创建的 SOAP 服务器的位置以进行连接。`getError()`方法检查客户机是否创建正确，如果不正确，代码显示一条错误消息。

`call()`方法生成并发送 SOAP 请求，以调用第一个参数定义的方法或函数。`call()`的第二个参数是 RPC 的相关参数数组。fault 属性和`getError()`方法用于检查和显示任何错误。如果没有错误，则输出函数的结果。

现在这两个文件都在您的 web 根目录中，在您的浏览器中启动客户端脚本(在我的例子中是`http://localhost/nusoap/productlistclient.php`)。您应该看到以下内容:

![](img/1d3086779d7f110e6b61c0d730f3ec9f.png "browser output")

如果您想出于调试目的检查 SOAP 请求和响应消息，或者如果您只是为了好玩而将它们分开，请将这些行添加到`productlistclient.php`的底部:

```
echo "<h2>Request</h2>";
echo "<pre>" . htmlspecialchars($client->request, ENT_QUOTES) . "</pre>";
echo "<h2>Response</h2>";
echo "<pre>" . htmlspecialchars($client->response, ENT_QUOTES) . "</pre>";
```

HTTP 头和 XML 内容现在将被附加到输出中。

## 摘要

在本系列的第一部分中，您了解了 SOAP 提供了构建支持多种平台和编程语言的可互操作软件的能力。您还了解了 SOAP 消息的不同部分，并构建了自己的 SOAP 服务器和客户机来演示 SOAP 是如何工作的。

在下一部分中，我将带您深入 SOAP 兔子洞，并解释什么是 WSDL 文件，以及它如何帮助您处理 web 服务的文档和结构。

对本文的评论已经关闭。有关于 PHP 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

<small>图片 via[Lilyana Vynogradova](http://www.shutterstock.com/gallery-9125p1.html)/[Shutterstock](http://shutterstock.com)</small>

## 分享这篇文章