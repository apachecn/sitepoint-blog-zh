# 字符编码:文化整合的问题

> 原文：<https://www.sitepoint.com/issues-with-cultural-integration/>

在我目前正在开发的一个应用程序中，我遇到了一个关于字符集的经典问题。作为 PHP 的标准，所有字符串都被视为 [latin1](http://en.wikipedia.org/wiki/ISO_8859-1) ，但是我们现在需要在一些地方允许更大范围的字符集。

黄金标准解决方案是将所有内容都转换为 utf-8。因为 utf-8 覆盖了整个 [unicode](http://www.unicode.org/standard/WhatIsUnicode.html) 范围，所以它能够表示 latin1 可以表示的任何字符。不幸的是，这从一开始就比运行大型应用程序要容易得多。即使这样，也可能有第三方代码和扩展，假定为 latin1。我更愿意继续将 latin1 作为缺省值，并且只在少数我真正需要全部 utf-8 容量的地方跳过一些限制。

所以经过一番思考，我想到了另一个解决方案。公平地说， *hack* 可能比 *solution* 更具描述性，但尽管如此。想法是这样的:

*   使用 latin1，但提供 utf-8 格式的页面，在输出时对其进行编码。
*   在 latin1 中嵌入 utf-8 字符串，不知何故不编码它(但仍然编码其他所有内容)。

简单，嗯？

## 里面是 Latin1，外面是 utf-8。

当呈现 HTML 页面时，用一个[输出缓冲区](http://www.php.net/outcontrol)捕获输出并通过 [utf8_encode](http://www.php.net/manual/en/function.utf8-encode.php) 传输它是很简单的事情。因此，页面以 utf-8 格式提供服务，尽管内部的所有内容都是 latin1。这并没有什么好处，因为它仍然限制了我们使用 latin1 包含的字符范围。
我们实际上已经在这么做了，只是为了减少外部服务与我们系统通信的问题。特别是， [XmlHttpRequest](http://en.wikipedia.org/wiki/XMLHttpRequest) 默认为 utf-8，不管页面的编码是什么。

本质上，下面的片段举例说明了:

```
 // declare that the output will be in utf-8
header("Content-Type: text/html; charset=utf-8");
// open an output buffer, capturing all output
ob_start('output_handler');
// when the script ends, the buffer is piped through this functions, encoding it from latin1 to utf-8
function output_handler($buffer) {
  return utf8_encode($buffer);
} 
```

## 在 latin1 中嵌入 utf-8。

这是棘手的部分。不是简单地通过 utf8_encode 处理整个缓冲区，而是可以对字符串进行解析，因此一组特殊标记(例如`[[charset:utf8]] ... [[/charset:utf8]]`)之间的任何内容都保持原样，而其余部分则被假定为 latin1，并像以前一样使用 utf8_encode 进行编码。这确保了完全向后兼容，同时允许真正的 utf-8。

让我们修改之前的输出处理程序:

```
 header("Content-Type: text/html; charset=utf-8");
ob_start('output_handler');
function output_handler($buffer) {
  return preg_replace_callback(
    '~[[charset:utf8]](.*?)[[/charset:utf8]]~',
    'utf8_decode_first',
    utf8_encode($buffer));
}
function utf8_decode_first($match) {
  return utf8_decode($match[1]);
} 
```

仅此而已。我们现在可以在 latin1 编码的应用程序中嵌入完整的 utf-8 字符串，方法是用`[[chaset:utf8]]`包装它。为了增加可读性，我添加了一个助手函数:

```
 function utf8($utf8_encoded_byte_stream) {
  return '[[charset:utf8]]' . $utf8_encoded_byte_stream . '[[/charset:utf8]]';
} 
```

我们现在可以构造一个简单的字符串，如下所示:

```
 echo utf8("blÃ¥bÃ¦r") . "grød"; 
```

两个产出:**蓝莓红色**

**注:**正如 [Kore](#comment-793844) 所指出的，如果分隔符本身(如`[[charset:utf8]]`)是数据的一部分，就会有问题。为了解决这个问题，使用一个更独特的分隔符会更安全。你可以简单地用不太可能发生的事情来代替`charset:utf8`。它仍然不能完全防弹，但对于大多数实际用途来说已经足够好了。

## 处理输入。

您可能知道也可能不知道这一点，但是当提交表单时，浏览器会以与页面相同的编码发回数据。因为我们的应用程序主要是 latin1，我们需要用户输入是 latin1，以保持 <abbr title="Backwards compatibility">BC</abbr> 。因此所有输入都必须从 utf-8 解码为 latin1。这很简单；我们只需通过管道传输所有用户输入($_GET，$_POST 等)。)通过 [utf8_decode](http://www.php.net/manual/en/function.utf8-decode.php) 。因为我们已经运行了 latin1-on-the-inside-utf-8-on-the-outside 方案，所以这在我们的例子中已经存在。

然而，当用户需要提交 utf-8 时，这确实会带来问题，因为我们的用户在回复邮件时需要提交 utf-8。所以在这些地方，我们必须通过一种替代机制显式地访问“原始”字符串。在我们的例子中，我们需要修改 http-request 包装器，但是因为这是对 API 的扩展，所以不存在 BC 问题。

随着 PHP6 的出现，也许将来不再需要这样的攻击，但现在这给出了一个可行的、不引人注目的解决方案。

## 分享这篇文章