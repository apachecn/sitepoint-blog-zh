# PHP DOM:使用 XML

> 原文：<https://www.sitepoint.com/php-dom-working-with-xml/>

SimpleXML 允许您快速、轻松地处理 XML 文档，在大多数情况下，SimpleXML 就足够了。但是，如果您以任何严肃的身份使用 XML，您最终将需要一个 SimpleXML 不支持的特性，这就是 PHP DOM(文档对象模型)的用武之地。

PHP DOM 是 W3C DOM 标准的一个实现，它比 SimpleXML 更符合对象模型。乍一看，这似乎有点让人不知所措，但是如果您愿意学习，您会发现这个用于访问和操作 XML 文档的库提供了对 PHP 中工作的 XML 文档的大量控制。这是因为 DOM 区分 XML 文档的各种成分，比如不同的节点类型。

为了探索与 PHP DOM 相关的一些基本功能，让我们创建一个能够添加和删除图书馆中的书籍并查询目录的类。它应该提供以下功能:

*   通过 ISBN 查询一本书
*   向图书馆添加一本书
*   从图书馆拿走一本书
*   查找特定流派的所有书籍

## DTD 和 XML

在本文中，我将使用以下描述图书馆及其书籍的 DTD 和 XML。这应该提供足够的材料来演示如何使用扩展:

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

理解 DOM 最重要的事情之一是*节点*的概念。节点实质上是 XML 文档中的任何概念项。如果它是一个元素(比如`chapter`)，那么它就是一个节点。如果它是一个属性，(比如`isbn`，那么它被 DOM 视为一个节点。节点提供了 XML 文档的原子结构。

PHP DOM 子类`DOMNode`提供代表文档不同方面的子类。所以，`DOMDocument`实际上继承了`DOMNode`。`DOMElement`和`DOMAttr`也继承自`DOMNode`。拥有一个公共的父类使您能够拥有对所有节点都可用的公共方法和属性，例如那些用于确定节点的类型、值甚至添加到节点的方法和属性。

## 图书馆课

名为`Library`的类为简介中概述的所需功能提供了方法。它还有一个构造函数和析构函数，以及存储 DOM 文档和 XML 文件路径的内部属性。对 DOM 文档引用执行各种操作，在将树作为 XML 保存回文件系统时使用路径。

```
<?php 
class Library
{
    private $xmlPath;
    private $domDocument;

    public function __construct($xmlPath) {
        // TODO: instantiate the private variable representing
        // the DOMDocument
    }

    public function __destruct() {
        // TODO: free memory associated with the DOMDocument
    }

    public function getBookByISBN($isbn) {
        // TODO: return an array with properties of a book 
    }

    public function addBook($isbn, $title, $author, $genre, $chapters) {
        // TODO: add a book to the library 
    }

    public function deleteBook($isbn) {
        // TODO: Delete a book from the library
    }

    public function findBooksByGenre($genre) {
        // TODO: Return an array of books
    }
}
```

我会刻意保持简单，因为这个例子只是用来演示 DOM 能做什么。在现实世界的应用程序中，也许您会实例化 book 对象来更全面地封装问题，并且您可能还想更优雅地处理错误。但是，在这个阶段，您不需要这样做。我们可以假设传递和返回的值是字符串或数组，错误可以通过抛出一个通用异常来处理。

## 处理对象构造和销毁

该构造函数被设计为获取要用作参数的 XML 文档的路径。它做了一些测试来确保文档是有效的。

第一个测试是确定加载的文档使用“库”doctype。每个`DOMDocument`都有一个公共属性`doctype`，它返回 XML 文档使用的 doctype。所以对于这个例子，当你加载文档时，你应该看到`doctype`属性被设置为“库”。

第二个测试是确保使用公共的`systemId`或`publicId`属性以正确的方式定义所使用的定义。这里使用的 XML 由系统上指定为`library.dtd`的 DTD 定义，所以它通过与`systemId`属性进行比较来测试。

第三个测试是根据 DTD 确保文档本身是有效的。文档的验证还检查文档是否格式良好(即标签不匹配等)。)并且它遵守它所基于的 DTD。

一旦满足了所有这些条件，它就存储一个对加载的文档的引用和 XML 文件的路径作为内部属性，供以后其他方法使用。但是，如果任何一个测试失败，就会抛出一个异常。

```
<?php
public function __construct($xmlPath) { 
    //loads the document 
    $doc = new DOMDocument(); 
    $doc->load($xmlPath); 

    //is this a library xml file? 
    If ($doc->doctype->name != "library" ||
        $doc->doctype->systemId != "library.dtd") { 
        throw new Exception("Incorrect document type"); 
    } 

    //is the document valid and well-formed? 
    if($doc->validate()) {
        $this->domDocument = $doc; 
        $this->xmlPath = $xmlPath;
    } 
    else {
        throw new Exception("Document did not validate"); 
    } 
}
```

析构函数方法释放`$domDocument`使用的所有内存。这实际上只是一个简单的取消属性设置的调用。

```
<?php
public function __destruct() { 
    unset($this->domDocument); 
}
```

## 按书号还书

现在来看看读取和操作底层 XML 文档的主要方法。

第一种方法从提供的 ISBN 中获取一本书的详细信息。您可以提供一个字符串形式的 ISBN，该方法返回一个数组，详细说明图书的属性。

PHP DOM 提供了一个非常简单的函数来返回基于 ID 的特定元素—`getElementById()`,它返回一个`DOMElement`对象。为此，您必须在 DTD 中指定一个 ID，就像我一样:

```
<!ATTLIST book isbn ID #REQUIRED>
```

重要的是要知道`getElementById()`只有在文档已经根据 DTD 进行了验证的情况下才有效。如果没有，那么这个函数就不会发现这个元素有一个 ID。

从文档中获取元素的另一种方法是使用`getElementsByTagName()`。这个方法返回一个具有指定标记名的节点集合。返回的集合是一个`DOMNodeList`，它是可遍历的。

`DOMNodeList`中的项目也可以通过它们在列表中的位置用`item()`挑选出来。因为 DTD 定义一本书只能有一个作者，所以我们知道`DOMNodeList`将包含一个可以用`item(0)`访问的节点。DTD 强化了这一事实，如果它在文档中是不同的，那么在创建库对象时，您将会收到一个验证错误。

一旦找到了想要的特定节点，就可以使用公共属性`nodeValue`找到它的值。

要访问属性，您可以利用返回一个`DOMNamedNodeMap`的`DOMNode`的公共属性。这类似于`DOMNodeList`,因为它是可遍历的，但是您也可以使用`getNamedItem()`方法挑选出一个特定的属性，并将属性的名称作为字符串传递。返回值是一个`DOMNode`。

检索一本书及其信息的方法的实现如下所示:

```
<?php
public function getBookByISBN($isbn) 
{ 
    // get a book element from the isbn ID 
    $book = $this->domDocument->getElementById($isbn); 

    // if a book was not returned...
    if (!$book) {
        throw new Exception("No book found with ISBN ". $isbn); 
    }

    $arrBook = array();
    $arrBook["isbn"] = $isbn; 

    // get the data from the elements based on their tag names 
    //
    // we know these DOMNodeLists will only return one 
    // item since the DTD states this
    $arrBook["author"] = $book->getElementsByTagName("author")
        ->item(0)->nodeValue;
    $arrBook["title"]  = $book->getElementsByTagName("title")
        ->item(0)->nodeValue; 
    $arrBook["genre"]  = $book->getElementsByTagName("genre")
        ->item(0)->nodeValue; 

    $chapters = $book->getElementsByTagName("chapter"); 

    $arrChapters = array(); 

    // iterate over the chapter elements 
    foreach($chapters as $chapter) { 
        $chapterId = $chapter->attributes
            ->getNamedItem("position")->nodeValue; 
        $chapterTitle = $chapter
            ->getElementsByTagName("chaptitle")->item(0)
            ->nodeValue; 
        $chapterText = $chapter
            ->getElementsByTagName("text")->item(0)
            ->nodeValue; 

        $arrChapter["title"] = $chapterTitle; 
        $arrChapter["text"] = $chapterText; 

        $arrChapters[$chapterId] = $arrChapter; 
    } 

    $arrBook["chapters"] = $arrChapters; 

    return $arrBook; 
}
```

从 XML 文档中识别和提取数据相对简单。需要克服的主要障碍是理解节点概念；一旦你理解了这一点，你会发现获得你想要的数据是一个简单的过程。

## 向图书馆添加图书

定义的下一个方法将一本书添加到 XML 数据库中。该方法接受要添加的书籍的属性和章节数组。

执行此类任务的一种方式是使用`createElement()`方法，将这个新节点添加到文档中，并设置对它的引用，这样您就可以从该点开始对对象进行操作。创建元素时，还必须将其添加到文档中。使用`createElement()`不会自动将它添加到文档中。它将元素与文档相关联，但仅此而已。一实例化就添加想要成为文档一部分的元素是一个好习惯，这样它们就不会被忘记！

您可以使用`documentElement`属性来标识 XML 文档的根元素。如果我们不这样做，只是直接添加到文档中，我们实际上是在文档的最后添加了一个子元素(即在`library`元素之外)。这将导致验证错误。仔细想想，DOM 的这种行为是完全合理的；将文档视为根元素并向其中添加一个子元素会将其放在`library`元素之后，因为这是文档的第一个子元素。

当然，`book`元素必须包含 ISBN，所以必须给新创建的元素添加一个属性。有两种方法可以做到这一点。最简单的是使用`setAttribute()`,它将属性的名称和属性值作为参数。第二种方法是创建一个`DOMAttr`对象，然后将其添加到元素中。`DOMAttr`是`DOMNode`的子类，因此它受益于其父类提供的所有继承方法和属性。

`setAttribute()`和`setAttributeNode()`负责添加和更新与元素相关的属性。如果该属性不存在，将会创建它。如果它确实存在，它将被更新。

要为文本元素提供值，建议使用`DOMCdataSection()`。书籍的章节在 DTD 中以 PCDATA 而不是 CDATA 的形式给出。这是因为不能将元素描述为直接包含 CDATA 我们必须将其声明为 PCDATA，然后将内容包装在`<![CDATA[...]]>`中。这听起来有些违背直觉，因为我们需要能够将未解析的字符数据放入文本元素中以备后用，但这就是为什么我们必须创建一个特定的`DOMCdataSection`；这将安全地在`<![CDATA[...]]>`中包装我们的文本。如果你直接在一个节点上添加 HTML，你会发现无效的字符如<或&会被转换成相应的实体(即&lt；和&amp；).这是因为这些字符在 XML 中有特殊的含义。实体的&符号和大于号开始标记。DOM 替换了这些，以便在加载或验证文档时不会导致任何解析问题。

添加图书的最后一步是将新文档保存回文件中，这是通过文档的`save()`方法完成的。

该方法看起来像这样:

```
<?php
public function addBook($isbn, $title, $author, $genre, $chapters) 
{ 
    // create a new element represeting the new book 
    $newbook = $this->domDocument->createElement("book"); 
    // append the newly created element
    $this->domDocument->documentElement
        ->appendChild($newbook);

    // setting the attribute can be done in one of two ways 
    // Method One: 
    // $newbook->setAttribute("isbn", $isbn); 

    // Method Two: 
    $idAttribute = new DOMAttr("isbn", $isbn); 
    $newbook->setAttributeNode($idAttribute); 

    $title = $this->domDocument
        ->createElement("title", $title); 
    $newbook->appendChild($title); 

    $author = $this->domDocument
        ->createElement("author", $author); 
    $newbook->appendChild($author); 

    $genre = $this->domDocument
        ->createElement("genre", $genre); 
    $newbook->appendChild($genre); 

    foreach($chapters as $position => $chapter) {
        $newchapter = $this->domDocument
            ->createElement("chapter"); 
        $newbook->appendChild($newchapter); 

        $newchapter->setAttribute("position", $position);

        $newchaptitle = $this->domDocument
            ->createElement("chaptitle", $chapter["title"]);
        $newchapter->appendChild($newchaptitle); 

        $newtext = $this->domDocument->createElement("text"); 
        $newchapter->appendChild($newtext); 

        // Rather than creating a new element, create a
        // DOMCdataSection which ensures our text is
        // wrapped in <![CDATA[ and ]]>
        $cdata = new DOMCdataSection($chapter["text"]); 
        $newtext->appendChild($cdata); 
    }

    // save the document 
    $this->domDocument->save($this->xmlPath); 
}
```

## 从图书馆删除图书

下一个要解决的方法是删除一本书。这只是识别 XML 文档中想要删除的元素，然后使用`removeChild()`方法删除它的一个例子。然而，有两件重要的事情需要理解。

首先，您不能直接从`DOMDocument`的实例中删除一个孩子。你必须进入`documentElement`并把孩子从那里带走。这和你在图书馆增加一本书时必须参考`documentElement`的原因是一样的。

其次，从文档中删除元素只是将它从内存中删除。如果您想要保存数据，您应该将它保存回文件中。

下面是`deleteBook()`方法的样子:

```
<?php
public function deleteBook($isbn) { 
    // get the book element based on its ID 
    $book = $this->domDocument->getElementById($isbn); 

    // simply remove the child from the documents
    // documentElement 
    $this->domDocument->documentElement->removeChild($book);

    // save back to disk 
    $this->domDocument->save($this->xmlPath);
}
```

## 按流派查找书籍

基于流派查找特定书籍的方法使用 XPath 来获得我们需要的结果。正如您之前看到的，当我们在 DTD 中声明了一个 ID 时，这是一种从 DOM 中挑选项目的便捷方式。但是，如果我们需要查询 XML 中的其他数据，我们该怎么办呢？我们可以使用一个`DOMXPath`对象。XPath 本身超出了本文的范围，但是我建议您查看一些解释语法的参考资料。在 XML 中查找任何具有特定类型流派的图书条目的 XPath 查询是:

```
//library/book/genre[text() = "some genre"]/..
```

这个查询首先告诉我们想要访问路径`//library/book`中的`genre`元素。两个正斜杠表示`library`是根元素，单斜杠表示`book`是`library`的子元素`genre`是`book`的子元素。`[text() = "some genre"]`表示我们正在寻找一个其中的文本是“某种类型”的。就其本身而言，结果将只是流派元素，这就是为什么`/..`被标记在末尾以表明我们实际上需要`genre`的父元素。

XPath 是在结构中定位节点的好方法。如果您发现自己在迭代几个`DOMNodeLists`并测试某些值的`nodeValues`,那么您最好看看一个等价的 XPath 查询，它肯定会更短、更快、更易读。

这是搜索方法的样子:

```
<?php
public function findBooksByGenre($genre) 
{ 
    // use XPath to find the book we"re looking for 
    $query = '//library/book/genre[text() = "' . $genre . '"]/..';

    // create a new XPath object and associate it with the document we want to query against 
    $xpath = new DOMXPath($this->domDocument); 
    $result = $xpath->query($query); 

    $arrBooks = array(); 

    // iterate of the results 
    foreach($result as $book)  {
        // add the title of the book to an array 
        $arrBooks[] = $book->getElementsByTagName("title")->item(0)->nodeValue; 
    } 

    return $arrBooks; 
}
```

## 摘要

本文只是向您展示如何使用 DOM 操作和报告 XML 数据的一个尝试。PHP DOM 并不像看起来那么可怕，在某些情况下，您可能会发现它比 SimpleXML 更受欢迎。

您学到的最重要的东西之一是节点的概念，就 DOM 而言，它是 XML 文档的基本构建块。您看到了如何将 XML 文档加载到内存中并验证它，如何使用`getElementById()`和`getElementsByTagName()`从 XML 文档中提取数据，如何添加和删除元素，如何处理属性，以及如何查看`DOMNodeList`和`DOMNamedNodeMap`的集合来提取数据集合。

虽然您今天看到的许多事情可能已经可以在 SimpleXML 中轻松完成，但我希望本文向您展示了如何使用 DOM 实现同样的事情，以及 DOM 的一些好处。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章