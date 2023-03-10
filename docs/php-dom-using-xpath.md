# PHP DOM:使用 XPath

> 原文：<https://www.sitepoint.com/php-dom-using-xpath/>

在最近的一篇文章中，我讨论了 PHP 的 DOM 实现，并介绍了各种从 XML 结构中提取数据和操作 XML 结构的函数。我也简单地提到了 XPath，但是没有太多的篇幅来讨论它。在本文中，我们将进一步了解 XPath，它是如何工作的，以及它是如何在 PHP 中实现的。您会发现，XPath 可以大大减少查询和过滤 XML 数据所需编写的代码量，并且通常会产生更好的性能。

我将使用上一篇文章中的 DTD 和 XML 来演示 PHP DOM XPath 功能。为了快速刷新您的记忆，下面是 DTD 和 XML 的样子:

```
<!ELEMENT library (book*)> 
<!ELEMENT book (title, author, genre, chapter*)> 
  <!ATTLIST book isbn ID #REQUIRED> 
<!ELEMENT title (#PCDATA)> 
<!ELEMENT author (#PCDATA)> 
<!ELEMENT genre (#PCDATA)> 
<!ELEMENT chapter (chaptitle,text)> 
  <!ATTLIST chapter position NMTOKEN #REQUIRED> 
<!ELEMENT chaptitle (#PCDATA)> 
<!ELEMENT text (#PCDATA)>
```

```
<?xml version="1.0" encoding="utf-8"?> 
<!DOCTYPE library SYSTEM "library.dtd"> 
<library> 
  <book isbn="isbn1234"> 
    <title>A Book</title> 
    <author>An Author</author> 
    <genre>Horror</genre> 
    <chapter position="first"> 
      <chaptitle>chapter one</chaptitle> 
      <text><![CDATA[Lorem Ipsum...]]></text> 
    </chapter> 
  </book> 
  <book isbn="isbn1235"> 
    <title>Another Book</title> 
    <author>Another Author</author> 
    <genre>Science Fiction</genre> 
    <chapter position="first"> 
      <chaptitle>chapter one</chaptitle> 
      <text><![CDATA[<i>Sit Dolor Amet...</i>]]></text> 
    </chapter> 
  </book> 
</library>
```

## 基本 XPath 查询

XPath 是一种可用于查询 XML 文档的语法。最简单的形式是，定义一个到所需元素的路径。使用上面的 XML 文档，下面的 XPath 查询将返回所有 book 元素的集合:

```
//library/book
```

就是这样。两个正斜杠表示 library 是文档的根元素，单斜杠表示 book 是子元素。很简单，不是吗？

但是如果你想指定一本书呢？比方说，你想归还“某个作者”写的任何一本书。其 XPath 应该是:

```
//library/book/author[text() = "An Author"]/..
```

您可以在方括号中使用`text()`来执行与节点值的比较，以及尾随的“/..”表示我们需要父元素(即在树中上移一个节点)。

XPath 查询可以使用两个函数之一来执行:`query()`和`evaluate()`。两者都执行查询，但是区别在于它们返回的结果类型。`query()`将总是返回一个`DOMNodeList`，而`evaluate()`将在可能的情况下返回一个类型化的结果。例如，如果您的 XPath 查询要返回某个作者写的书的数量，而不是实际的书本身，那么`query()`将返回一个空的`DOMNodeList`。`evaluate()`将简单地返回数字，这样您就可以立即使用它，而不必从节点中提取数据。

## XPath 的代码和速度优势

让我们做一个快速演示，返回一个作者写的书的数量。我们要看的第一种方法可以工作，但是没有使用 XPath。这是为了向您展示在没有 XPath 的情况下如何做到这一点，以及为什么 XPath 如此强大。

```
<?php
public function getNumberOfBooksByAuthor($author) { 
    $total = 0;
    $elements = $this->domDocument->getElementsByTagName("author");
    foreach ($elements as $element) {
        if ($element->nodeValue == $author) {
            $total++;
        }
    }
    return $number;
}
```

下一个方法实现了相同的结果，但是使用 XPath 只选择特定作者写的书:

```
<?php
public function getNumberOfBooksByAuthor($author)  { 
    $query = "//library/book/author[text() = '$author']/..";
    $xpath = new DOMXPath($this->domDocument);
    $result = $xpath->query($query); 
    return $result->length;
}
```

注意我们这次是如何去掉 PHP 测试作者价值的需求的。但是我们可以更进一步，使用 XPath 函数`count()`来计算这条路径的出现次数。

```
<?php
public function getNumberOfBooksByAuthor($author)  { 
    $query = "count(//library/book/author[text() = '$author']/..)";
    $xpath = new DOMXPath($this->domDocument);
    return $xpath->evaluate($query);
}
```

我们只需要一行 XPath 就可以检索到我们需要的信息，不需要用 PHP 进行费力的过滤。事实上，这是一种更简单、更简洁的方式来编写这个功能！

注意在最后一个例子中使用了`evaluate()`。这是因为函数`count()`返回一个类型化的结果。使用`query()`将返回一个`DOMNodeList`，但是你会发现它是一个空列表。

这不仅使你的代码更整洁，而且还带来了速度上的好处。我发现版本 1 比版本 2 平均快 30%，但是版本 3 比版本 2 快 10%(比版本 1 快 15%)。虽然这些度量会因服务器和查询的不同而有所不同，但使用最纯粹的 XPath 通常会带来相当大的速度优势，并使代码更容易阅读和维护。

## XPath 函数

有相当多的函数可以与 XPath 一起使用，并且有许多优秀的参考资料详细介绍了哪些函数是可用的。如果您发现您在迭代`DOMNodeList`或比较`nodeValue`的时候，您可能会发现一个 XPath 函数可以消除大量 PHP 代码。

你已经看到了`count()`是如何工作的。让我们使用`id()`函数返回给定 ISBNs 的书名。您需要使用的 XPath 表达式是:

```
id("isbn1234 isbn1235")/title
```

请注意，您要搜索的值用引号括起来，并用空格分隔；没有必要用逗号来分隔术语。

```
<?php
public function findBooksByISBNs(array $isbns) { 
    $ids = join(" ", $isbns);
    $query = "id('$ids')/title"; 

    $xpath = new DOMXPath($this->domDocument); 
    $result = $xpath->query($query); 

    $books = array();
    foreach ($result as $node) {
        $book = array("title" => $booknode->nodeValue);
        $books[] = $book;
    }
    return $books; 
}
```

在 XPath 中执行复杂的函数相对简单；诀窍是熟悉可用的功能。

## 在 XPath 中使用 PHP 函数

有时候，您可能会发现需要一些标准 XPath 函数无法提供的更强大的功能。幸运的是，PHP DOM 还允许您将 PHP 自己的函数合并到 XPath 查询中。

让我们考虑返回书名的字数。在其最简单的函数中，我们可以将该方法编写如下:

```
<?php
public function getNumberOfWords($isbn) {
    $query = "//library/book[@isbn = '$isbn']"; 

    $xpath = new DOMXPath($this->domDocument); 
    $result = $xpath->query($query); 

    $title = $result->item(0)->getElementsByTagName("title")
        ->item(0)->nodeValue; 

    return str_word_count($title); 
}
```

但是我们也可以将函数`str_word_count()`直接合并到 XPath 查询中。为此，需要完成几个步骤。首先，我们必须用 XPath 对象注册一个名称空间。XPath 查询中的 PHP 函数前面是“php:functionString ”,然后您要使用的函数的名称用括号括起来。同样，要定义的名称空间是`http://php.net/xpath`。命名空间必须设置为此；任何其他值都将导致错误。然后我们需要调用`registerPHPFunctions()`，它告诉 php 无论何时遇到以“PHP:”命名的函数，都应该由 PHP 来处理。

调用该函数的实际语法是:

```
php:functionString("nameoffunction", arg, arg...)
```

将所有这些放在一起会导致下面的`getNumberOfWords()`的重新实现:

```
<?php
public function getNumberOfWords($isbn) {
    $xpath = new DOMXPath($this->domDocument);

    //register the php namespace
    $xpath->registerNamespace("php", "http://php.net/xpath"); 

    //ensure php functions can be called within xpath
    $xpath->registerPHPFunctions();

    $query = "php:functionString('str_word_count',(//library/book[@isbn = '$isbn']/title))"; 

    return $xpath->evaluate($query); 
}
```

注意，您不需要调用 XPath 函数`text()`来提供节点的文本。`registerPHPFunctions()`方法会自动完成这项工作。然而，以下内容同样有效:

```
php:functionString('str_word_count',(//library/book[@isbn = '$isbn']/title[text()]))
```

注册 PHP 函数并不局限于 PHP 自带的函数。您可以定义自己的函数，并在 XPath 中提供这些函数。这里唯一的区别是，在定义函数时，你用的是“php:function”而不是“php:functionString”。此外，只可能提供它们自己的函数或静态方法。不支持调用实例方法。

让我们使用一个超出类范围的常规函数来演示基本功能。我们将使用的函数将只返回“乔治·奥威尔”的书。对于希望包含在查询中的每个节点，它必须返回 true。

```
<?php
function compare($node) {
    return $node[0]->nodeValue == "George Orwell";
}
```

传递给该函数的参数是一个由`DOMElement`组成的数组。由该函数来遍历数组，并确定被测试的节点是否应该在`DOMNodeList`中返回。在这个例子中，被测试的节点是`/book`，我们使用`/author`来做出决定。

现在我们可以创建方法`getGeorgeOrwellBooks()`:

```
<?php
public function getGeorgeOrwellBooks() { 
    $xpath = new DOMXPath($this->domDocument); 
    $xpath->registerNamespace("php", "http://php.net/xpath"); 
    $xpath->registerPHPFunctions(); 

    $query = "//library/book[php:function('compare', author)]"; 
    $result = $xpath->query($query); 

    $books = array(); 
    foreach($result as $node) { 
        $books[] = $node->getElementsByTagName("title")
            ->item(0)->nodeValue; 
    } 

    return $books;
}
```

如果`compare()`是一个静态方法，那么您需要修改 XPath 查询，使其读作:

```
//library/book[php:function('Library::compare', author)]
```

事实上，所有这些功能都可以很容易地用 XPath 编码，但是这个例子展示了如何扩展 XPath 查询，使其变得更加复杂。

在 XPath 中不可能调用对象方法。如果您发现需要访问一些对象属性或方法来完成 XPath 查询，那么最好的解决方案是尽可能使用 XPath，然后根据需要使用任何对象方法或属性来处理结果`DOMNodeList`。

## 摘要

在处理 XML 数据时，XPath 是减少必须编写的代码量和加快代码执行速度的好方法。虽然不是官方 DOM 规范的一部分，但是 PHP DOM 提供的附加功能允许您用自定义功能扩展普通的 XPath 函数。这是一个非常强大的特性，随着您对 XPath 函数越来越熟悉，您可能会发现对它的依赖越来越少。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章