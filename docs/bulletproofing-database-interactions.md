# 使用 PHPUnit 数据库扩展的防弹数据库交互

> 原文：<https://www.sitepoint.com/bulletproofing-database-interactions/>

这里已经有一篇很棒的文章[讨论了测试驱动开发](https://www.sitepoint.com/getting-started-with-phpunit/)，但是您知道您也可以测试与您的数据库交互的代码吗？尤其是如果您的应用程序是数据密集型的，让您的 CRUD 代码接受一系列测试是一个很好的实践，有助于确保您的应用程序正常工作。

在本文中，您将学习如何使用 PHPUnit 及其数据库扩展用 PHP 编写数据库测试。附带的代码可以在 GitHub 上找到，所以可以随意检查源代码并使用它。

在这个练习中，您需要 PHPUnit 和 PHPUnit 数据库扩展。您可以使用 PEAR 安装两者。只需遵循 [PHPUnit 文档](http://www.phpunit.de/manual/3.6/en/installation.html)中列出的说明。

2012 年 10 月 20 日:您可以使用 Composer 安装 PHPUnit 和 PHPUnit 数据库扩展。GitHub 上附带的代码已经更新，可以使用 Composer 来安装这些依赖项。

## 预热

是时候让你的脚湿了！这意味着，就像在任何软件项目中一样，我们将首先检查我们将要编程的内容。

想象一下，你是一个在线杂志的后台忍者，你刚刚收到了一份新杂志的说明书，你将创建:

*   该杂志将由许多部分或网页组成。
*   每个版块最多显示四篇由该版块编辑设定的专题文章。

对于本文，我们将只在文章将要显示在页面上时测试检索。因此，我们不会关心编辑选择文章的用例。我们假设专题文章已经设置好，并且在数据库中。通过这种方式，我们隔离了我们想要测试的行为，这样我们就可以专注于它(就像你在实践中实际要做的那样)。

当然，由于我们正在编写一个数据库测试，所以这里的一个必要步骤是设置数据。毕竟，我们需要一些东西来测试 CRUD 操作！

使用源代码部分中包含的数据库转储在 MySQL 中创建数据库。这是一个鸟瞰图:

![](img/a0bffa88a8a19914ea138262afdc1e21.png "diagram")

[有三种方法](http://www.phpunit.de/manual/3.6/en/database.html#understanding-datasets-and-datatables)来建立基于文件的测试数据，但是在这里我们将使用 XML 数据集。创建一个名为`seed.xml`的 XML 文件；文档的根元素应该是`dataset`。接下来，键入表定义和行数据。例如，下面是`articles`表的样子:

```
<dataset>
 <table name="sections">
  <column>id</column>
  <column>name</column>
  <row>
   <value>1</value>
   <value>Gadgets</value>
  </row>
 </table>
 <table name="articles">
  <column>id</column>
  <column>title</column>
  <column>description</column>
  <column>content</column>
  <column>preview_image</column>
  <column>section_id</column>
  <row>
   <value>1</value>
   <value>Android vs iOS</value>
   <value>Lorem ipsum dolor sit amet</value>
   <value>Aliquam scelerisque rhoncus porttitor. Nullam eget pulvinar magna. In vel lectus ut diam adipiscing porta vitae id nisi.</value>
   <value>android.jpg</value>
   <value>1</value>
  </row>
...
 </table>
</dataset>
```

我建议您编写自己的数据作为练习，尽管您可以随时使用本文文件中包含的示例数据集。那里所有的桌子都已经满了。

## 编写测试类

在这一点上，我们现在实际上要编码的东西！我们需要告诉 PHPUnit 在每次运行测试时将测试数据插入到数据库中，这是通过创建一个测试类来完成的。你可以把 test 类想象成任何其他类，但是它包含了其他被测试类的测试用例。

对于使用 PHPUnit 的数据库测试，test 类扩展了`PHPUnit_Extensions_Database_TestCase`。我们可以告诉 PHPUnit 如何通过覆盖`getConnection()`方法连接到数据库，然后通过覆盖`getDataSet()`方法从哪里获取我们的测试数据:

```
<?php
require_once "PHPUnit/Autoload.php";

class ArticleDAOTest extends PHPUnit_Extensions_Database_TestCase
{
    public function getConnection() {
        $db = new PDO(
            "mysql:host=localhost;dbname=bulletproof", 
            "root", "password");
        return $this->createDefaultDBConnection($db, "bulletproof");
    }

    public function getDataSet() {
        return $this->createXMLDataSet("seed.xml");
    }
...
}
```

假设您和其他开发人员同意以下接口:

```
<?php
interface IArticleDAO
{
    public function getArticles($sectionId, $isHome);
}
```

第一个要写的测试也是最简单的。也就是说，我们首先针对一个部分不是主页的情况进行编码。我们期望得到只属于那个部门的文章。根据我的文件，其中一个部分是小工具部分。它的`id`为 1，包含以下条目:

*   安卓 vs iOS
*   安卓 vs Wp7
*   iOS 5

因此，当我们获得该部分的所有文章时，应该返回上述文章。这反映在如下所示的测试中(为了简洁，我省略了选择其他属性)。

```
<?php
require_once "PHPUnit/Autoload.php";
require_once "ArticleDAO.php";

class ArticleDAOTest extends PHPUnit_Extensions_Database_TestCase
{
...
    public function testGetArticlesNonHome() {
        $articleDAO = new ArticleDAO();
        $articles = $articleDAO->getArticles(1, false);
        $this->assertEquals(
            array(
                array("id" => 1, "title" => "Android vs iOS"),
                array("id" => 2, "title" => "Android vs Wp7"),
                array("id" => 3, "title" => "iOS 5")),
            $articles);
    }
...
}
```

`testGetArticlesNonHome()`方法首先实例化一个文章[数据访问对象](http://en.wikipedia.org/wiki/Data_access_object) (DAO)的新实例，就好像它已经在生产代码中使用了一样。DAO 负责封装和抽象对数据库的调用。然后调用 DAO 的`getArticles()`方法。这是我们想要测试的方法，所以结果被放在`$articles`变量中进行检查。

一旦我们在`$articles`中获得了方法调用的结果，它们就会与预期的结果进行比较。在这种情况下，我们期望 Gadgets 部分包含三篇文章。PHPUnit 自带的`assertEquals()`便利方法用于比较相等性。

## 通过测试

在 TDD 中，失败是第一步。如果您现在运行测试，它将会失败，因为我们还没有编写实现。

```
jeune@Miakka:~/Bulletproofing$ phpunit ArticleDAOTest.php
PHPUnit 3.6.7 by Sebastian Bergman.

PHP Fatal error:  Class 'ArticleDAO' not found in /home/jeune/ArticleTest/ArticleDAOTest.php on line 18
PHP Stack trace:
...
```

下一步是编写让我们通过测试的代码(即根据业务逻辑从数据库中获取文章)。让我们开始一个类，它实现了之前与其他开发人员商定的接口:

```
<?php
class ArticleDAO implements IArticleDAO
{
    public function getArticles($sectionId, $isHome) {
        $db = new PDO(
            "mysql:host=localhost;dbname=bulletproof", 
            "root", "password");

        $result = $db->query("SELECT a.id, a.title FROM features f LEFT JOIN articles a ON f.article_id = a.id AND f.section_id = 1");
        $articles = array();
        while ($row = $result->fetch(PDO::FETCH_ASSOC)) {
            $articles[] = $row;
        }
        $result->closeCursor();

        return $articles;
    }
}
```

不要忘记将上面的类包含到测试类的文件中。

现在再次运行测试，您应该会看到一个通过的测试:

```
jeune@Miakka:~/Bulletproofing$ phpunit ArticleDAOTest.php
PHPUnit 3.6.7 by Sebastian Bergman.

.

Time: 0 seconds, Memory: 6.50Mb

OK (1 test, 1 assertion)
```

## 修复 bug

您已经通过了测试，提交了代码，并将其发送给 QA。你拍拍自己的背，然后在早上 9 点开始闲逛。但是等等，QA 检测到一个 bug！

当一个部分没有设置任何文章时，它会从任何地方返回文章！为了确认，你需要写下你的`seed.xml`来拥有一个没有文章的部分。

```
<dataset>
 <table name="sections">
  <column>id</column>
  <column>name</column>
  <row>
   <value>1</value>
   <value>Gadgets</value>
  </row>
  <row>
   <value>2</value>
   <value>Programming</value>
  </row>
 </table>
...
<dataset>
```

针对某个部分没有文章的情况，编写一个新的测试来说明新的 bug:

```
<?php
class ArticleDAOTest extends PHPUnit_Extensions_Database_TestCase
{
...
    public function testGetArticlesNonHomeNoArticles() {
        $articleDAO = new ArticleDAO();
        $articles = $articleDAO->getArticles(2, false);
        $this->assertEquals(array(), $articles);
    }
...
}
```

的确，当我们运行测试时，实现失败了:

```
jeune@Miakka:~/Bulletproofing$ phpunit ArticleDAOTest.php
PHPUnit 3.6.7 by Sebastian Bergman.

F.

Time: 0 seconds, Memory: 6.75Mb

There was 1 failure:
1) ArticleDAOTest::testGetArticlesNonHomeNoArticles
Failed asserting that two arrays are equal.
--- Expected
+++ Actual
@@ @@
 Array (
+    0 => Array (...)
+    1 => Array (...)
+    2 => Array (...)
 )

/home/jeune/ArticleTest/ArticleDAOTest.php:33

FAILURES!
Tests: 2, Assertions: 2, Failures: 1.
```

错误消息说我们期望一个没有内容的数组，但是方法调用返回了一个有内容的数组。那明显是个 bug！通过修改代码来通过新的测试。

## 摘要

在本文中，我们看到了如何使用 PHPUnit 的数据库扩展用 PHP 编写数据库测试，这使得确保与数据库交互的代码是正确的变得更加容易。更重要的是，它给了我们信心，让我们在修复错误和改进/重构代码时做出改变。就像生活中的其他事物一样，软件也有很多变化。编写自动化测试可以帮助您更有效、更自信地使代码适应这些变化。

图片经由[塔蒂安娜·普欧波娃](http://www.shutterstock.com/gallery-77552p1.html) / [快门](http://www.shutterstock.com/)

## 分享这篇文章