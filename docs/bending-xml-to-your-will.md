# 随心所欲地使用 XML

> 原文：<https://www.sitepoint.com/bending-xml-to-your-will/>

如果您曾经使用过 Twitter 或脸书 API，查看过网站上的 RSS 提要，或者使用过某种类型的 RPC 调用，那么您无疑体验过使用 XML。可扩展标记语言(XML)是当今 web 的一个重要组成部分，已经开发了数百种基于 XML 的语言，包括 XHTML、ATOM 和 SOAP 等等。我自己不得不与许多第三方系统一起工作来发送和接收数据，所有这些系统的首选方法都是 XML。

如今，知道如何处理 XML 数据是一项至关重要的编程技能，幸运的是，PHP 提供了多种读取、过滤甚至生成 XML 的方法。在这篇文章中，我将解释 XML 到底是什么，以防你还没有任何使用它的经验，然后深入到一些你可以使用 PHP 按照你的意愿改变 XML 的方法。

## XML 是做什么的？

“XML 是做什么的”这个问题的简短回答什么都没有。它什么也不做。XML 只是一种标记语言，类似于 HTML。HTML 是为显示数据而设计的，而 XML 则是为传输和存储数据提供一种结构化的方式。

让我们看一个简单的 XML 示例，它包含特定运动队的信息:

```
<?xml version="1.0" encoding="UTF-8" ?>
<roster>
 <team>
   <name>Bengals</name>
   <division>AFC North</division>
   <colors>Black and Orange</colors>
   <stadium location="Cincinnati">Paul Brown Stadium</stadium>
   <coach>Marvin Lewis</coach>
 </team>
 <team>
  <name>Titans</name>
  <division>AFC South</division>
  <colors>Blue and White</colors>
  <stadium location="Tennessee">LP Field</stadium>
  <coach>Mike Munchak</coach>
 </team>
</roster>
```

从例子中可以看出，XML 是人类可读的，并且是自描述的。与 HTML 不同，XML 没有预定义的标签，允许你创造自己的标签。任何人，不管是不是程序员，都可以看看这个例子，理解数据。您创建的软件负责编写或解析 XML 文档中的信息。

在各种平台、数据库和编程语言之间共享信息可能是一件令人沮丧的事情，但是因为 XML 只是一个纯文本文件，所以它允许您的数据独立于正在使用的软件。因为 XML 是一个如此广泛使用的标准，所以它也给了您开发应用程序的自由，而不用担心另一端的不兼容性。

如果你对 XML 和它在 web 开发中的地位还不太了解，看看这篇精彩的 XML 介绍，[一篇非常非常非常好的 XML 介绍](https://www.sitepoint.com/really-good-introduction-xml/)。

## XML 解析器的类型

XML 解析器有两种基本类型:基于树的解析器和基于事件的解析器(有时称为流解析器)。基于树的解析器将整个 XML 文档读入内存，将数据组织成类似树的格式，并允许您访问树元素。另一方面，基于事件的解析器读入 XML，并在每次到达新的开始或结束标记时引发一个事件。这允许您在特定元素发生事件时应用与您的应用程序相关的功能。因为不是将整个 XML 文档存储在内存中，所以基于事件的解析器通常比基于树的解析器更快，占用的资源也更少。基于树的解析器通常更容易使用，需要的代码也更少。

PHP 5 有太多的工具可供选择，包括 XML 解析器(也称为 SAX 或 Expat 解析器)、DOM、SimpleXML、XMLReader、XMLWriter 和 XSL 扩展。为了简洁起见，我只看两种最广泛使用的解析器，XML 解析器和 SimpleXML 扩展，它们恰好是每种解析器的一种。

## 使用 XML 解析器扩展

我将展示的第一个例子涉及使用 XML 解析器扩展，这是一个基于事件的解析器。首先，让我们使用前面的同一个 XML 示例，并用扩展名解析它。假设您有一项任务，要将 XML 解析成一个简单的列表，并显示在 web 页面上。以示例 XML 为内容创建文件`nfl.xml`。

使用以下代码创建另一个名为 xmlParserExample.php 的文件:

```
<?php
$xmlFile = "nfl.xml";

$parser = xml_parser_create();
xml_parser_set_option($parser, XML_OPTION_CASE_FOLDING, false);
xml_set_element_handler($parser, array(NFLParser, "openTag"),
    array(NFLParser, "closeTag"));
xml_set_character_data_handler($parser,
    array(NFLParser, "characterData"));

$fp = fopen($xmlFile, "r");
while ($data = fread($fp, 4096)) {
    xml_parse($parser, $data, feof($fp))
        or die (sprintf("XML Error: %s at line %d",
            xml_error_string(xml_get_error_code($parser)), 
            xml_get_current_line_number($parser)));
}
xml_parser_free($parser);

class NFLParser {
    protected static $element;
    protected static $attrs;

    public static function openTag($parser, $elementName, $elementAttrs) {
        self::$element = $elementName;
        self::$attrs = $elementAttrs;

        switch($elementName) {
            case "team":
                echo "<ul>";
                break;
            case "division":
                echo "<li>Division: ";
                break;
            case "name":
                echo "<li>Team Name: ";
                break;
            case "colors":
                echo "<li>Team Colors: ";
                break;
            case "stadium":
                echo "<li>Stadium: ";
                break;
            case "coach":
                echo "<li>Head Coach: ";
        }
    }

    public static function closeTag($parser, $elementName) {
        self::$element = null;
        self::$attrs = null;

        if ($elementName == "team") {
            echo "</ul>";
        }
        elseif($elementName != "roster") {
            echo "</li>";
        }
    }

    public static function characterData($parser, $data) {
        echo $data;
        if (self::$element == "stadium") {
            echo " (" . self::$attrs["location"] . ")";
        }
    }
}
```

`xml_parser_create()`函数创建一个新的 XML 解析器处理程序，在整个代码中使用。下一个函数`xml_parser_set_option()`用于为解析器设置选项。在这种情况下，`XML_OPTION_CASE_FOLDING`选项被设置为 false(因为默认情况下它被设置为 true)。大小写折叠是一个应用于字符序列的过程，在这个过程中，所有字符都转换为大写。通过将此选项设置为 true，我可以准确地保留标记在 XML 文件中出现时的大小写敏感性。

`xml_set_element_handler()`函数设置解析器的开始和结束元素处理程序。该函数接受三个参数:第一个参数是解析器引用，第二个参数是处理开始标记的回调函数(在本例中是`NFLParser`类的静态`openTag()`方法)，第三个参数是处理结束标记的回调函数(`closeTag()`方法)。

PHP 向`openTag()`传递三个参数:解析器、调用该处理程序的元素的名称，以及该元素所有属性的关联数组。向`closeTag()`提供了两个参数:解析器和元素名称。

`xml_set_character_data_handler()`函数指定了处理元素字符数据的函数。该函数接受两个参数:解析器和回调函数的名称，在本例中，回调函数是静态的`characterData()`方法。向`characterData()`方法传递了两个参数:解析器和来自元素的字符数据。

示例中剩余的代码读入 XML 文件并调用启动解析过程的`xml_parse()`函数。`xml_parse()`接受三个参数:解析器、要解析的数据块和指示它是否是最后一段数据的布尔参数。

最后调用的函数是`xml_parser_free()`；就像在文件处理中一样，完成后释放引用句柄总是一个好主意。

我选择将方法封装在类`NFLParser`中，这样我就可以跟踪在`$element`和`$attrs`中解析的当前元素和属性，而不会污染全局名称空间，并使它们可用于`characterData()`方法。

执行您的脚本，您应该有一个很好的来自 XML 的所有数据的 HTML 列表。

```
<ul>
 <li>Team Name: Titans</li>
 <li>Team Colors: Blue and White</li>
 <li>Stadium: LP Field (Nashville)</li>
 <li>Head Coach: Mike Munchak</li>
</ul>
<ul>
 <li>Team Name: Bengals</li>
 <li>Team Colors: Black and Orange</li>
 <li>Stadium: Paul Brown Stadium (Cincinnati)</li>
 <li>Head Coach: Marvin Lewis</li>
</ul>
```

使用事件驱动的解析器用 PHP 解释 XML 还不错，但是如果有一种更简单的方法来分割 XML，如果你愿意的话，一种更简单的方法呢？

## 使用 SimpleXML

SimpleXML 扩展是在 PHP 5 中引入的，它消除了 XML 操作的许多繁琐。SimpleXML 是一种基于树的面向对象的解析器，所以它是一种解析 XML 的较慢且更耗费资源的方式，但是一旦您看到它使用起来有多“简单”,使用这种扩展所带来的任何速度损失都将被遗忘。

创建一个名为`simpleXMLExample.php`的文件，并输入下面的代码:

```
<?php
$xmlFile = "nfl.xml";

$xml = simplexml_load_file($xmlFile);

foreach($xml->team as $element){
    $attr = $element->stadium->attributes();
    $location = $attr->location;

    echo "<ul>n";
    echo " <li>Division:" . $element->division . "</li>n";
    echo " <li>Team Name:" . $element->name . "</li>n";
    echo " <li>Team Colors:" . $element->color . "</li>n";
    echo " <li>Stadium:" . $element->stadium ." (" . $location. ")</li>n";
    echo " <li>Coach" . $element->coach . "</li>n";
    echo "</ul>n";
}
```

执行这个脚本将产生相同的输出，但是不需要编写太多的解析代码。

您可能想知道，如果 SimpleXML 如此简单，为什么还要使用 XML Parser 这样的扩展呢？我把这个问题比作一个建筑工人，他上班时腰带上只带着一把锤子。当然，他会通过钉钉子度过一段时间，但最终他将面对一个螺丝钉。尽管一种工具可能更容易使用，但它并不是每种情况下的理想选择。

## 摘要

在本文中，您了解了一些关于 XML 的知识，以及如何在 web 上使用它。更重要的是，您了解了两种基本类型的 XML 解析器，基于树的解析器和基于事件的解析器。PHP 提供了几种不同的 XML 解析扩展，其中两种是 XML Parser 和 SimpleXML。每一种都在性能、易用性和程序员需要编写的代码量之间做出了权衡。希望看到这两种扩展是如何使用的将有助于您下次需要使用 XML 时自信地选择最佳方法。

图片 via [肯·德登](http://www.shutterstock.com/gallery-246394p1.html)/[Shutterstock](http://www.shutterstock.com)

## 分享这篇文章