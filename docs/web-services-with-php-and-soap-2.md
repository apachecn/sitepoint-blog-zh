# 用 PHP 和 SOAP 创建 Web 服务，第 2 部分

> 原文：<https://www.sitepoint.com/web-services-with-php-and-soap-2/>

在本系列第一部分中，我向您展示了使用 SOAP 协议开发应用程序是构建可互操作软件的一种很好的方式。我还展示了使用 NuSOAP 库构建自己的 SOAP 服务器和客户机是多么容易。这一次，我想向您介绍一些您在使用 SOAP-WSDL 文件时肯定会遇到的东西。

在这篇文章中，我们将讨论什么是 WSDL 文件以及如何使用它们。我将向您展示如何使用 NuSOAP 快速构建您的 WSDL 文件，并将一个 WSDL 文件合并到第一部分的 SOAP 服务器和客户端示例中。

## 什么是 WSDL 文件？

Web 服务描述语言(WSDL)文件是为 SOAP 服务提供元数据的 XML 文档。它们包含有关应用程序提供的函数或方法以及要使用的参数的信息。通过向您的服务的消费者提供 WSDL 文件，它为他们提供了发送有效请求所需的定义，正如您所希望的那样。您可以将 WSDL 文件视为应用程序通信的完整契约。如果你真的想让其他人更容易地消费你的服务，你应该将 WSDL 整合到你的 SOAP 编程中。

## WSDL 结构

就像 SOAP 消息一样，WSDL 文件需要遵循特定的模式，并且必须有特定的元素才有效。让我们看看构成有效 WSDL 文件的主要元素，并解释它们的用途。

```
<definitions>
 <types>
  ........
 </types>
 <message>
  <part></part>
 </message>
 <portType>
  .......
 </portType>
 <binding>
  ....
 </binding>
 <service>
  ....
 </service>
</definitions>
```

WSDL 文件的根元素是`definitions`元素。这是有意义的，因为根据定义，WSDL 文件是 web 服务的定义。types 元素描述了所使用的数据类型，在 WSDL 的例子中，使用了 XML schema。在`messages`元素中，是服务的数据元素的定义。每个`messages`元素可以包含一个或多个`part`元素。`portType`元素定义了可以用 web 服务执行的操作以及所使用的请求响应消息。在`binding`元素中，包含特定`portType`的协议和数据格式规范。最后，我们有`service`元素，它定义了包含服务的 URI(位置)的<ports or="" endpoints.="" simply="" speaking="" the="">服务元素的集合。</ports>

在命名 WSDL 2.0 规范中的一些元素时，术语略有变化。比如`portType`，已经改名为`Interface`。由于对 WSDL 2.0 的支持很弱，我选择了使用更广泛的 1.1 版本。

## 建立 WSDL 档案

手动编写 WSDL 文件可能很麻烦，因为它们必须包含特定的标签，并且通常很长。使用 NuSOAP 的好处是它可以为你创建一个 WSDL 文件！让我们修改我们在第一篇文章中制作的 SOAP 服务器来适应这一点。

打开`productlist.php`并修改它以反映下面的代码:

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
$server->configureWSDL("productlist", "urn:productlist");

$server->register("getProd",
    array("category" => "xsd:string"),
    array("return" => "xsd:string"),
    "urn:productlist",
    "urn:productlist#getProd",
    "rpc",
    "encoded",
    "Get a listing of products by category");

$server->service($HTTP_RAW_POST_DATA);
```

基本上，这是与以前相同的代码，但只有几个变化。第一个变化是增加了对`configureWSDL()`的调用；该方法充当一个标志，告诉服务器为我们的服务生成一个 WSDL 文件。第一个参数是服务的名称，第二个参数是服务的名称空间。对名称空间的讨论超出了本文的范围，但是请注意，虽然我们在这里没有利用它们，但是像 Apache Axis 和。NET do。最好将它们包含进来，以实现完全的互操作性。

第二个变化是向 register()方法添加了额外的参数。分解一下:

*   `getProd`是函数名
*   `array("category" => "xsd:string")`定义`getProd`的输入参数及其数据类型
*   `array("return" => "xsd:string")`定义函数的返回值及其数据类型
*   `urn:productlist`定义名称空间
*   `urn:productlist#getProd`定义 SOAP 动作
*   `rpc`定义通话类型(可以是`rpc`或`document`)
*   `encoded`定义`use`属性的值(可以使用`encoded`或`literal`
*   最后一个参数是描述`getProd`函数功能的文档字符串

现在将你的浏览器指向`http://yourwebroot/productlist.php?wsdl`，你会看到为你创建的全新的 WSDL 文件。继续复制源文件，将其保存为自己的名为`products.wsdl`的文件，并将其放在您的 web 目录中。

## 使用客户端消费 WSDL 文件

我们已经修改了 SOAP 服务器来生成 WSDL 文件，所以现在让我们修改 SOAP 客户机来消费它。打开前一篇文章中创建的`productlistclient.php`,简单地修改启动客户端的代码行:

```
$client = new nusoap_client("http://localhost/nusoap/productlist.php");
```

对此:

```
$client = new nusoap_client("products.wsdl", true);
```

`nusoap_client()`构造函数调用中的第二个参数告诉 NuSOAP 构建一个 SOAP 客户端来接受 WSDL 文件。现在在你的浏览器中启动`productlistclient.php`，你应该会看到和之前一样的结果，但是现在你使用的是 WSDL 能量！

## 摘要

在用 PHP 和 SOAP 创建 web 服务的本系列的第 2 部分中，我们回顾了使用 WSDL 文件实现最佳互操作性的重要性。我们讨论了组成 WSDL 文件的不同元素及其定义，然后我向您展示了如何使用 NuSOAP 库快速轻松地创建您自己的 WSDL 文件。最后，我们修改了 SOAP 服务器和客户机，以演示如何在您的应用程序中使用 WSDL。

您可能已经猜到了，我只是简单地介绍了一下 SOAP 能为您做些什么，但是通过这些新工具，您可以提供一种简单且广为接受的方式来向您的用户公开 web 服务。

对本文的评论已经关闭。有关于 PHP 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

<small>图片 via[Lilyana Vynogradova](http://www.shutterstock.com/gallery-9125p1.html)/[Shutterstock](http://shutterstock.com)</small>

## 分享这篇文章