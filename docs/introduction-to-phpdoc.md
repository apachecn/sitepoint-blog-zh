# PhpDoc 简介

> 原文：<https://www.sitepoint.com/introduction-to-phpdoc/>

如果你曾经试图阅读别人而不是你自己写的代码(谁没有呢？)，你知道这可能是一项艰巨的任务。一堆混杂着许多奇怪命名的变量的“意大利面条代码”让你头昏脑胀。这个函数需要字符串还是数组？这个变量存储的是整数还是对象？在花了数不清的时间跟踪代码线索并试图理解每一位的作用后，放弃并从头重写整个代码并不罕见——这是一项浪费你太多宝贵时间的任务。

PhpDoc 是 PhpDocumentor 的缩写，它是一个强大的工具，允许您通过特殊格式的注释轻松地记录代码。这些文档不仅可以在源代码中获得，还可以在使用 web 或命令行界面提取的专业文档中获得。结果可以是各种格式，如 HTML、PDF 和 CHM。此外，许多提供代码补全的 ide 可以解析 PhpDoc 注释，并提供有用的特性，比如类型提示。通过使用 PhpDoc，您可以让其他人(和您自己)在您编写代码几周、几个月甚至几年后也能很容易地理解您的代码。

安装 PhpDoc 最简单的方法是用 PEAR。当然，在这样做之前，您必须安装 PEAR。如果没有，请按照[pear.php.net/manual/en/installation.php](http://pear.php.net/manual/en/installation.php)的指示进行操作。

在本文中，我将从头到尾向您展示如何使用 PhpDoc 来生成漂亮且用户友好的文档。

## 文档块

DocBlock 是一个多行 C 风格的注释，用于记录代码块。它以`/**`开头，每行开头都有一个星号。这里有一个例子:

```
<?php
/**
 * Calculates sum of squares of an array
 *
 * Loops over each element in the array, squares it, and adds it to 
 * total. Returns total.
 * 
 * This function can also be implemented using array_reduce();
 * 
 * @param array $arr
 * @return int
 * @throws Exception If element in array is not an integer
 */
function sumOfSquares($arr) {
    $total = 0;
    foreach ($arr as $val) {
        if (!is_int($val)) {
            throw new Exception("Element is not an integer!");
        }
        $total += $val * $val;
    }
    return $total;
}
```

文档块由三部分组成:简短描述、详细描述和标签。这三个部分都是可选的。

简短描述是以换行符或句号结尾的简洁描述。PhpDoc 的解析例程很智能；只有当句号在句尾时，它才会结束简短的描述。

长描述是大部分文档的位置；它可以是多行的，只要你愿意。

长描述和短描述都可能包含某些用于格式化的 HTML 元素。不支持的 HTML 标签将显示为纯文本。PhpDoc 可以生成多种格式的文档，所以 HTML 标签不必像在 HTML 文件中那样显示；实际的格式取决于生成的文件格式。如果您需要将 HTML 标签显示为文本，请使用双括号。例如:

```
<?php
/**
 * Here an example of the italics tag: <<i>>Hello, world!<<i>>
 */
```

文档块的 tags 部分包含任意数量的由`@`符号表示的特殊标记。标签用于指定附加信息，例如预期的参数及其类型。大多数标签必须在它们自己的行上，除了某些标签可能是内联的。行内标签用花括号括起来，可能出现在长描述和短描述中。要获得标签的完整列表，请查看相关的 PhpDoc 文档。

如果您需要用`@`符号开始一行，但是您不希望它被解释为一个标签，您可以用一个反斜杠来转义它。

PhpDoc 将自动识别长描述和短描述中的文本列表，并解析它们。但是，它不能正确解析嵌套列表。如果你想使用嵌套列表，使用 HTML 标签。这里有一个例子来说明我的意思:

```
<?php
/**
 * Example of using lists
 * 
 * PhpDoc will parse this list correctly: 
 * - Item #1
 * - Item #2
 * - Item #3
 * 
 * But not this list:
 * - Item 1
 *   - Item 1.1
 *   - Item 1.2
 * - Item 2
 * 
 * Use this instead for a nested list:
 * <ul>
 *   <li>Item 1</li>
 *   <ul>
 *     <li>Item 1.1</li>
 *     <li>Item 1.2</li>
 *   </ul>
 *   <li>Item 2</li>
 * </ul>
 */
```

## 包装

PhpDoc 包用于在生成的文档中对相关的代码元素进行分组。您可以为文件和类指定包，它们包含的文档化代码将从它们那里继承包。要指定一个包，在文件级或类级文档块中设置`@package`标记(文件级和类级文档块将在下一节中进一步讨论)。包名可以包含字母、数字、破折号、下划线和方括号(“[”和“]”)。

以下是定义文件包的示例:

```
<?php
/**
 * This is a file-level DocBlock
 * 
 * @package Some_Package
 */
```

如果您有多个级别的包和子包，那么您可以用`@subpackage`标签定义一个子包。这里有一个例子:

```
<?php
/**
 * This is a class-level DocBlock
 * 
 * @package    Some_Package
 * @subpackage Other
 */
class SomeClass {
}
```

如果一个文件或类没有指定包，它将被设置为默认包，“默认”。当通过`-dn`命令行选项生成文档时，您可以指定默认使用的不同包。

## 我可以记录什么？

并非所有的代码元素都可以用 DocBlocks 记录。以下是可能被记录的代码元素列表:

*   文件
*   班级
*   功能和方法
*   类别属性
*   全局变量
*   `include()` / `require()`
*   `define()`

所有这些元素都可以使用某些公共标签，但是每个元素都有特定于该元素的标签。我将介绍一些通常用于记录它们的元素和标记。

### 文件

文件级文档块用于记录文件的内容。强烈建议在您记录的每个文件中包含一个文件级的 DocBlock，事实上，如果在生成文档时没有找到，PhpDoc 会显示一个警告。

大多数元素都是通过在元素前放置一个 DocBlock 来记录的，但是文件是个例外(因为不能在文件前写任何东西)。文件级文档块放在文件的第一行。

文件级文档块通常包含标签`@package`、`@subpackage`、`@license`和`@author`。前面已经讨论过了`@package`和`@subpackage`标签。`@license`标签用于指定包含您代码的外部许可证的 URL。标签必须包含许可证的 URL，还可以包含标题。标签用来指定文件的作者。它必须包含作者的名字和一个可选的电子邮件地址在尖括号中。

与大多数元素不同，文件级 DocBlock 必须包含一个`@package`标签，以便正确解析。

以下是文件级文档块的完整示例:

```
<?php
/**
 * This file contains common functions used throughout the application.
 *
 * @package    MyProject
 * @subpackage Common
 * @license    http://opensource.org/licenses/gpl-license.php  GNU Public License
 * @author     Moshe Teutsch <moteutsch@gmail.com>
 */
```

### 班级

类级文档块放在类定义之前，应该描述类的含义。像文件级文档块一样，类级文档块通常包含标签`@package`、`@subpackage`和`@author`。这里有一个例子:

```
<?php
/**
 * An example class
 *
 * The class is empty for the sake of this example.
 *
 * @package    MyProject
 * @subpackage Common
 * @author     Moshe Teutsch <moteutsch@gmail.com>
 */
class Example {
}
```

### 功能和方法

PhpDoc 中记录的函数和方法是相同的。(对于那些可能不熟悉术语的人来说，方法只是类中的一个函数。)函数和方法通常在它们的文档块中包含`@param`和`@return`标签。`@param`标签用于记录参数的预期类型。它包含三个部分:参数、数据类型和可选描述。`@return`标签用于记录返回类型。它包含两个部分:数据类型和可选描述。在这两个标记中，数据类型可以是有效的 PHP 数据类型、类名或“混合”。它还可以包含由竖线分隔的多个选项。

```
<?php
/**
 * Finds and returns user by ID or username
 *
 * @param int|string $user Either an ID or a username
 * @param PDO $pdo A valid PDO object
 * @return User Returns User object or null if not found
 */
function getUser($user, PDO $pdo)
{
    // ...
    if ($isFound) {
        return new User(...);
    }
    return null;
}
```

## 生成文档

一旦您记录了您的 PHP 代码，您将希望从中生成用户友好的文档。为此，运行 PhpDoc 命令行工具。

```
moteutsch@vivaldi:~$ phpdoc -d /path/to/files/ -t /path/to/docs/ -ti 'Documentation Title' -dn 'Default Package' -o HTML:frames:default
```

`-d`选项指定包含要记录的文件的逗号分隔的目录列表，而`-t`指定生成的文档的路径。`-ti`用于指定项目标题，`-dn`用于设置默认的包名。最后，`-o`规定了文档格式。PhpDoc 有大量的格式可供选择。完整列表可在 [PhpDoc 网站](http://manual.phpdoc.org/HTMLSmartyConverter/HandS/phpDocumentor/tutorial_phpDocumentor.howto.pkg.html#using.command-line.output)上获得。

通过执行 help 命令，您可以找到有关命令行工具的更多信息，如下所示:

```
moteutsch@vivaldi:~$ phpdoc -h
```

运行该命令后，您指定的文档路径应该包含生成的文档。

对于那些不习惯使用命令行界面的人来说，PhpDoc 还提供了一个 web 界面。详细讨论这个问题超出了本文的范围，但是您可以在 PhpDoc 的官方网站上阅读更多相关内容，[phpdoc.org](http://phpdoc.org/)。

## 摘要

在本文中，我已经让您初步了解了 PhpDoc 及其许多强大的特性。我已经解释了文档块及其组件的用途；我已经向您展示了如何使用包来组织您的文档；我已经解释了哪些代码元素可以被记录，以及这样做的一些常见例子；最后，我向您展示了如何从源代码生成文档。我强烈建议您在自己的项目中开始使用 PhpDoc，即使只是为了记录最重要的部分。它非常简单明了，可以为你和你的同事节省无数个小时的焦虑和烦恼。

图片 via [扎多罗日涅伊维克多](http://www.shutterstock.com/gallery-599005p1.html) / [快门](http://www.shutterstock.com/)

## 分享这篇文章