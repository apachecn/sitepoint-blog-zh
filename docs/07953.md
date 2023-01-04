# 在你的 PHP 项目中使用 YAML

> 原文：<https://www.sitepoint.com/using-yaml-in-php-projects/>

测试装置、配置文件和日志文件都需要是人类和机器可读的。YAML (YAML 不是标记语言)是一种比 XML 更简洁的数据序列化格式，并且已经成为软件开发人员中的流行格式，主要是因为它是人类可读的。YAML 文件是简单的文本文件，包含根据 YAML 语法规则编写的数据，通常有一个`.yml`文件扩展名。在本文中，您将学习 YAML 的基础知识，以及如何将 PHP YAML 解析器集成到您的项目中。

## 理解 YAML 语法

YAML 支持引用和自定义数据类型等高级特性，但作为一名 PHP 开发人员，大多数时候你会对 YAML 如何表示枚举数组(YAML 术语中的序列)和关联数组(映射)感兴趣。

以下是如何在 YAML 中表示枚举数组:

```
- 2
- "William O'Neil"
- false
```

数组的每个元素都出现在连字符和空格之后。它表示值的语法类似于 PHP(引用字符串等)。)

以上等价于下面的 PHP:

```
<?php
array(2, "William O'Neil", false);
```

一般来说，在 YAML 中，每个元素都出现在自己的行上，但是枚举数组也可以用括号表示在一行上:

```
[ 2, "William O'Neil", false ]
```

以下代码显示了如何在 YAML 中表示关联数组:

```
id:       2
name:     "William O'Neil"
isActive: false
```

首先声明元素的键，后跟一个冒号和一个或多个空格，然后声明值。冒号后面只有一个空格就足够了，但是如果愿意，为了更好的可读性，可以使用更多的空格。

上述 YAML 的等效 PHP 数组是:

```
<?php
array("id" => 2, "name" => "William O'Neil", "isActive" => false);
```

与枚举数组类似，您可以使用大括号在一行中表示关联数组:

```
{ id: 2, name: "William O'Neil”, isActive: false }
```

通过一个或多个空格进行缩进，可以表示一个多维数组，如下所示:

```
author:
  0: { id: 1, name: "Brad Taylor", isActive: true }
  1: { id: 2, name: "William O'Neil", isActive: false }
```

注意，虽然第二级数组是枚举数组，但为了清楚起见，我使用了映射语法(冒号)而不是序列语法(连字符)。

上面的 YAML 块相当于下面的 PHP:

```
<?php
array(
    "author" => array(
        0 => array("id" => 1, "name" => "Brad Taylor", "isActive" => true),
        1 => array("id" => 2, "name" => "William O'Neil", "isActive" => false)
    )
);
```

YAML 还允许在同一个文档中表示一组数据元素，而不需要根节点。下面的例子是`article.yml`的内容，它显示了同一个文件中的几个多维数组。

```
author:
  0: { id: 1, name: "Brad Taylor", isActive: true }
  1: { id: 2, name: "William O'Neil", isActive: false }

category:
  0: { id: 1, name: "PHP" }
  1: { id: 2, name: "YAML" }
  2: { id: 3, name: "XML" }

article:
  0:
    id:      1
    title:   "How to Use YAML in Your Next PHP Project"
    content: >
               YAML is a less-verbose data serialization format.
               It stands for "YAML Ain't Markup Language".
               YAML has been a popular data serialization format among
               software developers mainly because it's human-readable.
    author:  1
    status : 2

articleCategory:
  0: { articleId: 1, categoryId: 1 }
  1: { articleId: 1, categoryId: 2 }
```

虽然 YAML 的大部分句法都很直观且容易记忆，但有一条重要的规则你应该注意。缩进必须有一个或多个空格；不允许使用制表符。您可以将 IDE 配置为在按 tab 键时插入空格而不是制表符，这是软件开发人员中的一种常见配置，用于确保代码在其他编辑器中查看时能够正确缩进和显示。

你可以通过阅读[官方文档](http://www.yaml.org/ "The Official YAML Web Site")、 [Symfony 参考文献](http://www.symfony-project.org/reference/1_4/en/02-YAML "The symfony Reference Book | The YAML Format | symfony | Web PHP Framework")或[维基百科](http://en.wikipedia.org/wiki/YAML "YAML - Wikipedia, the free encyclopedia")来了解 YAML 支持的更复杂的特性和语法。

## YAML 不一定是 XML 的替代品

如果你正在用你最喜欢的搜索引擎研究 YAML，你无疑会找到关于“YAML vs XML”的讨论，当你第一次体验 YAML 时，你自然会倾向于更喜欢它而不是 XML，主要是因为它更容易读写。然而，YAML 应该是开发人员武库中的另一个工具，不需要成为 XML 的替代品。以下是 YAML 和 XML 的一些优点。

**YAML 的优势**

*   不那么冗长，易于编写，可读性更好
*   不需要具有单个父节点的树形结构

XML 的优势

*   与 YAML 相比，内置了更多的 PHP 支持
*   XML 已经成为应用程序间通信的事实上的标准，并得到了广泛的认可
*   XML 标签可以有属性来提供关于所包含数据的更多信息

尽管冗长，但与 YAML 的面向空间的层次表示相比，当元素的层次较深时，XML 更具可读性和可维护性。

考虑到这两种语言的优势，YAML 似乎更适合不同数据集的集合，并且人类也是数据消费者之一。

## 选择 PHP YAML 解析器

YAML 解析器应该有两种功能，一种是将 YAML 转换成数组的加载函数，另一种是将数组转换成 YAML 的转储函数。

目前 PHP 的 YAML 解析器是作为 PECL 扩展提供的，没有和 PHP 捆绑在一起。或者，也有用纯 PHP 编写的解析器，与 PECL 扩展相比会稍慢一些。

下面是一些适用于 PHP 的 YAML 解析器:

**[PECL 扩展](http://php.net/manual/en/book.yaml.php "PHP: Yaml - Manual")**

*   不与 PHP 捆绑在一起
*   需要对服务器的超级用户访问权限才能安装

**[Symfony 1.4 YAML 组件](http://www.symfony-project.org/installation/1_4 "The smfony framework installation | symfony | Web PHP Framework")**

*   用 PHP 实现
*   将在 PHP 5.2.4+中工作
*   需要从 Symfony 框架中提取

**[Symfony 2 YAML 组件](http://symfony.com/doc/current/components/yaml.html "The YAML Component (Current) - Symfony")**

*   用 PHP 实现
*   将在 PHP 5.3.2+中工作

**[查询](http://code.google.com/p/spyc/ "spyc - A simple YAML loader/dumper class for PHP - Google Project Hosting")**

*   用 PHP 实现
*   将在 PHP 5+中工作

我的首选是 Symfony 1.4 YAML 组件，因为它的可移植性(它适用于 PHP 5.2.4+版本)和成熟性(Symfony 1.4 是一个成熟的 PHP 框架)。一旦你从 Symfony 档案中提取了 YAML 组件，YAML 类就可以在`lib/yaml`下找到。静态方法`load()`和`dump()`可用于`sfYaml`类。

***编者注 2012 年 10 月 28 日:**GitHub 上附带的代码已经更新，可以使用 Composer 来获取 PHPUnit 和 Symfony 1.4 YAML 组件依赖项。*

## 将 PHP YAML 解析器集成到您的项目中

每当您将第三方类或库集成到您的 PHP 项目中时，创建一个包装器和一个测试套件是一个很好的实践。这使您可以在稍后更改第三方库，只需对项目代码进行最小的更改(项目代码应该只引用包装器)，并确保更改不会影响任何功能(测试套件会告诉您)。

下面是我为我的包装类(`YamlParser.php`)创建的测试用例(`YamlParserTest.php`)。您需要 PHPUnit 的知识来运行和维护测试用例。如果你愿意，你可以添加更多的测试，除了`.yml`之外的错误文件名和文件扩展名，以及基于你在项目中遇到的场景的其他测试。

```
<?php
require_once "YamlParser.php";

class YamlParserTest extends PHPUnit_Framework_TestCase
{
    private $yamlParser;

    public function setup() {
        $this->yamlParser = new YamlParser();
    }

    public function testMainArrayKeys() {
        $parsedYaml    = $this->yamlParser->load("article.yml");
        $mainArrayKeys = array_keys($parsedYaml);
        $expectedKeys  = array("author", "category", "article", "articleCategory");

        $this->assertEquals($expectedKeys, $mainArrayKeys);

    }

    public function testSecondLevelElement() {
        $parsedYaml    = $this->yamlParser->load("article.yml");
        $actualArticle = $parsedYaml["article"][0];
        $title         = "How to Use YAML in Your Next PHP Project";
        $content = "YAML is a less-verbose data serialization format. "
                 . "It stands for "YAML Ain't Markup Language". "
                 . "YAML has been a popular data serialization format among "
                 . "software developers mainly because it's human-readable.n";

        $expectedArticle = array("id" => 1, "title" => $title, "content" => $content, "author" => 1, "status" => 2);

        $this->assertEquals($expectedArticle, $actualArticle);
    }

    /**
     * @expectedException YamlParserException
     */
    public function  testExceptionForWrongSyntax() {
        $this->yamlParser->load("wrong-syntax.yml");
    }
}
```

这是包装类:

```
<?php
require_once "yaml/sfYaml.php";

class YamlParser
{
    public function load($filePath) {
        try {
            return sfYaml::load($filePath);
        }
        catch (Exception $e) {
            throw new YamlParserException(
                $e->getMessage(), $e->getCode(), $e);
        }
    }

    public function dump($array) {
        try {
            return sfYaml::dump($array);
        }
        catch (Exception $e) {
            throw new YamlParserException(
                $e->getMessage(), $e->getCode(), $e);
        }
    }
}

class YamlParserException extends Exception
{
    public function __construct($message = "", $code = 0, $previous = NULL) {
        if (version_compare(PHP_VERSION, "5.3.0") < 0) {
            parent::__construct($message, $code);
        }
        else {
            parent::__construct($message, $code, $previous);
        }
    }
}
```

## 摘要

现在您已经了解了什么是 YAML，如何在 YAML 表示 PHP 数组，以及如何将 PHP YAML 解析器集成到您的项目中。通过多花一点时间学习 YAML 语法，您将能够掌握它提供的强大功能。您也可以考虑探索广泛使用 YAML 的 Symfony 1.4 和 2 框架。如果您对本文中的[代码感兴趣，可以在 GitHub 上找到。](https://github.com/phpmasterdotcom/UsingYAMLinPHP "phpmasterdotcom/UsingYAMLinPHP")

<small>图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")</small>

## 分享这篇文章