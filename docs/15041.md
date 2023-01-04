# XML DTDs 与 XML 模式

> 原文：<https://www.sitepoint.com/xml-dtds-xml-schema/>

XML 是一种非常方便的格式，可以以独立于平台的方式在不同的系统之间存储和交流数据。XML 不仅仅是计算机的一种格式——其创建的指导原则是它应该是人类可读的并且易于创建。

XML 允许用 C 语言编写的 UNIX 系统与 Web 服务通信，例如，运行在微软。NET 架构，并且是在 ASP.NET 写的。然而，XML 只是系统理解的元语言——它们都需要对 XML 数据的格式达成一致。通常，流程中的一个合作伙伴会向另一个提供服务:一个负责数据的格式。

该定义有两个目的:第一个是确保通过解析阶段的数据至少具有正确的结构。因此，这是可以拒绝“垃圾”输入的第一级。其次，定义以标准、正式的方式记录了协议，这使得开发人员更容易理解什么是可用的。

##### DTD–文档类型定义

用于提供该定义的第一种方法是 DTD，即文档类型定义。它定义了文档中可能包含的元素、这些元素的属性以及元素的排序和嵌套。

DTD 是在 XML 文档中包含的 XML 声明下的 DOCTYPE 声明中声明的:

内嵌定义:

```
<?xml version="1.0"?> 

<!DOCTYPE documentelement [definition]>
```

外部定义:

```
<?xml version="1.0"?> 

<!DOCTYPE documentelement SYSTEM "documentelement.dtd">
```

DTD 本身的实际主体包含元素及其属性方面的定义。例如，下面的简短 DTD 定义了一个书店。它说明书店有一个名字，并且至少有一个主题的书。

每个主题都有一个名称和 0 或更多的库存书籍。每本书都有书名、作者和 ISBN 号。主题的名称和书店的名称被定义为相同类型的元素:这个商店的`PCDATA:`只是文本数据。书名和作者被存储为文本数据，XML 解析器不会对其进行进一步的字符解析。ISBN 号存储为图书的一个属性:

```
<!DOCTYPE bookstore [ 

  <!ELEMENT bookstore (topic+)> 

  <!ELEMENT topic (name,book*)> 

  <!ELEMENT name (#PCDATA)> 

  <!ELEMENT book (title,author)> 

  <!ELEMENT title (#CDATA)> 

  <!ELEMENT author (#CDATA)> 

  <!ELEMENT isbn (#PCDATA)> 

  <!ATTLIST book isbn CDATA "0"> 

  ]>
```

书店内联定义的一个例子可能是:

```
<?xml version="1.0"?> 

<!DOCTYPE bookstore [ 

  <!ELEMENT bookstore (name,topic+)> 

  <!ELEMENT topic (name,book*)> 

  <!ELEMENT name (#PCDATA)> 

  <!ELEMENT book (title,author)> 

  <!ELEMENT title (#CDATA)> 

  <!ELEMENT author (#CDATA)> 

  <!ELEMENT isbn (#PCDATA)> 

  <!ATTLIST book isbn CDATA "0"> 

  ]> 

<bookstore> 

  <name>Mike's Store</name> 

  <topic> 

    <name>XML</name> 

    <book isbn="123-456-789"> 

      <title>Mike's Guide To DTD's and XML Schemas<</title> 

      <author>Mike Jervis</author> 

    </book> 

  </topic> 

</bookstore>
```

当您只有几个文档并且它们处于脱机状态时，使用内联定义会很方便，因为定义总是在文件中。但是，例如，如果您的 DTD 定义了用于在两个独立系统之间进行对话的 XML 协议，那么与每个文档一起重新传输 DTD 会增加通信开销。使用外部 DTD 消除了每次重新发送的需要。我们可以从文档中删除 DTD，并将它放在 Web 服务器上的 DTD 文件中，这两个系统都可以访问该文件:

```
<?xml version="1.0"?> 

<!DOCTYPE bookstore SYSTEM "http://webserver/bookstore.dtd"> 

<bookstore> 

  <name>Mike's Store</name> 

  <topic> 

    <name>XML</name> 

    <book isbn="123-456-789"> 

      <title>Mike's Guide To DTD's and XML Schemas<</title> 

      <author>Mike Jervis</author> 

    </book> 

  </topic> 

</bookstore>
```

bookstore.dtd 文件将包含纯文本文件中的完整定义:

```
 <!ELEMENT bookstore (name,topic+)> 

  <!ELEMENT topic (name,book*)> 

  <!ELEMENT name (#PCDATA)> 

  <!ELEMENT book (title,author)> 

  <!ELEMENT title (#CDATA)> 

  <!ELEMENT author (#CDATA)> 

  <!ELEMENT isbn (#PCDATA)> 

  <!ATTLIST book isbn CDATA "0">
```

DTD 中最低级别的定义是某样东西是`CDATA`或`PCDATA:`字符数据，或者是经过解析的字符数据。我们只能将一个元素定义为文本，有了这个限制，就不可能强制一个元素为数字。可以将属性强制为一系列定义的值，但不能强制为数字。

例如，如果您将应用程序设置存储在一个 XML 文件中，可以手动编辑该文件，使窗口起始坐标为字符串——您仍然需要在代码中验证这一点，而不是让解析器来为您验证。

##### XML 模式

XML 模式提供了一种更强大的方法来定义 XML 文档结构和限制。XML 模式本身就是 XML 文档。它们引用 XML 模式名称空间(这里详细描述为)，甚至有自己的 [DTD](http://www.w3c.org/2001/XMLSchema.dtd) 。

XML 模式提供了一种面向对象的方法来定义 XML 文档的格式。XML 模式提供了一组基本类型。这些类型比 dtd 的基本`PCDATA`和`CDATA`要广泛得多。它们包括最基本的编程类型，如整数、字节、字符串和浮点数，但它们也扩展到互联网数据类型，如 ISO 国家和语言代码(例如 en-GB)。完整的名单可以在[这里](http://www.w3c.org/TR/xmlschema-0/#simpleTypesTable)找到。

然后，XML 模式的作者使用这些核心类型以及各种操作符和修饰符来创建他们自己的复杂类型。然后，这些复杂类型用于定义 XML 文档中的元素。

作为一个简单的例子，让我们试着创建一个基本的 XML 模式来定义我们用作 dtd 例子的书店。首先，我们必须声明这是一个 XSD 文档，因为我们希望它非常用户友好，所以我们将向它添加一些基本文档:

```
<xsd:schema xmlns:xsd="https://www.w3.org/2001/XMLSchema">  

<xsd:annotation>  

  <xsd:documentation xlm:lang="en">  

    XML Schema for a Bookstore as an example.  

  </xsd:documentation>  

</xsd:annotation>
```

在前面的例子中，书店由一个名字和至少一个主题组成。我们可以在 XML 模式中轻松做到这一点:

```
<xsd:element name="bookstore" type="bookstoreType"/>  

<xsd:complexType name="bookstoreType">  

  <xsd:sequence>  

    <xsd:element name="name" type="xsd:string"/>  

    <xsd:element name="topic" type="topicType" minOccurs="1"/>  

  </xsd:sequence>  

</xsd:complexType>
```

在这个例子中，我们定义了一个元素`bookstore`，它等同于我们文档中的一个 XML 元素。我们将它定义为类型`bookstoreType`，这不是一个标准类型，所以我们接下来提供该类型的定义。

然后我们定义一个`complexType`，它将`bookstoreType`定义为一系列名称和主题元素。我们的“`name" type`是一个`xsd:string`，一个由 XML Schema 名称空间定义的类型，所以我们已经完全定义了那个元素。

然而，主题元素属于类型`topicType`，这是我们必须定义的另一个定制类型。我们还用`minOccurs="1",`定义了我们的主题元素，这意味着任何时候都必须至少有一个元素。由于`maxOccurs`没有定义，所以可能包含的元素数量没有上限。如果我们两者都没有指定，缺省值就是一个实例，就像在`name`元素中使用的那样。接下来，我们为`topicType`定义模式。

```
<xsd:complexType name="topicType">  

  <xsd:element name="name" type="xsd:string"/>  

  <xsd:element name="book" type="bookType" minOccurs="0"/>  

</xsd:complexType>
```

这与`bookstoreType`的声明非常相似，但是注意我们必须在这个类型的范围内重新定义我们的 name 元素。如果我们使用了一个复杂类型作为名称，比如`nameType`，它只定义了一个`xsd:string`——并且在我们的类型之外定义了它，我们可以在两个类型中重用它。然而，为了说明这一点，我决定在每个部分中定义它。当我们开始定义我们的`bookType`时，XML 变得有趣起来:

```
<xsd:complexType name="bookType">  

  <xsd:element name="title" type="xsd:string"/>  

  <xsd:element name="author" type="xsd:string"/>  

  <xsd:attribute name="isbn" type="isbnType"/>  

</xsd:complexType>  

<xsd:simpleType name="isbnType">  

  <xsd:restriction base="xsd:string">  

    <xsd:pattern value="[0-9]{3}[-][0-9]{3}[-][0-9]{3}"/>  

  </xsd:restriction>  

</xsd:simpleType>
```

所以`bookType`的定义并不是特别有趣。但其属性“`isbn`”的定义是。XML Schema 不仅支持使用诸如`xsd:nonNegativeNumber`这样的类型，而且我们还可以使用各种修饰符从这些基本类型中创建自己的简单类型。在上面的`isbnType`的例子中，我们基于一个字符串，并限制它匹配一个给定的正则表达式。原谅我糟糕的正则表达式，这应该限制任何 isbn 属性，以匹配由破折号分隔的三组三位数的标准。

这只是一个简单的例子，但是它可以让您体验到控制属性或元素内容的许多方法。使用模式可以更好地控制什么是有效的 XML 文档。你甚至可以

*   从您创建的其他类型中扩展您的类型，
*   要求范围内的唯一性，以及
*   提供查找。

这是一种非常好的面向对象的方法。您可以构建一个复杂类型和简单类型的库，以便在许多项目中重用，甚至可以从互联网上找到常见类型的其他定义(例如“地址”)，并使用这些定义来提供 XML 文档的强大定义。

##### DTD 与 XML 模式

DTD 根据包含文档形状的元数据提供了定义 XML 文档的基本语法。XML 模式提供了这一点，以及定义数据可以包含什么和不可以包含什么的详细方法。它为开发人员提供了对合法内容的更多控制，并且提供了面向对象的方法，以及由此带来的所有好处。

因此，如果 XML 模式提供了一种面向对象的方法来定义 XML 文档的结构，如果 XML 模式使我们能够定义可重用的类型，比如基于各种预定义类型的 ISBN 号，那么我们为什么要使用 DTD 呢？事实上，使用 DTD 代替模式有几个很好的理由。

首先，也是非常重要的一点，XML 模式是一项新技术。这意味着尽管一些 XML 解析器完全支持它，但许多仍然不支持。如果您使用 XML 与遗留系统通信，那么它可能不支持 XML 模式。

许多系统接口已经被定义为 DTD。它们是成熟的定义，丰富而复杂。重写定义的努力可能不值得。

DTD 也建立起来了，DTD 中定义的公共对象的例子在互联网上比比皆是——可以免费重用。开发人员可以使用它们来定义 DTD，这比他们完成核心元素作为新模式的完全重新开发要快得多。

最后，您还必须考虑 XML 模式是一个 XML 文档的事实。它有一个可引用的 XML 名称空间和一个定义它的 XML DTD。这都是开销。当一个解析器检查文档时，它可能必须链接所有的内容，解释模式的 DTD，加载名称空间，验证模式，等等。，所有在之前的*都可以解析实际的 XML 文档。如果您使用 XML 作为两个频繁使用的系统之间的协议，并且需要快速响应，那么这种开销可能会严重降低性能。*

同样，如果您的系统可以作为 Web 服务供第三方开发人员使用，那么 XML 模式的详细实施可能会更有效地保护您的应用程序免受恶意(或者仅仅是恶意)XML 数据包的攻击。举个例子，Muse.net 是一项有趣的技术。他们有一个用 XML 模式定义的公开可用的 SOAP API，这为他们的开发人员提供了对他们从用户社区获得的内容的更多控制。

另一方面，我最近参与设计了一个系统来处理来自多个设备的输入事务。为了扩展系统，所选择的处理请求的服务是 SOAP 服务器。然而，这个系统是完全封闭的，服务器上的一个简单的 DTD 足以确保从客户机发送的包完整无损地到达，而不需要 XML Schema 的额外开销。

## 分享这篇文章