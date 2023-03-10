# 用 PHP 从 Access 数据库中提取对象，第 1 部分

> 原文：<https://www.sitepoint.com/extract-ole-objects-from-an-access-database-using-php-1/>

简单地搜索一下 Web 就会发现，有许多程序员需要使用传统的数据库，如 Microsoft Access，但是在面对从 Access 的 OLE 对象字段中提取文件的任务时却被难住了。部分问题是缺少关于对象如何存储在 OLE 字段中的现成信息。

对 PHP 程序员来说更糟糕的是，论坛和支持网站上发布的每个查询实例都来自非 PHP 程序员。Net 似乎是最常见的，也有 Java 和 Delphi 程序员的提问。

不过，所有这些开发人员的一个共同点是需要从这些麻烦的 OLE 字段中提取信息，以便从遗留数据库中进行迁移。但是没有一篇文章专门针对 PHP 程序员。它们也不处理“包”，只处理特定的数据类型，如 JPEG、PNG、GIF 等。

在本文中，我们将看到如何使用 PHP 从两种 OLE 类型中提取对象:包和 Acrobat PDF 文档。这是由两部分组成的系列文章的第一部分，在这个系列文章中，我们将了解 OLE 包，正如我们将看到的，它可以被标识为“包”或“打包程序外壳对象”。本文中表示的值基于 Access 2000 数据库。

## OLE 容器

在数据库中存储 blob(二进制大对象)从来都不是一件简单的事情，微软的 Access 数据库也不例外。让我们总结一下将对象插入 Access 的 OLE 字段时所使用的 OLE 容器的一些关键方面:

*   如果没有先包装在容器中，任何外部文件都不会插入到 Access OLE 字段中。就我们的目的而言，这个容器可以被认为是我们感兴趣的对象前面的一个头和后面的一个尾。
*   报头长度不固定。即使对于 OLE 包类型，标题长度也可能不同，这取决于容器中嵌入的对象。
*   数据不是人类可读的形式，这使得很难直观地找到关键项目，如嵌入对象的原始名称、其名称或对象组件的开头。
*   预告片可以忽略。我们在这里的重点是头部和对象组件。

还有一点:不只是对象组件被编码，头也是。在我们能够理解任何数据之前，首先需要解码报头。

## 编码报头和数据

尽可能简单地定义测试数据库，嵌入文件包含在`Table1`的`image`字段中:

![ole01-1](img/ea4f78d35b0f7826f23e937df5e346ff.png)

在整个工作示例中，我们将使用我存储在 OLE 字段中的 Apache 徽标 GIF。在不解码的情况下提取字段内容，然后在十六进制查看器中检查输出，会发现乱码:

![ole01-2](img/c28e341331767a995ad0309463543a15.png)

在使用 PHP 的`hex2dec()`函数将提取的头从十六进制编码转换为十进制编码后，我们有了更有用的东西:

![ole01-3](img/729c5d21922e0576c46b7139160349be.png)

这看起来并不吸引人，但是很明显我们现在可以理解这些数据了。为了简化问题，可以忽略前 20 个字节。

下一个字符块告诉我们 OLE 对象的类型是 Packager Shell 对象。OLE 包是一种通用容器，用于存放插入文件时数据库无法识别的文件类型。

用法恩斯沃思教授的话说，“好消息，各位！”如果我们仔细观察十六进制转储的 ASCII 列，我们可以看到嵌入在容器中的原始文件的名称–`apache_02.gif`。稍后当我们试图定位报头的结尾和我们需要提取的数据的开始时，这将是有用的。更好的是，文件名总是从打包程序外壳对象头的字节 84 开始，从包头的字节 70 开始。

在继续讨论如何从包中提取 GIF 之前，这里有一个小问题:在这两个十六进制转储中，序列 0A0600 都有黄色下划线。现在，接受 0A0600 是重要的。我们一会儿就知道为什么了。

## 提取对象

在我们能够提取嵌入的对象之前，我们需要知道它在数据中的开始和结束位置。仔细检查十六进制表示可以发现，文件名的字符序列出现了三次——一次出现在字节 84(或 70 ),然后作为完整路径的一部分又出现了两次。

![ole01-4](img/51cf4af5d63e9151a621e5e0c8592d1e.png)

至少，在这个例子中是这样的。更复杂的是，完整路径可能只出现一次。此外，Access 可能已经更改了文件路径的大小写，或者将文件路径的每一级更改为老式的 8:3 格式(这意味着长度超过 8 个字符的名称将被截断，字符 7 和 8 将被~1 替换)。幸运的是，在本例中，在第二个完整路径的位置附近总会有一个完整路径的实例。“地点附近的某个地方？”正如标头长度不固定一样，完整路径的位置也不固定。我们需要解决这些小问题。

“更多好消息，各位！”文件名的最后一个实例是文件头中倒数第二个重要的项目。它是空终止的(00)，后面是我们前面看到的十六进制序列 0A0600。这是嵌入对象的原始字节大小。它是以 little-endian 格式存储的，所以在我们使用它之前，需要将其反转为 big-endian 格式:0A0600 变成 00060A，这意味着 1，546 个字节。这三个字节的组给出了 16 MB 的最大允许嵌入文件大小。

这个序列之后就是我们需要的对象。它以字符序列 474946383961 开始。将其转换为 ASCII 码会产生“GIF89a”。它不会显示在 ASCII 列中，因为 Access 不会将 OLE 头中的数据存储在一致的双字符块中。这就是为什么完整路径的第一个实例很好地显示到“DocumentsWri ”,然后就出错了。在本例中,“Wri”后面的字符应该是“Writing”中的“t ”,但是 Access 在序列中插入了一个空字符。为什么？是啊，微软，为什么！请注意，“ting”(74646 e67)紧跟在空值之后。又一个小麻烦。

让我们回顾一下。我们现在有什么？我们有嵌入文件的原始名称、原始文件的字节大小，以及我们需要提取的数据的起始位置。这样，我们就有了从遗留 Access 数据库中提取嵌入文件所需的全部内容，然后使用其原始名称和扩展名将其保存到光盘上。

## 将理论付诸实践

下面是我用来从我的测试数据库中提取我们上面讨论的对象细节的 PHP。这是一个简洁的过程化 PHP，任何人都应该很容易理解从 OLE 包中提取对象的步骤。

你会注意到一些逻辑将某些数字乘以了两倍。例如，嵌入文件名的偏移量被定义为 168，而不是上面提到的 84 字节。这是因为部分代码处理 Access 数据库 OLE 字段中的原始数据，这些数据是以十六进制格式编码的。也就是说，每个字节都存储为两个十六进制字符。

```
<?php
if (!function_exists("hex2bin")) {
   function hex2bin($hexStr) {
      $hexStrLen = strlen($hexStr);
      $binStr = "";
      $i = 0;
      while ($i < $hexStrLen) {
         $a = substr($hexStr, $i, 2);
         $c = pack("H*", $a);
         $binStr .= $c;
         $i += 2;
      }
      return $binStr;
   }
}

$dbName = "db1.mdb";
$db = new PDO("odbc:DRIVER={Microsoft Access Driver (*.mdb)}; DBQ=$dbName; Uid=; Pwd=;");

$sql = "SELECT * FROM Table1 WHERE id = 2";
foreach ($db->query($sql) as $row) {
   $objName = "";

   switch (getOLEType($row["image"])) {
      case "Packager Shell Object":
          list($objName, $objData) = extractPackagerShellObject($row["image"]);
          break;
      case "Package":
          list($objName, $objData) = extractPackage($row["image"]);
          break;
      default:
          throw new Exception("Unknown OLE type");
   }
   if ($objName != "") {
      file_put_contents($objName, $objData);
   }
}

function flipEndian($data, $size) {
   $str = "";
   for ($i = $size - 2; $i >= 0; $i -= 2) {
      $str .= substr($data, $i, 2);
   }
   return $str;
}

function findNullPos($str) {
   // must start on a two-character boundary
   return floor((strpos($str, "00") + 1) / 2) * 2;
}

function getOLEType($data) {
   // fixed position of OLE type
   $offset = 40;

   $tmp = substr($data, $offset, 255);
   $nullPos = findNullPos($tmp);
   $tmp = substr($tmp, 0, $nullPos);
   $type = hex2bin($tmp);

   return $type;
}

function extractPackagerShellObject($data) {
   $headerBlock = 500; // usable header size
   $offset = 168; // location of name

   // find name
   $tmp = substr($data, $offset, 255);
   $nullPos = findNullPos($tmp);
   $name = substr($tmp, 0, $nullPos);
   $pos = $offset + strlen($name);

   // find data
   $path1 = strpos($data, $name, $pos); // 1st full path
   $pos = $path1 + strlen($name);
   $path2 = strpos($data, $name, $pos); // 2nd full path
   // check if only one full path
   if ($path2 > $pos) {   
      $pos = $path2 + strlen($name);
   }
   $oleSizePos = $pos + 2;
   $oleObjSize = flipEndian(substr($data, $oleSizePos, 8), 8);
   $oleHeaderEnd = $oleSizePos + 8;
   $objName = hex2bin(substr($tmp, 0, $nullPos));

   // extract object
   $data = substr($data, $oleHeaderEnd, hexdec($oleObjSize) * 2);
   $objData = hex2bin($data);

   return array($objName, $objData);
}

function extractPackage($data) {
   $headerBlock = 500; // usable header size
   $offset = 140; // location of name

   // find name
   $tmp = substr($data, $offset, 255);
   $nullPos = findNullPos($tmp);
   $name = substr($tmp, 0, $nullPos);
   $pos = $offset + strlen($name);

   // find data
   $path1 = strpos($data, $name, $pos); // 1st full path
   $pos = $path1 + strlen($name);
   $path2 = strpos($data, $name, $pos); // 2nd full path
   // check if only one full path
   if ($path2 > $pos) {   
      $pos = $path2 + strlen($name);
   }
   $oleSizePos = $pos + 2;
   $oleObjSize = flipEndian(substr($data, $oleSizePos, 8), 8);
   $oleHeaderEnd = $oleSizePos + 8;
   $objName = hex2bin(substr($tmp, 0, $nullPos));

   // extract object
   $data = substr($data, $oleHeaderEnd, hexdec($oleObjSize) * 2);
   $objData = hex2bin($data);

   return array($objName, $objData);
}
```

请注意，只有当您的 PHP 安装没有自己的函数时，才需要 bespoke `hex2bin()`函数(它是在 PHP 5.4 中添加的)。此外，`switch`语句使逻辑可扩展，我们将在本文的下一部分中发现这一点。

这两个包函数看起来非常相似。这些将在本文的下一部分中再次讨论，因为这些和其他对象类型所需的逻辑性质相似，所以代码将被重构。

我用以下文件类型测试了这个 PHP:BMP、GIF、JPEG、PNG、DOC、XLS 和 PPT，所有这些文件都存储为包。上述逻辑应该允许从遗留数据库中保存存储在 OLE 包中的任何文件，而不仅仅是本例中使用的 GIF 文件。

## 摘要

在本文中，我们介绍了使用 PHP 从 Microsoft Access 数据库的 OLE 字段中提取包对象的基本要素。了解了 OLE 对象的基本结构以及如何提取其中包含的文件后，上述方法可以应用于其他 OLE 类型，以确保可以从旧的 Access 数据库中检索尽可能多的数据。

在本系列的[第二部分](https://www.sitepoint.com/extract-ole-objects-from-an-access-database-using-php-2/ "Extract OLE Objects from an Access Database using PHP, Part 2")中，我们将关注提取已知对象类型这一更复杂的问题，特别关注从遗留 Access 数据库中提取 Acrobat PDF 文档。与此同时，您可以随意用本文的代码克隆 [GitHub 库来玩和探索。](https://github.com/phpmasterdotcom/ExtractObjectsFromAccessDatabase/tree/part-1 "ExtractObjectsFromAccessDatabase at part-1 - GitHub")

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章