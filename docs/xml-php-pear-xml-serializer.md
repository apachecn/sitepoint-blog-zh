# 带有 PHP 和 PEAR::XML_Serializer 的即时 XML

> 原文：<https://www.sitepoint.com/xml-php-pear-xml-serializer/>

最近，XML 已经成为软件开发几乎所有领域的一部分——尤其是在 Web 上。那些使用普通 [XML](https://www.sitepoint.com/books/xml1/) 应用程序的人，比如 RSS 和 XML-RPC，可能会找到专门帮助他们处理格式的公共领域库，消除了重新发明轮子的需要。

但是对于“特别的”XML 文档，您可能只能靠自己，并且很可能会花费宝贵的时间构建代码来解析它。您可能还会发现自己需要将数据公开为 XML，以便其他一些系统或应用程序可以使用它，尽管 XML 最终只是文本，但生成符合 XML 格式良好规则的文档可能比看起来更复杂。输入: [PEAR::XML_Serializer](http://pear.php.net/XML_Serializer) ，XML 的“瑞士军刀”。

如果你和 SitePoint 保持联系，在阅读[PEAR](https://www.sitepoint.com/article/getting-started-with-pear)入门时，你已经体验过 PEAR::XML_Serializer 了。在本文中，我将深入研究 XML_Serializer，并向您展示它如何使 XML 的使用变得轻而易举。如果你对 XML 总体上有任何疑问，可以试试《XML 简介》。

当今的标签层次结构:

*   简介:PEAR::XML_Serializer 做什么以及如何安装它
*   XML_Serializer API:带有简单示例的序列化类概述
*   XML_Unserializer API:包含更多示例的 unserialization 类概述
*   管理配置信息:PEAR::XML_Serializer 应用于管理 XML 配置文件
*   使用 PEAR::XML_Serializer 的 Web 服务:系统到系统的数据交换

注意，本文使用的 PEAR::XML_Serializer 版本是 0.91。为了方便起见，我将这些例子中的所有代码都保存到了一个档案中，您可以从这里下载。

##### 介绍

PEAR::XML_Serializer 是德国最多产的 PHP 开发人员之一斯特凡·施密特辛勤工作的成果。如果你曾经查看过 [PHP 应用工具(PAT)](http://www.php-tools.net/site.php?file=/about/authors.xml) (如在 patTemplate、patUser 和许多其他工具中)，你很有可能已经遇到了 Stephan 从事的 PHP 项目。事实上，如果你看看[的出版物](http://www.php-tools.net/site.php?file=/about/publications.xml)和[的展示](http://www.php-tools.net/site.php?file=/about/presentations.xml)，你可能会发现自己怀疑斯蒂芬是否设法克隆了自己。

PEAR::XML_Serializer 的工作原理是 XML 可以表示为原生 PHP 类型(变量)。换句话说，您可以在 PHP 中构建一些数组，将其传递给 XML_Serializer，它会返回一个表示该数组的 XML 文档。它还能够进行相反的转换——给它一个 XML 文档，它可以帮你解序列化，返回一个表示文档的 PHP 数据结构。

幕后的魔术是 PHP 的反射函数，比如 [is_array()](http://www.php.net/is_array) 、 [get_class()](http://www.php.net/get_class) 、 [get_object_vars()](http://www.php.net/get_object_vars) 。根据[维基百科](http://en.wikipedia.org/wiki/Reflection_(computer_science))，“反射是程序在运行时检查并可能修改其高层结构的能力”。

这意味着，给定一些任意的 PHP 数据结构，PEAR::XML_Serializer 可以很好地将其转换成有用的 XML 表示，反之亦然。当然，这是基于“猜测”,您可能会发现最终的转换并不总是您所期望的那样。为了给你更多的控制，PEAR::XML_Serializer 有许多影响它如何进行转换的运行时选项。我将查看类 API，并马上总结可用的选项。

您可能要应用 PEAR::XML_Serializer 的一些问题包括用 XML 文档(config.xml)管理应用程序配置、构建基于 REST 的 Web 服务、用 XML 存储数据供应用程序以后调用、一般的系统到系统数据交换，以及几乎所有您需要在短时间内完成的“快速而繁琐”的解析。

您可能希望避免使用 PEAR::XML_Serializer 的地方是在解析大型 XML 文档(以兆字节为单位)时，或者在处理复杂的、可能是任意的 XML 文档(比如 XHTML)时。像 DOM API 一样，XML_Serializer 解析整个 XML 文档，并在内存中根据它构建一个 PHP 数据结构。大型文档可能会导致达到 PHP 的 memory_limit(参见 php.ini ),并且像循环遍历数据结构这样的操作会很昂贵。在这种情况下，PHP 的原生 SAX 解析器通常是更好的选择，它允许您处理小“块”并控制内存使用。

同时，对于像 XHTML 这样的文档，API PEAR::XML_Serializer 过于简单，无法提供您所需要的细粒度控制。一旦你熟悉了如何使用它，试着去序列化 SitePoint 的主页，或者通过序列化一个 PHP 数据结构来生成 XHTML，你会很快明白我的意思。DOM 通常是操作 XML 的更好选择。

要使用 PEAR::XML_Serializer，您还需要安装[PEAR::XML _ Parser](http://pear.php.net/XML_Parser)(PHP 的 SAX 扩展的包装器)和 [PEAR::XML_Util](http://pear.php.net/XML_Util) (提供了许多使用 XML 的简便方法)。XML_Parser 通常与 PEAR 一起安装，但是，假设您两者都没有，从命令行键入以下命令来安装所有内容:

```
$ pear install XML_Parser 

$ pear install XML_Util 

$ pear install XML_Serializer
```

当然，这是假设你已经安装了 PEAR——参见[PEAR 入门](https://www.sitepoint.com/article/getting-started-with-pear)获取安装 PEAR 的说明。

PEAR::XML_Serializer 提供了两个带有类`XML_Serializer`和`XML_Unserializer`的 API。第一个是 XML_Serializer，用于将 PHP 数据结构转换为 XML，而 XML_Unserializer 执行相反的操作，将 XML 转换为 PHP 数据结构。在这两种情况下，只有少数公共类方法被公开，使得简单的转换成为快速编码。对类行为的进一步控制需要设置“选项”，通常是通过将一个关联的 PHP 数组传递给正在使用的类的构造函数。我不得不承认，我不太喜欢用这种方式处理配置，正如我之前在[这里](https://www.sitepoint.com/blog/)的博客中所写的，PEAR::XML_Serializer 可能证明了这一点；找到受支持的选项需要搜索源代码(为了让您的工作变得简单，一个完整的列表即将出现)。不管怎样，除了抱怨，PEAR::XML_Serializer 仍然是处理 XML 的优秀工具。

##### XML_Serializer API

我将从用于将 PHP 数据结构转换成 XML 的`XML_Serializer class`开始，首先总结 API，然后用一些基本的例子来说明。为了描述 API，我将使用 PHP 手册中常见的函数签名概念:

```
return_type function_name(type param_name, [type optional_param_name])
```

`XML_Serializer`类中可用的主要公共方法有:

*   `object XML_Serializer`(【数组选项】)
    构造器接受一个可选的选项数组(见下文)。
*   `mixed serialize`(混合数据，[数组选项])
    给这个方法传递一个 PHP 数据结构，它执行序列化成 XML。如果成功，返回值为 TRUE 如果遇到问题，返回值为 PEAR Error 对象。其他选项也可以作为第二个参数传递(见下文)。
*   `mixed getSerializedData()`
    这个方法以字符串的形式返回序列化的 XML 文档，如果没有可用的序列化 XML，则以 PEAR error 对象的形式返回。
*   `void setOption`(字符串名，混合值)
    该方法设置一个单独的选项。
*   `void resetOptions()`
    使用此方法将所有选项重置为默认状态。

XML_Serializer 可用的*选项*有:

*   `addDecl`(默认为假):
    是否增加期初 XML 处理指令，`<?xml version="1.0"?>`
*   `encoding`(默认= " ":
    将添加到开始 XML 声明中的 XML 字符编码，例如`<?xml version="1.0" encoding="ISO-8859-1"?>`
*   `addDoctype`(默认值= FALSE):
    是否在文档中添加 DOCTYPE 声明
*   `doctype`(默认值= null):
    指定要在 DOCTYPE 声明中使用的 URIs(参见下面的示例)
*   `indent`(default = " ":
    用于缩进 XML 标签的字符串，使其对人眼更友好
*   `linebreak` (default = "n"):
    也用于格式化，该字符被插入到每个开始和结束标记之后
*   `indentAttributes`(默认= FALSE):
    用于缩进生成的 XML 标签的属性的字符串。如果设置为特殊值“_auto”，它会将同一列下的所有属性排成一行，在每个属性之间插入一个换行符。
*   `defaultTagName`(default = " XML _ Serializer _ Tag "):
    用于序列化索引数组中的值的标记名
*   `mode` (default = "default"):
    如果设置为' simplexml '，索引数组的元素将放在与其父元素同名的标记中。以下是更多相关信息。
*   `rootName`(默认= " ":
    要分配给 XML 文档的根标签的标签。如果没有指定，PHP 数据结构中根元素的类型将被用作根名称(例如“数组”)。
*   `rootAttributes` (default = array()):
    要转换为根标签属性的值的关联数组，关键字成为属性名。使用时要小心，因为您有责任确保键和值是合法的 XML 属性。
*   `scalarAsAttributes` (default = FALSE):
    对于关联数组，如果值是标量类型(如字符串、整数)，它们将作为属性分配给它们的父节点，使用数组键作为属性名。
*   `prependAttributes`(默认= " ":
    一个字符串，要附加到任何生成的标记属性的名称之前。
*   `typeHints` (default = FALSE):
    确定一个标记所表示的 PHP 值的原始变量类型是否应该作为属性存储在序列化的 XML 文档中。下面是一个例子。
*   `typeAttribute` (default = "_type"):
    如果使用了 typeHints，类型将使用具有该选项名称的属性存储在 XML 标记中。如果您有一个像`$myVariable = 'Hello World!'`这样的 PHP 变量，如果使用了 typeHints，默认的序列化 XML 表示将是`<myVariable _type="string">Hello World!</myVariable>`。
*   `keyAttribute`(default = " _ original key "):
    用于存储索引数组元素的原始键的属性。仅在 typeHints 打开时使用。
*   `classAttribute` (default = "_class"):
    序列化对象时(启用 typeHints)，该属性将用于存储创建该对象的类的名称。

还有一个特殊的选择。将选项传递给`serialize()`方法时使用“overrideOptions”。如果赋值为' TRUE ',传递给构造函数的选项将被忽略，取而代之的是默认选项值和传递给`serialize()`方法的任何其他选项。

用 XML_Serializer 序列化 PHP 数据结构的一个简单示例如下:

```
<?php 

// Set error reporting to ignore notices 

error_reporting(E_ALL ^ E_NOTICE); 

// Include XML_Serializer 

require_once 'XML/Serializer.php'; 

// Some data to transform 

$palette = array('red', 'green', 'blue'); 

// An array of serializer options 

$serializer_options = array ( 

    'addDecl' => TRUE, 

    'encoding' => 'ISO-8859-1', 

    'indent' => '  ', 

    'rootName' => 'palette', 

    'defaultTagName' => 'color', 

); 

// Instantiate the serializer with the options 

$Serializer = &new XML_Serializer($serializer_options); 

// Serialize the data structure 

$status = $Serializer->serialize($palette); 

// Check whether serialization worked 

if (PEAR::isError($status)) { 

    die($status->getMessage()); 

} 

// Display the XML document 

header('Content-type: text/xml'); 

echo $Serializer->getSerializedData(); 

?>
```

文件名:palette1.php

您可以在这里看到这些选项通常是如何使用的。我需要构建一个数组`$serializer_options`，并将其传递给 XML_Serializer 的构造函数。

请注意，如果您通常在打开完整错误报告的情况下工作，则需要更改错误报告。当前版本的 PEAR::XML_Serializer 抛出类似“数组到字符串转换”的 PHP 错误通知，都不严重，但会导致错误通知消息。

生成的 XML 如下所示:

```
<?xml version="1.0" encoding="ISO-8859-1"?> 

<palette> 

  <color>red</color> 

  <color>green</color> 

  <color>blue</color> 

</palette>
```

因为数据结构是一个索引数组，所以我使用了`'defaultTagName'`选项为代表数组元素的标签命名。

现在，让我们用一个关联数组来代替:

```
<?php 

// Set error reporting to ignore notices 

error_reporting(E_ALL ^ E_NOTICE); 

// Include XML_Serializer 

require_once 'XML/Serializer.php'; 

// Some data to transform 

$palette = array( 

    'red' => 45, 

    'green' => 240, 

    'blue' => 120 

    ); 

// An array of serializer options 

$serializer_options = array ( 

    'addDecl' => TRUE, 

    'encoding' => 'ISO-8859-1', 

    'indent' => '  ', 

    'rootName' => 'palette', 

); 

// Instantiate the serializer with the options 

$Serializer = &new XML_Serializer($serializer_options); 

// Serialize the data structure 

$status = $Serializer->serialize($palette); 

// Check whether serialization worked 

if (PEAR::isError($status)) { 

    die($status->getMessage()); 

} 

// Display the XML document 

header('Content-type: text/xml'); 

echo $Serializer->getSerializedData(); 

?>
```

文件名:palette2.php

生成的 XML 如下所示:

```
<?xml version="1.0" encoding="ISO-8859-1"?>  

<palette>  

  <red>45</red>  

  <green>240</green>  

  <blue>120</blue>  

</palette>
```

注意，标签名现在对应于`$palette`变量的键。如果我再次使用这个示例，添加`'scalarAsAttributes'`选项(参见示例文件 palette3.php)，下面是我得到的 XML:

```
<?xml version="1.0" encoding="ISO-8859-1"?>  

<palette blue="120" green="240" red="45" />
```

标量整数值现在成为根标记的属性，而不是表示为单独的标记。

另一个示例显示了当您序列化对象并利用“typeHints”选项时会发生什么:

```
<?php  

// Set error reporting to ignore notices  

error_reporting(E_ALL ^ E_NOTICE);  

// Include XML_Serializer  

require_once 'XML/Serializer.php';  

// A class to store color information  

class ColorInformation {  

    var $hue;  

    var $value;  

    function ColorInformation($hue = NULL, $value = NULL) {  

        $this->hue = $hue;  

        $this->value = $value;  

    }  

}  

// Some data to transform  

$palette = array();  

$palette[] = &new ColorInformation('red', 45);  

$palette[] = &new ColorInformation('green', 240);  

$palette[] = &new ColorInformation('blue', 120);  

// An array of serializer options  

$serializer_options = array (  

    'addDecl' => TRUE,  

    'encoding' => 'ISO-8859-1',  

    'indent' => '  ',  

    'indentAttributes' => '_auto',  

    'rootName'  => 'palette',  

    'defaultTagName' => 'color',  

    'typeHints' => TRUE,  

);  

// Instantiate the serializer with the options  

$Serializer = &new XML_Serializer($serializer_options);  

// Serialize the data structure  

$status = $Serializer->serialize($palette);  

// Check whether serialization worked  

if (PEAR::isError($status)) {  

    die($status->getMessage());  

}  

// Display the XML document  

header('Content-type: text/xml');  

echo $Serializer->getSerializedData();  

?>
```

文件名:palette4.php

相应的 XML 如下所示:

```
<?xml version="1.0" encoding="ISO-8859-1"?>  

<palette _type="array">  

  <color _class="colorinformation"  

         _originalKey="0"  

         _type="object">  

    <hue _type="string">red</hue>  

    <value _type="integer">45</value>  

  </color>  

  <color _class="colorinformation"  

         _originalKey="1"  

         _type="object">  

    <hue _type="string">green</hue>  

    <value _type="integer">240</value>  

  </color>  

  <color _class="colorinformation"  

         _originalKey="2"  

         _type="object">  

    <hue _type="string">blue</hue>  

    <value _type="integer">120</value>  

  </color>  

</palette>
```

打开`'typeHints'`后，添加了描述原始数据结构的一些细节属性，`'_type'`表示原始 PHP 变量类型，`'_class'`存储任何序列化对象的类名，`'_originalKey'`是在其中找到该元素的索引数组的键(如果适用)。

当您需要确保当您取消文档序列化时，您可以准确地返回到开始时的状态时,`'typeHints'`功能非常有用。如果您使用 PEAR::XML_Serializer 来存储您的代码稍后将检索的持久数据，您可能希望使用`'typeHints'`。注意，当您需要使用 typeHints 精确表示 PHP 数据结构时，最好避免使用`'scalarAsAttributes'`选项，这会丢失关于标量类型的信息。

最后，下面的示例显示了如何添加 DOCTYPE 声明来呈现 XHTML:

```
<?php  

// Set error reporting to ignore notices  

error_reporting(E_ALL ^ E_NOTICE);  

// Include XML_Serializer  

require_once 'XML/Serializer.php';  

// PHP data structure representing an XHTML document  

$xhtml = array  

    (  

        'head' => array (  

            'title' => 'XHTML with XML_Serializer',  

        ),  

        'body' => array (  

            'h1' => 'XHTML with XML_Serializer',  

            'p' => 'It's possible but not recommended',  

        ),  

    );  

// XML_Serializer options  

$serializer_options = array (  

    'addDecl' => TRUE,  

    'encoding' => 'ISO-8859-1',  

    'indent' => '  ',  

    'rootName' => 'html',  

    'rootAttributes' => array (  

        'xmlns' => 'https://www.w3.org/1999/xhtml',  

        'lang' => 'en',  

        'xml:lang' => 'en'  

        ),  

    'addDoctype' => TRUE,  

    'doctype' => array (  

        'id' => '-//W3C//DTD XHTML 1.0 Strict//EN',  

        'uri' => 'https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd'  

    ),  

);  

// Create the serializer  

$Serializer = &new XML_Serializer($serializer_options);  

// Serialize the XHTML  

$status = $Serializer->serialize($xhtml);  

// Check for errors  

if (PEAR::isError($status)) {  

    die($status->getMessage());  

}  

// Send the right HTTP header  

// See http://www.juicystudio.com/tutorial/xhtml/mime.asp for more info  

if (stristr($_SERVER[HTTP_ACCEPT], 'application/xhtml+xml')) {  

    header('Content-Type: application/xhtml+xml; charset=ISO-8859-1');  

} else {  

    header('Content-Type: text/html; charset=ISO-8859-1');  

}  

// Display the XML document  

echo $Serializer->getSerializedData();  

?>
```

Filename: xhtml.php

注意`'doctype'`选项。这里定义的数组实际上是由 PEAR::XML_Util 在其`getDocTypeDeclaration()`方法中确定的。还要注意，我使用了`'rootAttributes'`选项向根 html 标签添加属性。下面是生成的 XHTML:

```
<?xml version="1.0" encoding="ISO-8859-1"?>  

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"  

    "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">  

<html lang="en" xml:lang="en" >  

  <head>  

    <title>XHTML with XML_Serializer</title>  

  </head>  

  <body>  

    <h1>XHTML with XML_Serializer</h1>  

    <p>It's possible but not recommended</p>  

  </body>  

</html>
```

这个例子纯粹是为了展示如何使用 DOCTYPE 声明。实际上，我不建议您用 XML_Serializer 生成 XHTML，因为 API 不能为您提供对转换的细粒度控制，并且您可能会花费大量时间摆弄庞大的 PHP 数组，而且通常会掉毛。

##### XML_Unserializer API

您已经看到了 XML_Serializer API 的要点；用于将 XML 转换为 PHP 数据结构的 XML_Unserializer，本质上就是它的“镜像”。

主要的公共方法有:

*   `object XML_Unserializer`(【数组选项】)
    这个构造函数接受一个可选的选项数组(如下所述)。
*   `mixed unserialize`(混合数据、【布尔 isFile】、【数组选项】)
    第一个参数 data 可以是包含 XML 文档的字符串、包含 XML 的文件的路径(在这种情况下，第二个参数 isFile 必须设置为 true)或实现 [steams API](http://www.php.net/streams) 的 PHP 资源，比如您已经打开的文件或 [PEAR 的流包装器](http://pear.php.net/packages.php?catpid=35&catname=Streams)之一的实例。第三个可选参数 options 是一个选项数组，如下所述。从`unserialize()`返回的值要么是 TRUE，要么是 PEAR Error 对象。
*   `mixed getUnserializedData()`
    这会返回 XML 文档表示的 PHP 数据结构或 PEAR 错误对象。
*   `mixed getRootName()`
    该方法返回根元素的名称(通常在返回的数据结构中不可见`from getUnserializedData()`)或一个 PEAR 错误对象。
*   `void setOption`(字符串名称，混合值)
    使用此选项设置单个选项。
*   `void resetOptions()`
    该方法将所有选项重置为默认状态。

可用于 XML_Unserializer 的选项有:

*   `parseAttributes`(默认= FALSE)
    标签属性是否要变成数组；如果设置为 false，属性将被忽略
*   `attributesArray`(默认值= FALSE)
    用于将数组标识为将要放置的开关属性的名称
*   `prependAttributes` (default = "")
    用于预先设置数组的键，属性将被放置到该数组中
*   `complexType` (default = "array")
    如果找不到 typeHint，复杂类型(包含 CDATA 和其他子标签的标签)将被转换为数组
*   `contentName` (default = "_content")
    对于复杂类型，标签内的 CDATA 将被放入结果数组中
*   `tagMap` (default = array())
    允许你将一个 XML 标签名映射到一个 PHP 类的名称上(见下面的例子)；如果 XML 文档中存在未序列化的 typeHints，则重写 classAttribute 的值
*   `keyAttribute` (default = "_originalKey")
    标识用作索引数组键的 typeHint 的属性(请参见同名的 XML_Serializer 选项)
*   `typeAttribute` (default = "_type")
    标识 PHP 数据类型，用作 typeHint(参见同名的 XML_Serializer 选项)
*   `classAttribute` (default = "_class")
    标识一个 PHP 类的 PHP 名称，该标记表示该类的一个对象(参见同名的 XML_Serializer 选项)

与 XML_Serializer 类一样，可以将“overrideOptions”选项传递给`unserialize()`方法，以忽略已经传递给构造函数的值。

为了提供 XML_Unserializer 的基本说明，我将从上面看到的 XML_Serializer 示例中获取输出，并看看当我们取消序列化它们时会得到什么。

下面是第一个例子的反例:

```
<?php   

// Set error reporting to ignore notices   

error_reporting(E_ALL ^ E_NOTICE);   

// Include XML_Unserializer   

require_once 'XML/Unserializer.php';   

// The XML document   

$doc = <<<EOD   

<?xml version="1.0" encoding="ISO-8859-1"?>   

<palette>   

  <color>red</color>   

  <color>green</color>   

  <color>blue</color>   

</palette>   

EOD;   

// Instantiate the serializer   

$Unserializer = &new XML_Unserializer();   

// Serialize the data structure   

$status = $Unserializer->unserialize($doc);   

// Check whether serialization worked   

if (PEAR::isError($status)) {   

    die($status->getMessage());   

}   

// Display the PHP data structure   

echo '<pre>';   

print_r($Unserializer->getUnserializedData());   

echo '</pre>';   

?>
```

filename:up panel 1 . PHP

产生的数据结构如下所示:

```
Array   

(   

    [color] => Array   

        (   

            [0] => red   

            [1] => green   

            [2] => blue   

        )   

)
```

在这里，您可以看到使用 XML_Serializer 时需要谨慎——原始的 PHP 数据结构如下:

```
$palette = array('red', 'green', 'blue');
```

如果您想确保在处理索引数组时得到的数据结构与开始时完全相同，那么在将它们序列化为 XML 时，您需要打开“typeHints”。

转到第二个示例，代码基本相同:

```
 // Set error reporting to ignore notices   

error_reporting(E_ALL ^ E_NOTICE);   

// Include XML_Unserializer   

require_once 'XML/Unserializer.php';   

// The XML document   

$doc = <<<EOD   

<?xml version="1.0" encoding="ISO-8859-1"?>   

<palette>   

  <red>45</red>   

  <green>240</green>   

  <blue>120</blue>   

</palette>   

EOD;   

// Instantiate the serializer   

$Unserializer = &new XML_Unserializer();   

// Serialize the data structure   

$status = $Unserializer->unserialize($doc);   

// Check whether serialization worked   

if (PEAR::isError($status)) {   

    die($status->getMessage());   

}   

// Display the PHP data structure   

echo '<pre>';   

print_r($Unserializer->getUnserializedData());   

echo '</pre>';   

?>

Filename: upalette2.php
```

产生的 PHP 数据结构现在是:

```
Array   

(   

    [red] => 45   

    [green] => 240   

    [blue] => 120   

)
```

这一次，它与原始数据结构相匹配，原始数据结构也使用了一个关联数组(但请注意，数组的值将是' string '类型，而不是' integer '类型):

```
$palette = array(   

    'red' => 45,   

    'green' => 240,   

    'blue' => 120   

    );
```

第三个示例要求我告诉 unserializer 解析属性:

```
<?php   

// Set error reporting to ignore notices   

error_reporting(E_ALL ^ E_NOTICE);   

// Include XML_Unserializer   

require_once 'XML/Unserializer.php';   

// The XML document   

$doc = <<<EOD   

<?xml version="1.0" encoding="ISO-8859-1"?>   

<palette blue="120" green="240" red="45" />   

EOD;   

// Array of options   

$unserializer_options = array (   

    'parseAttributes' => TRUE   

);   

// Instantiate the serializer   

$Unserializer = &new XML_Unserializer($unserializer_options);   

// Serialize the data structure   

$status = $Unserializer->unserialize($doc);   

// Check whether serialization worked   

if (PEAR::isError($status)) {   

    die($status->getMessage());   

}   

// Display the PHP data structure   

echo '<pre>';   

print_r($Unserializer->getUnserializedData());   

echo '</pre>';   

?>
```

尽管整数现在是字符串，但最终的 PHP 数据结构还是和我开始时差不多:

```
Array   

(   

    [blue] => 120   

    [green] => 240   

    [red] => 45   

)
```

最后，当我解序列化包含类型提示的文档时，我需要确保定义了类 ColorInformation。在最初的示例中，ColorInformation 类使用构造函数将外部变量传递到其字段中。和 PHP 的 [unserialize()](http://www.php.net/unserialize) 函数一样，XML_Unserializer 不提供在取消序列化对象时调用类方法的机制；相反，它直接设置对象的属性(所以，要小心):

```
<?php   

// Set error reporting to ignore notices   

error_reporting(E_ALL ^ E_NOTICE);   

// Include XML_Unserializer   

require_once 'XML/Unserializer.php';   

// A class to store color information   

class ColorInformation {   

    var $hue;   

    var $value;   

    function ColorInformation($hue = NULL, $value = NULL) {   

        $this->hue = $hue;   

        $this->value = $value;   

    }   

}   

// The XML document   

$doc = <<<EOD   

<?xml version="1.0" encoding="ISO-8859-1"?>   

<palette _type="array">   

  <color _class="colorinformation"   

         _originalKey="0"   

         _type="object">   

    <hue _type="string">red</hue>   

    <value _type="integer">45</value>   

  </color>   

  <color _class="colorinformation"   

         _originalKey="1"   

         _type="object">   

    <hue _type="string">green</hue>   

    <value _type="integer">240</value>   

  </color>   

  <color _class="colorinformation"   

         _originalKey="2"   

         _type="object">   

    <hue _type="string">blue</hue>   

    <value _type="integer">120</value>   

  </color>   

</palette>   

EOD;   

// Instantiate the serializer   

$Unserializer = &new XML_Unserializer();   

// Serialize the data structure   

$status = $Unserializer->unserialize($doc);   

// Check whether serialization worked   

if (PEAR::isError($status)) {   

    die($status->getMessage());   

}   

// Display the PHP data structure   

echo '<pre>';   

print_r($Unserializer->getUnserializedData());   

echo '</pre>';   

?>
```

产生的数据结构是:

```
Array   

(   

    [0] => colorinformation Object   

        (   

            [hue] => red   

            [value] => 45   

        )   

    [1] => colorinformation Object   

        (   

            [hue] => green   

            [value] => 240   

        )   

    [2] => colorinformation Object   

        (   

            [hue] => blue   

            [value] => 120   

        )   

)
```

这与原始数据结构相匹配，忽略了构造函数的问题:

```
$palette = array();   

$palette[] = &new ColorInformation('red', 45);   

$palette[] = &new ColorInformation('green', 240);   

$palette[] = &new ColorInformation('blue', 120);
```

**序列化对象的注意事项:**

与 PHP 内置的 [serialize()](http://www.php.net/serialize) 和 [unserialize()](http://www.php.net/unserialize) 函数一样，当 PEAR::XML _ serialize 转换为 XML 并返回时，它将尝试调用这些对象上的`__sleep()`和`__wakeup()`函数，如果它们已被定义的话。这使您有机会通过在这些方法中定义操作来执行操作，例如与数据库连接或断开连接。更多细节参见 PHP 手册中的 [__sleep()和 __wakeup()](http://www.php.net/manual/en/language.oop.magic-functions.php) 。

当对象被 XML_Unserializer 取消序列化时，它首先尝试使用原始类重新构建它们，但是要这样做，您的代码必须确保该类可用。如果它找不到类定义，假设您使用的是 PHP4，它将使用 PHP 内置的 stdClass 定义。据我所知，PHP5 已经放弃了对 stdClass 的支持，所以在这种情况下会发生什么还有待观察。

目前，XML_Serializer 无法表示包含相互引用的对象。软件包附带的待办事项列表表明在不久的将来会提供支持。

##### 管理配置信息

既然您已经对 PEAR::XML_Serializer 提供的东西有了大致的了解，并且已经看到了一些基本的例子，那么是时候用它做一些有用的事情了。

大多数 PHP 应用程序需要某种形式的配置，以使它们能够“理解”使用它们的环境，例如 Web 服务器的域名、管理员的电子邮件地址、数据库连接设置等等。在 PHP 中有许多常见的方法来处理这个问题，从简单地拥有一个包含需要编辑的变量列表的 PHP 脚本，到使用 [parse_ini_file()](http://www.php.net/parse_ini_file) 函数(注意，PHP 解析 ini 文件的速度比它包含和解析等效 PHP 脚本的速度更快)。

XML 做出了另一种选择，它相对来说更易于手动编辑，相对来说更易于解析和生成，并且允许比 ini 文件更复杂的数据结构。不利的一面是，与其他方法相比，从 XML 文件中检索配置数据可能会很慢(虽然 PHP 代码生成的一些技巧可以帮助您解决这个问题，但那是另一回事了)。

撇开性能问题不谈，这里有一种方法使用 PEAR::XML_Serializer 和一个允许您检索和修改配置设置的类。

首先，我定义了两个类:一个用于存储配置数据，另一个用于管理对配置数据的访问:

```
<?php    

/**    

 * The name of file used to store config data    

 */    

define ('CONFIG_FILE', 'config.xml');    

/**    

 * Stores configuration data    

 */    

class Config {    

    /**    

     * Array of configuration options    

     * @var array    

     * @access private    

     */    

    var $options = array();    

    /**    

     * Returns the value of a configuration option, if found    

     * @param string name of option    

     * @return mixed value if found or void if not    

     * @access public    

     */    

    function get($name) {    

        if (isset($this->options[$name])) {    

            return $this->options[$name];    

        }    

    }    

    /**    

     * Sets a configuration option    

     * @param string name of option    

     * @param mixed value of option    

     * @return void    

     * @access public    

     */    

    function set($name, $value) {    

        $this->options[$name] = $value;    

    }    

}
```

`Config`类充当值的简单存储，允许通过`get()`和`set()`方法访问。

```
/**    

 * Provides a gateway to the Config class, managing its serialization    

 */    

class ConfigManager {    

    /**    

     * Returns a singleton instance of Config    

     * @return Config    

     * @access public    

     * @static    

     */    

    function &instance() {    

        static $Config = NULL;    

        if (!$Config) {    

            $Config = ConfigManager::load();    

        }    

        return $Config;    

    }    

    /**    

     * Loads the Config instance from it's XML representation    

     * @return Config    

     * @access private    

     * @static    

     */    

    function load() {    

        error_reporting(E_ALL ^ E_NOTICE);    

        require_once 'XML/Unserializer.php';    

        $Unserializer = &new XML_Unserializer();    

        if (file_exists(CONFIG_FILE)) {    

            $status = $Unserializer->unserialize(CONFIG_FILE, TRUE);    

            if (PEAR::isError($status)) {    

                trigger_error ($status->getMessage(), E_USER_WARNING);    

            }    

            $Config = $Unserializer->getUnserializedData();    

        } else {    

            $Config = new Config();    

        }    

        return $Config;    

    }    

    /**    

     * Stores the Config instance, serializing it to an XML file    

     * @return boolean TRUE on succes    

     * @access public    

     * @static    

     */    

    function store() {    

        error_reporting(E_ALL ^ E_NOTICE);    

        require_once 'XML/Serializer.php';    

        $Config = &ConfigManager::instance();    

        $serializer_options = array (    

            'addDecl' => TRUE,    

            'encoding' => 'ISO-8859-1',    

            'indent' => '  ',    

            'typeHints' => TRUE,    

        );    

        $Serializer = &new XML_Serializer($serializer_options);    

        $status = $Serializer->serialize($Config);    

        $success = FALSE;    

        if (PEAR::isError($status)) {    

            trigger_error($status->getMessage(), E_USER_WARNING);    

        }    

        $data = $Serializer->getSerializedData();    

        if (!$fp = fopen(CONFIG_FILE, 'wb')) {    

            trigger_error('Cannot open ' . CONFIG_FILE);    

        } else {    

            if (!fwrite($fp, $data, strlen($data))){    

                trigger_error(    

                    'Cannot write to ' . CONFIG_FILE, E_USER_WARNING    

                    );    

            } else {    

                $success = TRUE;    

            }    

            fclose($fp);    

        }    

        return $success;    

    }    

}    

?>
```

Filename: configmanager.php

`ConfigManager`类稍微复杂一点。我将在这里解释关键点，但是如果您有任何具体的问题，请随时在本文末尾的讨论中提出。

静态`instance()`方法使用 PHP4 技巧在对象上创建单例实例。不管你是否知道单例设计模式，`instance()`方法允许我从代码中的任何地方获取相同的 Config 实例，只需通过调用`ConfigManager::instance()`，确保发生在 Config 对象上的任何更改都可以从使用它的任何地方获得。

`load()`和`store()`方法处理将配置对象序列化和反序列化为 XML。如果对 Config 对象进行了任何外部更改，那么`load()`方法只能由`instance()`方法调用，而`store()`方法应该在我的应用程序执行结束时调用。注意，我在这些方法中使用了 [Lazy Includes](https://www.sitepoint.com/blog/) 来保持 PHP 引擎需要做的解析量最小。可能存在调用了`load()`而没有调用`store()`的情况，这时使用它的代码只需要检索配置值，而不需要修改它们。在这些情况下，在每个请求中包含`XML_Serializer`类会浪费开销。

现在，使用 [PEAR::HTML_QuickForm](http://pear.php.net/HTML_Quickform) ，我可以构建一个表单来编辑配置文件。这一次，我将跳过对 HTML_QuickForm 的解释(你可以在[PHP 选集](https://www.sitepoint.com/books/phpant1/)的包和教程中找到更多的例子)。通过键入以下命令确保您已经安装了它:

```
$ pear install HTML_Quickform
```

这里使用的 HTML_Quickform 版本是 3.2.2。

表单代码:

```
<?php    

require_once 'configmanager.php';    

require_once 'HTML/QuickForm.php';    

// Fetch the singleton instance of Config    

$Config = &ConfigManager::instance();    

// Build a form with PEAR::HTML_QuickForm    

$Form = new HTML_QuickForm('labels_example', 'post');    

$Form->addElement('text', 'domain', 'Domain');    

$Form->addRule('domain', 'Please enter a domain name', 'required',    

    NULL, 'client');    

$Form->addRule('domain', 'Please enter a valid domain name',    

    'regex', '/^(www.)?.+.(com|net|org)$/', 'client');    

$Form->addElement('text', 'email', 'Email');    

$Form->addRule('email', 'Please enter an email address', 'required',    

    NULL, 'client');    

$Form->addRule('email', 'Please enter a valid email address',    

    'email', NULL, 'client');    

$Form->addElement('text', 'docroot', 'Document Root');    

$Form->addRule('docroot', 'Please enter the document root',    

    'required', NULL, 'client');    

$Form->addRule('docroot', 'Please enter a valid document root',    

    'callback', 'is_dir');    

$Form->addElement('text', 'tmp', 'Tmp Dir');    

$Form->addRule('tmp', 'Please enter the tmp dir', 'required',    

    NULL, 'client');    

$Form->addRule('tmp', 'Please enter a valid tmp dir', 'callback',    

    'is_dir');    

$Form->addElement('text', 'db_host', 'DB Host');    

$Form->addRule('db_host', 'Please enter a value for DB Host', 'required',    

    NULL, 'client');    

$Form->addRule('db_host', 'Please enter a valid value for DB Host',    

    'regex', '/^[a-zA-Z0-9.]+$/', 'client');    

$Form->addElement('text', 'db_user', 'DB User');    

$Form->addRule('db_user', 'Please enter a value for DB User', 'required',    

    NULL, 'client');    

$Form->addRule('db_user', 'Please enter a valid value for DB User',    

    'regex', '/^[a-zA-Z0-9]+$/', 'client');    

$Form->addElement('text', 'db_pass', 'DB Password');    

$Form->addRule('db_pass', 'Please enter a value for DB Password', 'required',    

    NULL, 'client');    

$Form->addRule('db_pass', 'Please enter a valid value for DB Password',    

    'regex', '/^[a-zA-Z0-9]+$/', 'client');    

$Form->addElement('text', 'db_name', 'DB Name');    

$Form->addRule('db_name', 'Please enter a value for DB Name', 'required',    

    NULL, 'client');    

$Form->addRule('db_name', 'Please enter a valid value for DB Name', 'regex',    

    '/^[a-zA-Z0-9]+$/', 'client');    

$Form->addElement('submit', null, 'Update');    

// Initialize $db array as needed    

$db = $Config->get('db');    

if (!is_array($db)) $db = array();    

if (!isset($db['db_host'])) $db['db_host'] = '';    

if (!isset($db['db_user'])) $db['db_user'] = '';    

if (!isset($db['db_pass'])) $db['db_pass'] = '';    

if (!isset($db['db_name'])) $db['db_name'] = '';    

// Set initial form values from Config    

$Form->setDefaults(array(    

    'domain' => $Config->get('domain'),    

    'email' => $Config->get('email'),    

    'docroot' => $Config->get('docroot'),    

    'tmp' => $Config->get('tmp'),    

    'db_host' => $db['db_host'],    

    'db_user' => $db['db_user'],    

    'db_pass' => $db['db_pass'],    

    'db_name' => $db['db_name'],    

    ));    

// If the form is valid update the configuration file    

if ($Form->validate()) {    

    $result = $Form->getSubmitValues();    

    $Config->set('domain',$result['domain']);    

    $Config->set('email',$result['email']);    

    $Config->set('docroot',$result['docroot']);    

    $Config->set('tmp',$result['tmp']);    

    $db['db_host'] = $result['db_host'];    

    $db['db_user'] = $result['db_user'];    

    $db['db_pass'] = $result['db_pass'];    

    $db['db_name'] = $result['db_name'];    

    $Config->set('db', $db);    

    if (ConfigManager::store()) {    

        echo "Config updated successfully";    

    } else {    

        echo "Error updating configuration";    

    }    

} else {    

    echo '<h1>Edit ' . CONFIG_FILE . '</h1>';    

    $Form->display();    

}    

?>
```

Filename: configedit.php

开始时，我从`ConfigManager`获取`Config`的实例，并用它来填充默认的表单值。我需要初始化`$db`数组，以防这是第一次编辑 config.xml 文件(即它还不存在)并且`Config`包含空值。

一旦表单被成功验证提交，我就把值放回到`Config`实例中，然后调用`ConfigManager::store()`用最新的值更新 config.xml 文档。

以下是表单在浏览器中的外观:

![1336_form](img/dd2ae82d6e238b89fea4a2d89c20f46f.png)

存储的 config.xml 文件如下所示:

```
<?xml version="1.0" encoding="ISO-8859-1"?>    

<config _class="config" _type="object">    

  <options _type="array">    

    <domain _type="string">www.sitepoint.com</domain>    

    <email _type="string">info@sitepoint.com</email>    

    <docroot _type="string">/www</docroot>    

    <tmp _type="string">/tmp</tmp>    

    <db _type="array">    

      <db_host _type="string">db.sitepoint.com</db_host>    

      <db_user _type="string">phpclient</db_user>    

      <db_pass _type="string">secret</db_pass>    

      <db_name _type="string">sitepointdb</db_name>    

    </db>    

  </options>    

</config>
```

Filename: config.xml

typehints 的使用对人眼来说有点不友好，但是如果有必要的话，仍然可以手动编辑这个文件。

现在，使用另一个脚本，我可以访问 config.xml 中的值:

```
<?php    

require_once 'configmanager.php';    

// Fetch the singleton instance of Config    

$Config = &ConfigManager::instance();    

?>    

<h1><?php echo CONFIG_FILE; ?></h1>    

<table>    

    <tr>    

        <td>Domain:</td><td><?php echo $Config->get('domain'); ?></td>    

    </tr>    

    <tr>    

        <td>Email:</td><td><?php echo $Config->get('email'); ?></td>    

    </tr>    

    <tr>    

        <td>Docroot:</td><td><?php echo $Config->get('docroot'); ?></td>    

    </tr>    

    <tr>    

        <td>Tmp Dir:</td><td><?php echo $Config->get('tmp'); ?></td>    

    </tr>    

    <?php $db = $Config->get('db'); ?>    

    <tr>    

        <td>DB Host:</td><td><?php echo $db['db_host']; ?></td>    

    </tr>    

    <tr>    

        <td>DB User:</td><td><?php echo $db['db_user']; ?></td>    

    </tr>    

    <tr>    

        <td>DB Pass:</td><td><?php echo $db['db_pass']; ?></td>    

    </tr>    

    <tr>    

        <td>DB Name:</td><td><?php echo $db['db_name']; ?></td>    

    </tr>    

</table>
```

文件名:configview.php

在这里，我只是将它们显示在一个表格中，这样您就可以看到它是如何工作的。因为我可以从代码中的任何地方调用`ConfigManager::instance()`，并接收对`Config`对象的最新引用，所以当我需要配置我的应用程序的行为时，很容易检索存储在 ico
中的值。此外，因为我使用的是`Config`的单例实例，所以取消底层 xml 文档序列化的开销只需要发生一次，即第一次获取`Config`的实例。

在本例中使用 PEAR::XML_Serializer 有助于我避免卷入 XML 的细节，使我能够将精力集中在构建于其上并对应用程序有直接价值的代码上。

##### 带有 PEAR::XML_Serializer 的 Web 服务

PEAR::XML_Serializer 可以证明有价值的另一个领域是系统到系统或应用程序到应用程序的数据交换。像 [PEAR::SOAP](http://pear.php.net/SOAP) 和 [PEAR::XML_RPC](http://pear.php.net/XML_RPC) 这样的包提供了各自 Web 服务协议的实现，但是 SOAP 和 XML-RPC 并不是将数据从 A 移动到 b 的唯一方法

例如，亚马逊为他们的网站提供了通常所说的 REST-ful 接口(关于 REST Web 服务的简短概述，请参见[以 REST 方式构建 Web 服务](http://www.xfront.com/REST-Web-Services.html))。这意味着你只需要一个 URL 就可以访问亚马逊销售的产品的数据。您从类似于 [this](http://rcm.amazon.com/e/cm?t=sitepoint&l=st1&search=php&mode=books&p=102&o=1&f=xml) 的 URL 返回的结果是一个 XML 文档，其中包含您通常会在类似于 [this](http://www.amazon.com/exec/obidos/search-handle-url/ref=pd_kk_sr_1/102-0532764-2294555?index=stripbooks&field-keywords=php) 的页面上找到的包装在 HTML 中的数据。通过将数据公开为 XML，Amazon 使得从远程网站解析并使用自己的 HTML 显示变得非常容易。全部细节可以在 amazon.com/webservices 找到(你需要注册成为一名准会员)。

就 PEAR::XML_Serializer 而言，解析 Amazon 提供的 XML 并将其转换成网页非常容易:

```
<?php     

// Include PEAR::HTTP_Request     

require_once 'HTTP/Request.php';     

// Include PEAR::XML_Unserializer     

require_once 'XML/Unserializer.php';     

// Your Amazon associate ID     

$assoc_id = 'sitepoint';     

// Allow the Amazon book search keyword to be entered via the URL     

if (!isset($_GET['keyword']) ||     

        !preg_match('/^[a-zA-Z]+$/', $_GET['keyword'])) {     

    $_GET['keyword'] = 'php';     

}     

// Build the URL to access the Amazon XML     

$amazon_url = 'http://rcm.amazon.com/e/cm?t=' . $assoc_id .     

              '&l=st1&search=' . $_GET['keyword'] .     

              '&mode=books&p=102&o=1&f=xml';     

// Create the HTTP_Request object, specifying the URL     

$Request = &new HTTP_Request($amazon_url);     

// Set proxy server as necessary     

// $Request->setProxy('proxy.myisp.com', '8080', 'harryf', 'secret');     

// Send the request for the feed to the remote server     

$status = $Request->sendRequest();     

// Check for errors     

if (PEAR::isError($status)) {     

   die("Connection problem: " . $status->toString());     

}     

// Check we got an HTTP 200 status code (if not there's a problem)     

if ($Request->getResponseCode() != '200') {     

   die("Request failed: " . $Request->getResponseCode());     

}     

// Get the XML from Amazon     

$amazon_xml = $Request->getResponseBody();      

// Create an instance of XML_Unserializer     

$Unserializer = new XML_Unserializer();     

// Unserialize the XML     

$status = $Unserializer->unserialize($amazon_xml);     

// Check for errors     

if (PEAR::isError($status)) {     

    die($status->getMessage());     

}     

// Get the PHP data structure from the XML     

$amazon_data = $Unserializer->getUnserializedData();     

?>     

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"     

        "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">     

<html  lang="en" xml:lang="en">     

<head>     

    <title>Amazon Search for <?php echo $_GET['keyword']; ?></title>     

</head>     

<body>     

    <h1>Amazon Search for <?php echo $_GET['keyword']; ?></h1>     

    <p>     

        <a href="?keyword=Linux">Search for Linux</a> |     

        <a href="?keyword=Apache">Search for Apache</a> |     

        <a href="?keyword=MySQL">Search for MySQL</a> |     

        <a href="?keyword=PHP">Search for PHP</a>     

    </p>     

    <table>     

        <tr>     

            <td>     

        <?php     

        foreach ($amazon_data['product'] as $product) {     

        ?>     

        <table width="600">     

            <tr>     

                <th><?php echo nl2br(wordwrap($product['title'], 40)); ?></th>     

                <td rowspan="2" align="right">     

                    <a href="<?php echo $product['tagged_url']; ?>">     

                        <img src="<?php echo $product['small_image']; ?>"     

                             border="0" />     

                    </a>     

                </td>     

            </tr>     

            <tr>     

                <td>     

                    Author: <?php echo $product['author']; ?><br />     

                    ISBN: <?php echo $product['asin']; ?><br />     

                    Price: <?php echo $product['our_price']; ?><br />     

                </td>     

            </tr>     

        </table>     

        <?php     

        }     

        ?>     

            </td>     

        </tr>     

    </table>     

</body>     

</html>
```

文件名:amazon.php

这里的代码基本上与您之前在 PEAR 入门的[末尾看到的一样，用于解析 RSS 提要。我使用了](https://www.sitepoint.com/article/getting-started-with-pear) [PEAR::HTTP_Request](http://pear.php.net/HTTP_Request) (版本 1.2)作为 HTTP 客户端，给我更详细的错误报告。剩下的只是简单地将亚马逊的数据去序列化。

下面是(有些粗糙的)HTML 在浏览器中的样子:

![1336_amazon](img/8a4c991cefc4535bd5859c137cf0781a.png)

当然，它并不仅限于解析别人的 XML。在你自己的网站上做同样的事情怎么样？这里有一个简单的例子:

```
<?php     

// An array simulating a database result set     

$products = array(     

    array('code' => '000325', 'item' => 'Hamster', 'price' => 13.99),     

    array('code' => '005523', 'item' => 'Parrot', 'price' => 76.99),     

    array('code' => '000153', 'item' => 'Snake', 'price' => 49.99),     

);     

// If ?mime=xml is in the URL, display XML     

if (isset($_GET['mime']) && $_GET['mime'] == 'xml') {     

    error_reporting(E_ALL ^ E_NOTICE);     

    require_once 'XML/Serializer.php'; // Lazy include     

    $serializer_options = array (     

        'addDecl' => TRUE,     

        'encoding' => 'ISO-8859-1',     

        'indent' => '  ',     

        'rootName' => 'products',     

        'defaultTagName' => 'product',     

    );     

    $Serializer = &new XML_Serializer($serializer_options);     

    $status = $Serializer->serialize($products);     

    if (PEAR::isError($status)) {     

        die($status->getMessage());     

    }     

    // Display the XML     

    header('Content-type: text/xml');     

    echo $Serializer->getSerializedData();     

} else {     

    // Otherwise the HTML equivalent     

?>     

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Strict//EN"     

        "https://www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">     

<html  lang="en" xml:lang="en">     

<head>     

    <title>Product Catalog</title>     

</head>     

<body>     

    <h1>Product Catalog</h1>     

    <table>     

        <tr>     

            <th>Product Code</th>     

            <th>Item</th>     

            <th>Price</th>     

        </tr>     

        <?php     

        foreach ($products as $product) {     

        ?>     

        <tr>     

            <td><?php echo $product['code']; ?></td>     

            <td><?php echo $product['item']; ?></td>     

            <td><?php echo $product['price']; ?></td>     

        </tr>     

        <?php     

        }     

        ?>     

    </table>     

</body>     

</html>     

<?php     

}     

?>
```

文件名:products.php

如果有人将`?mime=xml`添加到用于查看该脚本的 URL 中，而不是以 HTML 的形式接收页面，他们会得到下面的 XML:

```
<?xml version="1.0" encoding="ISO-8859-1"?>     

<products>     

  <product>     

    <code>000325</code>     

    <item>Hamster</item>     

    <price>13.99</price>     

  </product>     

  <product>     

    <code>005523</code>     

    <item>Parrot</item>     

    <price>76.99</price>     

  </product>     

  <product>     

    <code>000153</code>     

    <item>Snake</item>     

    <price>49.99</price>     

  </product>     

</products>
```

这使得在远程会员网站上显示您的数据变得非常容易，如果您愿意的话。只要将处理访问和操作数据的代码与处理将数据呈现给最终用户的代码分开，使用 XML_Serializer 提供“替代 XML 视图”应该没有问题。

将类似于 [JOX](http://www.wutka.com/jox.html) 的库加入其中，它为 Java Beans 提供了一个类似的 XML 序列化器，这样就有了一个让 PHP 和 Java 对话的便利机制。

##### 包裹

正如您在本文中看到的，PEAR::XML_Serializer 为处理 XML 提供了一个非常方便的工具。您已经看到了如何使用 PEAR::XML_Serializer，现在对它所适用的问题类型有了一些了解。还有一些小问题需要解决(这里使用的版本 0.9.1 是测试版),但是总的来说，PEAR::XML_Serializer 运行可靠，我还没有发现任何令人失望的错误。

最重要的是，PEAR::XML_Serializer 提供了一种解析 XML 的方法，使您不必处理 XML 的细节。正如在[处理 XML 的 API 和技术综述](http://www.xml.com/pub/a/2003/07/09/xmlapis.html)中所描述的，PEAR::XML_Serializer 提供了一个“对象到 XML 映射 API”。尽管使用这种方法有一些限制，但是对于解决您在本文中看到的这类问题来说，对象到 XML 映射 API 使事情变得容易多了。

随着 PHP5 打包了大幅改进的 XML 支持，加上对 XML Schema 和 Relax NG 的支持，PEAR::XML_Serializer 将有机会处理它目前通过“typeHints”实现的功能。随之而来的是与 Java 互操作的进一步可能性(通过 [JAXB](http://java.sun.com/xml/jaxb/) )和。NET(通过它的 [XmlSerializer](http://msdn.microsoft.com/library/default.asp?url=/library/en-us/dnexxml/html/xml01202003.asp) )。

## 分享这篇文章