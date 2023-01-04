# 看看 HHVM 的 PHP 替代者 Hack

> 原文：<https://www.sitepoint.com/look-hack-php-replacement-hhvm/>

您可以使用之前创建的[漫游框](https://www.sitepoint.com/hhvm-hack-part-1/)来运行本文中的代码片段。

### 为什么是类型？

在本文的第一部分，我们已经看到 HACK 实际上是静态类型的。这意味着您必须为应用程序中的所有变量提供类型。提醒一下，PHP 是动态类型的，所以你永远不需要输入你的变量，尽管你可以使用[类型提示](http://www.php.net/manual/en/language.oop5.typehinting.php)作为函数参数。

但是等等，这是否意味着你必须为你的应用程序的每一个变量提供类型？不完全是，我们会看到细节。

脸书的代码库由数亿行代码组成，在他们可以切换到 HACK 之前到处添加类型将是一个真正的负担。所以他们采用了“渐进类型化”:HACK 只期望“严格”模式下的类型。在非严格模式中，类型只在它们出现的地方被考虑。

进入严格模式就像将 HACK start 标签从`<?hh`切换到`<?hh // strict`一样简单。

即使在严格模式下，你也不需要注释所有的变量。那是因为 HACK 足够聪明，可以推断出局部变量类型。只有类属性、函数参数和返回值才需要类型注释。否则，我会建议在有助于理解代码时注释局部变量。

让我们看一个例子:

```
<?hh // strict

require "/vagrant/www/xhp/php-lib/init.php";

// ...

function add(int $a, int $b): int {
    return $a + $b;
}

// ERROR(calling "add()" on l.17) : Argument 2 passed to add() must be an
// instance of int, string given
echo <p>add(1, "a") = {add(1, "a")}</p>;

// ERROR(calling "add()" on l.22) : Argument 2 passed to add() must be an
// instance of int, string given
function add_array(array<int> $a): int {
    return array_reduce($a, "add", 0);
}

echo <p>add_array([1, "a"]) = {add_array([1, "a"])}</p>;
```

这一部分的示例代码位于`www/type-checker/index.php`，您可以通过将浏览器指向`http://localhost:8080/type-checker/`来查看它的输出。

第一个错误消息并不奇怪:调用`add(1, "a")`会产生一个错误，因为`add()`期望第二个参数是一个整数。

第二个错误信息更出乎意料:错误不是通过调用`add_array([1, "a"])`生成的。实际上是对`add_array()`内部的`add(1, "a")`的调用产生了错误！人们可能已经预料到传递`[1, "a"]`会触发一个错误，因为它不是一个`array<int>`。

问题是，为了不影响性能，HHVM 运行时检查是稀疏的:它不迭代对象。在这一点上，你可能会质疑 HACK 类型系统的有用性！但是不要担心，有一个简单的答案，即“类型检查器”:它可以捕捉任何类型不匹配，包括上一个例子中的不匹配。不要在 HHVM 库中寻找它，它还没有被脸书发布。

类型检查器被实现为一个服务器，它监视您的文件的更改。每当它检测到一个变化，它将扫描修改文件及其依赖关系的错误。错误会被实时报告，因此您甚至不必运行代码。它被设计成即使在 FB 的规模下也能快速工作。

您现在应该确信类型系统工作得很好，但是它有什么好处呢？它允许实时捕捉开发人员的错误，产生更高效的代码:PHP `add()`函数将首先检查`$a`和`$b`(即`string`、`null`、…)的类型，可能将它们转换成数字，然后才执行加法。而 HACK 上面的`add()`函数将两个非空整数相加，这在汇编语言中是一个非常快的操作(由 HHVM JIT 生成)。

如果作为一名开发人员，您已经在使用 PHP 类型提示和 [PHPDoc](http://www.phpdoc.org/) 注释，那么切换到严格模式应该是显而易见的。你的代码会变得更安全、更快——注意，一些现有的 QA 工具，如[审查员](https://scrutinizer-ci.com/)已经在使用类型推断来检查你的代码，尽管它们不是实时的。

如果您使用 PHP 主要是因为它的动态类型特性，那么您可能希望坚持非严格模式。

### 用户属性

在过去的几年中，注释在 PHP 世界中的使用显著增加。对于那些不熟悉注释的人来说，注释是可以添加到类、接口、特征、变量和函数/方法中的元数据。

[教条 ORM](http://www.doctrine-project.org/) 可能是第一批广泛使用注释的 PHP 项目之一。以下是来自[学说文件](http://docs.doctrine-project.org/projects/doctrine-orm/en/latest/reference/basic-mapping.html#property-mapping)的模型配置示例:

```
<?php
/** @Entity */
class Message
{
    /** @Column(type="integer") */
    private $id;
    /** @Column(length=140) */
    private $text;
    /** @Column(type="datetime", name="posted_at") */
    private $postedAt;
}
```

与许多其他语言不同，PHP 没有内置的注释支持。然而，[教条注释库](https://github.com/doctrine/annotations)被广泛用于从文档块中提取元数据。早在 2011 年，一个提议在 PHP 中内置注释支持的 [RFC](https://wiki.php.net/rfc/annotations-in-docblock) 就被拒绝了。

用户属性是注释的脸书实现。它们包含在`<<` … `>>`中，其语法与教义注释略有不同:

```
<?hh

require "/vagrant/www/xhp/php-lib/init.php";

<< UA('klass', ['type' => 'class']) >>
class klass {
    protected $prop;

    << UA(['type' => 'function']) >>
    public function funktion(<< Argument >> $arg) {
    }
}

$rc = new ReflectionClass(klass);
$rm = $rc->getMethod('funktion');
$ra = $rm->getParameters()[0];

// On class
// array ( 'UA' => array ( 0 => 'klass', 1 => array ( 'type' => 'class', ), ), )
// On method
// array ( 'UA' => array ( 0 => array ( 'type' => 'function', ), ), )
// On argument
// array ( 'Argument' => array ( ), )
echo <div><h1>User annotations</h1>
    <h2>On class</h2><p>{var_export($rc->getAttributes(), true)}</p>
    <h2>On method</h2><p>{var_export($rm->getAttributes(), true)}</p>
    <h2>On argument</h2><p>{var_export($ra->getAttributes(), true)}</p></div>;
```

您应该注意到，不出所料，用户属性是从反射 API 访问的。还要注意，对类属性进行注释的支持仍有待实现。

这一部分的示例代码位于`www/attributes/index.php`，您可以通过将浏览器指向`http://localhost:8080/attributes/`来查看它的输出。

### XHP

到目前为止，您应该已经对 XHP 有了初步的了解，因为从本文的第一个代码示例中我们就一直在使用它。让我引用[脸书](https://github.com/facebook/xhp)更完整的定义“XHP 是一个 PHP 扩展，它增加了语言的语法，使得 XML 文档片段成为有效的 PHP 表达式。”。注意，XHP 可以作为 PHP 扩展使用，HHVM 有本地支持。

有了 XHP，你可以在普通 PHP 中使用`"<h1>$hello</h1>"`地方使用`<h1>{$hello}</h1>`。虽然前面的例子微不足道，但 XHP 还有更多:

*   它会验证你的标记，这样你就不会写无效的 HTML——想想丢失的结束标签，参数名中的拼写错误…
*   它提供了某种程度的上下文转义——当引擎知道你在渲染什么时，它可以适当地对 HTML 和属性值进行转义，以防止 [XSS 攻击](http://en.wikipedia.org/wiki/Cross-site_scripting)，
*   您可以通过扩展或包装现有标签来编写自己的标签。

让我们看一个例子:

```
<?hh

require "/vagrant/www/xhp/php-lib/init.php";

$examples = [
    'hello'        => 'Hello HHVM / HACK',
    'promotion'    => 'Constructor argument promotion',
    'collections'  => 'Collections',
    'types'        => 'Types and Generics',
    'type-checker' => 'The type checker',
];

// The XHP validation should be disabled for better performance in production
//:x:base::$ENABLE_VALIDATION = false;

class :tuto:examples extends :x:element {
    // examples, current are required attributes
    attribute array examples @required;
    attribute string current @required;

    // forbid to explicitly add children
    children empty;

    protected function render() {
        $select = <select onchange="window.location.href=window.location.pathname + '?ex=' + this.value"/>;
        foreach ($this->getAttribute('examples') as $name => $label) {
            $selected = $name === $this->getAttribute('current');
            $child = <option selected={$selected} value={$name}>{$label}</option>;
            $select->appendChild($child);
        }
        return $select;
    }
}

$folder = preg_replace('/[^-_a-z0-9]/', '',isset($_GET['ex']) ? $_GET['ex'] : '');

function getTheCode($folder) {
    // ...
}

echo <html>
    <head><title>"XHP generated index"</title></head>
    <body>
        <tuto:examples examples={$examples} current={$folder} />
        {getTheCode($folder)}
    </body></html>;
```

本节的完整示例代码位于`www/hhxhp/index.php`，您可以通过将浏览器指向`http://localhost:8080/hhxhp/`来查看其输出。

在这个例子中，我们首先定义一个定制的`<tuto:examples>`标签，它将呈现一个`<select>`标签，这是通过声明一个类`:tuto:examples`来完成的。我们的定制标签需要两个属性，`examples`和`current`，但是不允许有子属性(`children empty;`)。

因为我们扩展了基本的`:x:element`，我们应该覆盖`render()`方法来返回我们的自定义标记作为 XHP。

脸书使用 XHP 语言作为其 UI 库的基础，最终也可能开源。

### 异步代码执行

在看过 HHVM 报告中的一些测试后，我计划写一个关于异步代码执行的章节。然而，我不能拿出一个工作的例子。这可能是因为我对这个话题了解不多，或者是因为脸书还没有发布所有相关的代码。一旦脸书发布了一些文档，我可能会写这方面的内容。

### 其他功能

有很多关于 HHVM 生态系统的东西没有在这篇文章中涉及，因为我必须选择包含什么，也因为脸书还没有发布所有的代码和文档。

值得一提的是最近对 FastCGI 和 T2 集成调试器的支持。

脸书还展示了“FBIDE”，这是一个基于网络的 IDE，具有自动完成、语法高亮、协作编辑等功能。我们可以期待它在稍后的某个时间可用。

### 外部资源

你可以在脸书团队的一些演讲和幻灯片中找到更多的信息，我用它们来准备这篇文章。我第一次听说 HACK 是从 Keith Adams 的[“认真对待 PHP”](http://www.infoq.com/presentations/php-history)演讲和 Julien Verlaguet 的[演讲中。](http://www.youtube.com/watch?v=gKWNjFagR9k)[萨拉·戈莱蒙的](https://twitter.com/SaraMG) [漂亮的](http://dl.hhvm.com/resources/conf/2013/TrueNorth-Scaling_with_HipHop.pdf) [幻灯片](http://dl.hhvm.com/resources/conf/2014/Hacking%20with%20HHVM.pdf)对我也很有帮助。

### 结论

脸书致力于为 HHVM 提供与 PHP 同等的特性。到去年年底，HHVM 已经能够通过 20 多个最流行的 PHP 框架的 98.5%的单元测试。从那以后，T2 的情况略有改善。

时至今日，HHVM 执行 PHP 代码的速度比 PHP 快，但消耗的内存更少。当最终实现对等时，这将是对 HHVM 有利的一个重大优势。最重要的是，您可以开始引入 HACK，在类型检查器的帮助下获得更高的性能并提高代码安全性——请记住，由于逐步键入以及 HACK 和 PHP 可互操作的事实，您不必一次转换整个代码库。

从现在起的几个月内，我们可以期待从脸书得到更多的文档和工具。你甚至可以通过参与 github 上的项目来提供帮助，这里还有一个 T2 奖金项目。

PHP 社区报告的问题之一可能是采用的一个主要障碍是缺乏对 PECL 扩展的支持。为了减轻这一点，脸书有一个工具，可以为 HHVM 目标自动编译 PHP 扩展；尽管成功率远非 100%。另一件有帮助的事情是，为 HHVM 开发一个扩展比为 PHP 开发要容易得多。

事实上，HHVM 只得到脸书的支持，而且在出兵 HHVM 之前需要签署一份合作协议，这对其他人来说似乎有些麻烦。

我个人认为公平的竞争对 PHP 的未来是一件好事。

最后，我要感谢脸书团队所做的出色工作，并对其进行开源。如果你想在未来看到更多关于 HHVM 和 HACK 的文章，请不要犹豫，通过在下面添加评论来建议主题。

## 分享这篇文章