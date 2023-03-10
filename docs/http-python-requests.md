# HTTP Python 请求初学者指南

> 原文：<https://www.sitepoint.com/http-python-requests/>

任何东西都可以通过请求在网上获得。如果您需要来自 Python 应用程序中的网页的信息，您需要一个 web 请求。在本文中，我们将深入研究 Python 请求。我们将看看 web 请求是如何构造的，以及如何发出 Python 请求。最后，您将能够使用 Python 请求库，这使得整个过程更加容易。

## HTTP 请求简介

为了在网络上交换数据，我们首先需要一个通信协议。我们浏览网页时使用的协议是超文本传输协议，简称 HTTP。HTTP 使用 TCP 作为传输协议，因为它需要可靠的传输，而只有 TCP 能够保证这一点。

假设有一个我们需要的资源——比如一个 HTML 页面，位于世界某个地方的 web 服务器上。我们想要访问这个资源，或者换句话说，我们想要在我们的 web 浏览器中查看那个页面。我们要做的第一件事是发出一个 HTTP 请求。HTTP 是一种客户端-服务器协议，这意味着请求是由客户端发起的。

服务器收到请求后，会处理它们并返回适当的响应。

服务器可能会以不同的方式回复。它可能会发送我们请求的资源，或者如果事情没有按预期进行，它会回复状态代码。

在每种通信协议中，信息都需要在特定的字段中。这是因为客户机和服务器都应该知道如何解释请求或响应。在接下来的几节中，我们将看看 HTTP 请求和 HTTP 响应是如何构建的。我们还将讨论最重要的字段的作用。

### HTTP 请求

HTTP 最重要的设计特征之一是它是人类可读的。这意味着，当我们查看一个 HTTP 请求时，我们可以轻松地读取所有内容，即使其中有很多复杂性。HTTP 的另一个特点是**无状态**。这意味着一个接一个地处理的两个请求之间没有联系。HTTP 协议不记得先前请求的任何内容。这意味着每个请求必须包含服务器执行请求所需的所有内容。

有效的 HTTP 请求必须包含以下元素:

*   HTTP 方法——比如`GET`或`POST`
*   HTTP 协议的版本
*   要提取的资源的路径

然后，我们还可以添加一些可选的头，指定关于发送者或消息的附加信息。常见的 HTTP 请求头的一个例子是`User-Agent`或者客户端喜欢的自然语言。这两个可选的头给出了发出请求的客户机的信息。

这是一个 HTTP 消息的例子，我们可以清楚地理解所有指定的字段:

```
~~~http
GET / HTTP/1.1
Host: www.google.com
Accept-Language: en-GB,en;q=0.5
~~~ 
```

第一行指定请求类型和 HTTP 协议的版本。然后我们指定发送请求的客户机接受的`Host`和语言。通常，消息要长得多，但这暗示了它们的样子。

### HTTP 响应

现在我们对 HTTP 请求有了一个概念，我们可以继续查看 HTTP 响应。

HTTP 响应通常包含以下元素:

*   HTTP 协议的版本
*   带有描述性短信的状态代码
*   HTTP 头的列表
*   包含所请求资源的消息正文

既然我们已经介绍了您需要的基本要素，那么在进行下一步之前，有必要做一个总结。现在应该清楚了，每当客户机想要与 HTTP 服务器通信时，它必须创建并发送一个 HTTP 请求。然后，当服务器收到它时，它创建并发送一个 HTTP 响应。

我们终于准备好引入 Python 请求库了。

## Python 请求库

Python [请求库](https://requests.readthedocs.io/en/latest/)允许您发送 Python HTTP 请求——从基本的到复杂的。Python 请求库抽象了进行复杂 Python 请求的复杂性，提供了一个易于使用的接口。在接下来的小节中，我们将看到如何创建简单的 Python 请求并解释响应。我们还将看到 Python 请求库提供的一些特性。

### 安装 Python 请求

首先，我们需要安装 Python 请求库。让我们使用`pip`来安装它:

```
$ pip install requests 
```

一旦 Python 请求库被正确安装，我们就可以开始使用它了。

### Python 请求的第一个 GET 请求

我们要做的第一件事是创建一个 Python 文件。在这个例子中，我们称之为`web.py`。在该源文件中，插入以下代码:

```
import requests

URL = "https://www.google.com"
resp = requests.get(URL)

print(resp) 
```

这个程序向 Google 发出一个 GET 请求。如果我们运行这个程序，我们可能会得到这样的输出:

```
$ python web.py
<Response [200]> 
```

那么，这意味着什么呢？

我们之前讨论过状态代码。这个输出告诉我们，我们的请求已经被成功地接收、理解和处理。还有其他代码，我们可以列出一些最常见的代码:

*   `301 Moved Permanently`。这是一封重定向邮件。我们要查找的资源的 URL 已被移动。新的 URL 与响应一起出现。

*   `401 Unauthorized`。这表示客户端错误响应。在这种情况下，服务器告诉我们，在继续处理请求之前，我们必须进行身份验证。

*   `404 Not found`。这也表示客户端错误响应。特别是，这意味着服务器找不到我们正在寻找的资源。

如果我们希望有条件地检查状态，并根据状态代码提供不同的操作，该怎么办？嗯，我们可以很容易地做到这一点:

```
import requests

URL = "https://www.google.com/blah"
resp = requests.get(URL)

if resp.status_code == 200:
  print("Okay, all good!")
elif resp.status_code == 301:
  print("Ops, the resource has been moved!")
elif resp.status_code == 404:
  print("Oh no, the resource wasn't found!")
else:
  print(resp.status_code) 
```

如果我们现在运行脚本，我们会得到不同的东西。试一试，看看我们得到了什么。😉

如果我们还想要每个状态代码附带的描述性短消息，我们可以使用`resp.reason`。在 200 状态码的情况下，我们将简单地得到`OK`。

### 检查 Python 请求的响应

至此，我们知道了如何发出一个基本的 Python 请求。在请求之后，我们希望得到响应，对吗？

在上一节中，我们看到了如何获取响应的状态代码。现在，我们想要读取响应的主体，这是我们请求的实际资源。为此，我们需要使用`resp.content`。假设我们正在寻找谷歌主页。

这是我们运行脚本时得到的结果:

```
b'<!doctype html><html itemscope="" itemtype="http://schema.org/WebPage" lang="en"><head><meta content="text/html; [...] 
```

我在上面添加了`[...]`,因为我们得到的资源——它是一个`text/html`文档——太长了，无法打印。差多少？我们可以利用`len(resp.content)`来获取这些信息。在上面的例子中，它是 13931 字节——肯定太多了，无法在这里打印出来！

### 利用 API

Python 请求库变得如此流行的原因之一是因为它使得与 API 的交互变得非常容易。对于这个例子，我们将使用一个简单的 API 来预测一个人的年龄，给出他们的名字。这个 API 叫做 [Agify](https://agify.io/) 。

该示例的代码如下:

```
import requests
import json

URL = "https://api.agify.io/?name=Marcus"
resp = requests.get(URL)

if resp.status_code == 200:
  encoded = resp.json()
  print(encoded['age'])
else:
  print(resp.status_code) 
```

在这种情况下，我们想知道一个名叫 Marcus 的人的年龄。一旦我们有了响应，如果状态码是 200，我们使用`resp.json()`在 JSON 中解释结果。此时，我们有了一个类似字典的对象，我们可以打印估计的年龄。

马库斯的估计年龄是`41`岁。

### 自定义标题

HTTP 头为 HTTP 会话的双方提供了附加信息。在下面的例子中，我们将看到如何改变 HTTP GET 请求的头。特别是，我们将改变`User-Agent`和`Accept-Language`头。`User-Agent`告诉服务器一些关于应用程序、操作系统和请求代理的供应商的信息。`Accept-Language`报头传达了客户端能够理解的语言。

这是我们简单的代码片段:

```
import requests

URL = "https://www.google.com"
custom_headers = {'Accept-Language': 'fr-CH, fr;q=0.9, en;q=0.8, de;q=0.7, *;q=0.5', 'User-Agent': 'Mozilla/5.0 (Linux; Android 12; SM-S906N Build/QP1A.190711.020; wv) AppleWebKit/537.36 (KHTML, like Gecko) Version/4.0 Chrome/80.0.3987.119 Mobile Safari/537.36'}
resp = requests.get(URL, headers=custom_headers)

if resp.status_code == 200:
  # handle the response
  print(resp.content[:100])  
else:
  print(resp.status_code) 
```

如果一切顺利，您应该会得到这样的结果:

```
$ <!doctype html><html lang="fr"><head><meta charset="UTF-8"><meta content="width=device-width,mini [...] 
```

在这个例子中，我们修改了`User-Agent`，假装我们的请求来自 Mozilla Firefox。我们还说我们的操作系统是 Android 12，我们的设备是三星 Galaxy S22。

由于我们已经打印了上面响应的前 100 个字符，我们可以看到我们收到的 HTML 页面是法语的。

## 结论

在本文中，我们讨论了 HTTP 协议，并对其进行了简单的理论介绍。然后我们查看了 Python 请求库。我们看到了如何编写基本的 Python HTTP 请求，以及如何根据我们的需要定制它们。

我希望你会发现这个库和这篇文章对你的项目有用。

**相关阅读:**

*   [为什么要学 Python？](https://www.sitepoint.com/why-learn-python/)
*   [四个程序员如何得到他们的第一份 Python 工作](https://www.sitepoint.com/how-programmers-got-first-python-jobs/)
*   [使用 Python 解析电子表格数据](https://www.sitepoint.com/using-python-parse-spreadsheet-data/)
*   [Python 自然语言处理入门](https://www.sitepoint.com/natural-language-processing-python/)
*   课程:[学习 Python 编程基础](https://www.sitepoint.com/premium/courses/learn-programming-fundamentals-with-python-2916)

## 分享这篇文章