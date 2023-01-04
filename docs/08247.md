# 休息——除了拼写，你还能做什么？第三部分

> 原文：<https://www.sitepoint.com/rest-can-you-do-more-than-spell-it-3/>

本系列的第 1 部分从理论的角度讨论了休息，具体来说，它到底是什么。然后，[第 2 部分](https://www.sitepoint.com/rest-can-you-do-more-than-spell-it-2)更深入地讨论了如何着手构建一个面向 REST 的系统。到目前为止，这无疑是一个令人兴奋的自我实现之旅，但是如果你愿意，没有时间休息。我们必须继续前进！

想象这是一个温暖、阳光明媚的夏日。你只是在散步，悠闲地在中午散步，突然你面对一个宁静的 API。你是做什么的？你如何与它互动？或者，正如我们这些了解内情的人会说的，“你如何消费 RESTful 服务？”朋友们，这就是本文的主题。

## 与 REST 交互

为了通过 HTTP 使用 REST 服务，在 PHP 程序中需要做三件基本的事情:指定要访问的资源(使用的或创建的)的 URL，指定请求所需的任何参数，最后调用正确的 HTTP 动词进行实际的检索或更新/创建。

首先，您需要确定想要创建、获取、更新或删除的 URL。记住，每个 REST 资源都由一个惟一的 URL 标识，因此选择正确的 URL 非常重要。有时组成 URL 的片段将由 API 严格定义，而其他时候 API 将接受任何内容，不管多长或多钝。在你自己定义 URL 的情况下，你应该努力使它尽可能的短并且有意义。URL 应该是人类可读的。

其次，您需要考虑您将在交易中使用的数据。也就是说，任何 web 交互都涉及到将数据移入或移出应用程序，因此设置要传输的数据非常重要。有许多方法可以做到这一点；您可以将数据作为`GET`或`POST`参数、HTTP 头，甚至是 XML 或 JSON 有效载荷来提供。

现在，最后一步；正在与 REST 服务对话。正如你可能猜到的，有很多方法可以做到这一点。如果你使用的是像 Zend Framework T1 或 T2 WS02 T3 这样的框架，你很可能会得到 REST/HTTP 便利功能。如果您没有使用框架，那么有两个 PHP 扩展会对您有所帮助。第一个(也是我将在这里使用的)是 cURL，它是 libcurl 库的包装器。第二个是 pecl_http 扩展，可以通过 pecl 轻松下载和安装。这两种方式对您来说都很好，允许您编写可以轻松处理 HTTP 内容和头的 PHP 代码。

我可能还应该提到，你不需要使用旋度或 PECL；当然，你可以用简单的 PHP 函数做任何事情(比如`fopen()`和`file_put_contents()`)，但是它有点罗嗦，不太优雅。关于使用这种 PHP 函数与远程服务器通信的例子，请参见达斯汀·伦内尔的文章[了解 OAuth——从头开始发推特](https://www.sitepoint.com/understanding-oauth-1/)。

## 将它整合在一起

每个卷曲活动都有四个步骤:

*   初始化 cURL 资源
*   设定任何必要的卷曲选项
*   执行 HTTP 调用
*   解析返回的响应

所以事不宜迟，让我们将三个主要步骤(和四个特定于 curl 的步骤)和一个示例放在一起:

```
<?php
// set up the URL value involved in a variable
$url = "http://www.funland.com/summerschedule";

// set up the data that is going to be passed
$events = array(
    array("event" => "20120601-0001",
          "name"  => "AC/DC Drink Till U Drop Concert",
          "date"  => "20120601",
          "time"  => "22000030"),
    array("event" => "20120602-0001",
          "name"  => "Enya – Can You Feel the Peace",
          "date"  => "20120602",  
          "time"  => "19300045"),
    array("event" => "20120603-0002",
          "name"  => "Nicki Menaj – The Midnight Girls Concerrtt",
          "date"  => "20120603",  
          "time"  => "21300039"));
$jsonData = json_encode($events) 

// perform the curl transaction
$ch = curl_init();
curl_setopt($ch, CURLOPT_URL, $url); 
curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
curl_setopt($ch, CURLOPT_POST, true);
curl_setopt($ch, CURLOPT_POSTFIELDS, $jsonData);

$response = curl_exec($ch); 
$decode = json_decode($response);
print_r($resonse);
```

该示例指定了将要调用的目标资源，以及带有事件信息的 JSON 负载。然后使用`curl_init()`函数初始化 cURL 句柄。该句柄用于与该调用相关的后续 cURL 函数。

然后设置一些 cURL 选项来告诉 cURL 你到底想要它做什么。这是通过`curl_setopt()`功能完成的。

*   `CURLOPT_URL`–让 cURL 知道您想要操作的 URL，在这种情况下使用之前定义的`$url`变量。
*   `CURLOPT_RETURNTRANSFER`–告诉 cURL 不要将响应直接发送给浏览器，而是从`curl_exec()`返回一个字符串。
*   `CURLOPT_POST`–标识使用什么 HTTP 动词，在本例中为`POST`。注意，这里需要值`true`作为第三个参数，否则该语句将不起作用。

cURL 是一个非常灵活的库，您可以指定许多可配置的选项。要获得完整的列表，请务必查看 PHP 网站上的 [cURL 文档。](http://php.net/curl)

然后是实际执行 cURL 调用并解释结果的时候了。如上所述，通过`json_encode()`函数输入 JSON 的数据将使用`$jsonData`变量传递给 cURL。如果您正在执行一个`GET`，那么逻辑将是类似的，除了将使用`CURLOPT_GET`，并且您将使用`json_decode()`函数跟踪 cURL 命令，该函数将获取返回的 JSON 数据并将其移动到 PHP 数组中。

## 摘要

本文给出了一个非常简单的大纲，说明了如何从 PHP 脚本中建立 HTTP 连接。现在唯一剩下的问题是——这个模型如何被认为是 RESTful 的，以及如何保持这种状态？请记住，因为 REST 是一种精神状态，而不是一种标准，所以对于一个特定的系统是否是 RESTful 的会有一定的争论。但是让我们回到我们的基本原则。

REST 将一切都视为资源，并为该资源分配一个 URL。我们要从第一行代码开始。此外，URL 还形成了一个有意义的名称，与 RPC 体系结构正好相反，在 RPC 体系结构中，所有内容都指向一个动作节点，而不一定是最终目的地。

REST 是无状态的，正如示例中所提到的，其中没有携带关于以前或将来事务的信息。每一个都独立于之前发生的事情(除了正常的数据依赖)。

REST 简单明了，但是健壮而强大。这个例子使用了一个标准协议(HTTP)和 cURL 扩展。不需要加载什么特别的东西，只需要 PHP 和 HTTP 的基本能力。

而且说到 HTTP，还有一点需要探讨；我们在上面的`print_r()`陈述中已经暗示过了；响应代码和 HTTP 头。如果您要在服务器上执行 POST 或其他操作，那么您需要知道它是否工作。类似地，您可能还想给服务器一些关于如何进行 POST 或者用 GET 获取什么类型的数据的指导。所有这些以及更多(嗯，大部分是“所有这些”)将在传奇的最后一章中讲述，在那里我将向您介绍 HTTP 头的迷人世界。

<small>图片 via[little Sam](http://www.shutterstock.com/gallery-351511p1.html)/[Shutterstock](http://www.shutterstock.com/)</small>

## 分享这篇文章