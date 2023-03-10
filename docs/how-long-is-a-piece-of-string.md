# 你知道一根绳子有多长？

> 原文：<https://www.sitepoint.com/how-long-is-a-piece-of-string/>

这个问题最近一直困扰着我。想知道是否有人知道如何解决这个问题…

PHP 的 [serialize()](http://www.php.net/serialize) 函数允许您将 PHP 数据结构表示为一个字符串，然后可以使用 [unserialize()](http://www.php.net/unserialize) 解析并恢复为数据。

因为字符串非常容易生成，所以它开辟了在其他语言中使用它来与 PHP 交换数据的可能性，这就是我一直用 Javascript [在这里](http://cvs.sourceforge.net/viewcvs.py/xmlrpccom/scriptserver/ScriptServer/js/serializer.js?rev=1.4&view=auto)所做的事情。其他实现存在于 Ruby、Perl、Flash Actionscript 甚至 C#中——我在这里列出了我发现的[。](http://jpspan.sourceforge.net/wiki/doku.php?id=php:serialization#known_implementations)

总的来说，这种方法工作得很好——至少不需要在 PHP 端重新发明东西。但是有一个问题:一根绳子有多长？正如这个 bug 所显示的，这是一个问题。

以 Javascript 为例，如果我有一个类似“Főő”的字符串(“Foo”使用匈牙利语 o 字符–[见此处](http://www.slovo.info/testuni.htm))(注意，Sitepoint 有一个问题，似乎因此出现了实体——您需要查找字符)

 `var s = "Főő";
alert (s.length);`

会告诉我字符串长度是 3——Javascript(至少在 Mozilla / IE 中)在理解什么是字符方面很聪明。

当使用 Javascript 为 PHP 序列化这个字符串时，它的长度构成了编码的一部分，看起来像；

 `s:3:"Főő";`

不幸的是，根据 PHP 运行的服务器上使用的字符集，PHP 不会将字符串视为 3 个字符——会是一个更大的数字——对大多数人来说可能是 5 个——PHP 将一个字符视为 1 个字节长。换句话说，如果我只发送 Javascript 看到的字符串长度，PHP 的 unserialize()函数会抱怨报告的字符串长度与实际长度不匹配。

从[这里](http://www.derickrethans.nl/files/wereldveroverend-phpworks04.pdf)有一个很好的一般性问题的解释(PDF)。您可以通过运行下面的代码来查看(确保您的编辑器使用了类似 Unicode 代码页的东西——参见 [SciTE](https://www.sitepoint.com/blog/) 中的全局属性)；

结果可能看起来像这样:

 `s:5:"Főő";`

那么如何解决这个问题呢？我如何让 Javascript 报告一个和 PHP 看到的一样的字符串长度(字符串中的字节数)？

到目前为止，我一直在将 Javascript 中的字符串转换成 UTF-8，基本上是有计划的巧合，如果运行 PHP 的服务器使用的是类似 ISO-8859-1(西欧)的东西，它就能工作。UTF-8 中一个字符的字节数通常与它在 ISO-8859-1 中表示的字节数相匹配(即使它看起来很奇怪)。不幸的是，这在 Sourceforge 上确实有效——locale(1)实际上报告了 LC_CTYPE="en_US。UTF-8 英寸(这让我更加困惑，可能没有抓住重点)。

想尽一切办法避免在 PHP 中进行字符集转换(首先，iconv 刚刚成为默认 PHP 发行版的一部分)或者试图向 Javascript 报告操作系统正在使用的语言环境，因为在 PHP 中没有获取该信息的标准 API。看看其他人所做的，除了 US-ASCII(不幸的是没有有用的灵感)，看起来没有人考虑过任何事情。

有什么想法吗？

旁注——尽管浏览器会自动处理表单字符编码，但在 Mozilla 和 IE 中，它看起来像 XmlHttpRequest，这要由开发人员来处理，不管你设置的 HTTP 请求头是什么(虽然还没有 100%确认)。

## 分享这篇文章