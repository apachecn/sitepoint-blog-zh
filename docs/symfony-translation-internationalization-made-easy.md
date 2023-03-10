# Symfony 翻译:国际化变得容易

> 原文：<https://www.sitepoint.com/symfony-translation-internationalization-made-easy/>

如果你曾经开发过需要多种语言的网站，你就会知道这有多难。在 Symfony2 的翻译组件的帮助下，你可以很容易地创建国际化的网站。我将通过一些示例代码和对其 API 的一些讨论向您展示如何操作。

## 一个基本例子

让我们首先用下面的内容创建文件`translation.php`。

```
<?php
require 'vendor/autoload.php';
use SymfonyComponentTranslationTranslator,
   SymfonyComponentTranslationLoaderArrayLoader;

$translator = new Translator('fr_FR');
$translator->addLoader('array', new ArrayLoader());
$translator->addResource('array', array(
   'Hello World!' => 'Bonjour tout le monde!',
   ),
   'fr_FR'
);

echo $translator->trans('Hello World!') . "n";
echo $translator->trans('How are you?') . "n";
```

通过传递您想要使用的语言环境(在本例中为法语/法国)和可选的一个`MessageSelector`对象实例作为第二个参数来创建一个`Translator`对象。`MessageSelector`用于复数。如果没有传递，那么`Translator`构造函数会创建自己的。

然后设置用于加载语言字符串的加载程序。`addLoader()`方法接受一个名称(您可以给任何您喜欢的名称)和一个类型为`LoaderInterface`的对象。在这种情况下，我使用了实现接口的`ArrayLoader`。

`addResource()`方法添加翻译消息。第一个参数是赋予`addLoader()`的加载程序的名称，第二个参数是一个数组(因为我使用了`ArrayLoader`)。根据加载程序的不同，第二个参数可能会发生变化。第三个参数是语言字符串。

翻译查找是通过`trans()`方法完成的。如果没有找到匹配的翻译，则返回原始字符串。

翻译通常不是由程序员完成的。相反，它们是由专业翻译完成的。翻译可能没有编程知识，我们可能不想给他们代码。幸运的是，Symfony 可以使用不同的加载器来加载翻译消息列表。支持的加载器的完整列表有:`ArrayLoader`、`CsvFileLoader`、`MoFileLoader`、`XliffFileLoader`、`IcuDatFileLoader`、`PhpFileLoader`、`YamlFileLoader`、`IcuResFileLoader`、`PoFileLoader`、`IniFileLoader`和`QtTranslationsLoader`。

让我们看看如何使用`PoFileLoader`而不是数组来加载翻译字符串，这样对于翻译人员来说会更容易。

将`addLoader()`的第二个参数更改为一个`PoFileLoader`对象的实例。同样在`addResource()`方法中，传递 PO 文件所在的路径。

```
<?php
require 'vendor/autoload.php';
use SymfonyComponentTranslationTranslator,
   SymfonyComponentTranslationLoaderPoFileLoader;

$translator = new Translator('fr_FR');
$translator->addLoader('pofile', new PoFileLoader());
$translator->addResource('pofile', 'languages/po/fr_FR.po', 'fr_FR');
```

## 处理后备区域设置

您可以利用 PHP 的`Locale`类从 Accept-Language 头中获取请求的区域设置，或者利用 [Symfony 区域设置组件](http://symfony.com/doc/2.0/components/locale.html "The Locale Component (2.0) - Symfony")(它用一些附加功能扩展了 PHP 的类)，来动态设置区域设置。

```
<?php
$locale = Locale::acceptFromHttp($_SERVER['HTTP_ACCEPT_LANGUAGE']);
$translator = new Translator($locale);
```

但是如果语言环境不可用呢？您可以使用`setFallbackLocale()`方法设置一个后备语言环境。如果找不到某个语言环境，或者该语言环境存在但缺少翻译，那么`Translator`将寻找回退。

```
<?php
$translator->setFallbackLocale('fr');
```

同一种语言在不同的国家可能有所不同；以英语为例，你会注意到英国英语用 en_GB，美国英语用 en_US，等等。每种语言在不同的地区会有所不同。那么有多次回落怎么办？

假设在这种情况下，请求的区域设置是 fr_CA，而您没有 fr_CA 翻译。作为退路，你可以尝试从 fr_FR 获得翻译，如果没有，那么从 en_US，然后一般英语。

```
<?php
$translator->setFallbackLocale(array('fr_FR', 'en_US', 'en'));
```

有时，您可能希望将一个区域的消息分割成许多小单元。默认情况下，所有字符串都被添加到 messages 域中并在其中查找(这就是为什么我们不需要将域传递给`trans()`)。

翻译文件的命名约定是:*域*。*地区*格式，例如`messages.fr.po`、`navigation.fr.po`等。如果你想从一个特定的域获得翻译，那么你必须指定这个域作为`trans()`的第三个参数。回退只发生在同一个域中，不会发生在其他域中。

总的来说，这看起来像这样:

```
<?php
require 'vendor/autoload.php';
use SymfonyComponentTranslationTranslator,
   SymfonyComponentTranslationLoaderPoFileLoader;

$locale = Locale::acceptFromHttp($_SERVER['HTTP_ACCEPT_LANGUAGE']);
$translator = new Translator($locale);
$translator->setFallbackLocale(array('fr_FR', 'en_US', 'en'));

$translator->addLoader('pofile', new PoFileLoader());
$iterator = new FilesystemIterator("languages/po");
$filter = new RegexIterator($iterator, '/.(po)$/');
foreach($filter as $entry) {
    $name = $entry->getBasename('.po');
    list($domain, $locale) = explode('.', $name);
    $translator->addResource('pofile',
        $entry->getPathname(), $locale, $domain
    );
}

echo $translator->trans('Hello World!') . "n";
echo $translator->trans('How are you?') . "n";
echo $translator->trans('How are you?', array(), 'navigation') . "n";
```

如果你用地区来命名你的翻译文件，例如 fr_FR.po，fr_CA.po 等。，那么用语言来命名你的一些后援可能是个好主意。考虑一个来自比利时的人，他正在访问你的网站，Accept-Language 头请求 fr_BE。你可能没有 fr_BE，fr_FR 也不会匹配。回退到 fr.po 比切换到一种完全不同的语言，比如英语，要好。

可以从语言环境中提取一个仅命名为 fallback 的语言，并将其放在数组的开头，如下所示:

```
<?php
$fallbacks = array('fr_FR', 'en_US', 'en');
$locale = Locale::acceptFromHttp($_SERVER['HTTP_ACCEPT_LANGUAGE']);
array_unshift($fallbacks, substr($locale, 0, 2));
$translator->setFallbackLocale($fallbacks);
```

## 多元化

处理复数形式是国际化最困难的部分之一。要根据数字选择不同的翻译，可以使用`transChoice()`方法。下面举一个夸张的例子来演示它的用法:

```
<?php
$value = 1;
echo $translator->transChoice(
   '[-Inf, 0]There is nothing to delete|{1}Are you sure you want to delete this file|]1,10[ %count% files will be deleted|[10,Inf] Are you sure you want to delete all files',
  $value,
  array('%count%' => $value)
) . "n";
```

替代字符串由竖线字符分隔。改变`$value`的值，观察`Translator`如何改变输出。最初你应该得到消息“你确定你要删除这个文件”。如果您将`$value`更改为 2 到 9，您将得到消息“$value 文件将被删除”。如果您使用 10 个或更多，您会收到消息“您确定要删除所有文件吗”。如果`$value`小于 1，您将得到“没有要删除的内容”。

Symfony 翻译使用 [ISO 31-11 符号](http://en.wikipedia.org/wiki/ISO_31-11 "ISO 31-11 – Wikipedia, the free encyclopedia")，所以上面使用区间类来选择正确的句子。我们可以这样写范围:

```
[a, b] means a <= x <= b
[a, b[ means a <= x < b
]a, b] means  a < x <= b
]a, b[ means a < x < b
```

一个有助于更容易理解范围的记忆方法是看方括号朝哪个方向打开。如果方括号对着数字，那么它是包含的。

```
[3 means value is <= 3 (inclusive)
]3 means value is < 3 (exclusive)
```

还可以用大括号定义一组特定的值，例如{1，2，3}只匹配值 1、2 和 3。

## 在翻译格式之间转换

我用`ArrayLoader`开始这篇文章，因为它可能是大多数开发人员最容易开始的。但后来我换成了`PoFileLoader`，因为 PO 文件对翻译来说更容易。如果你有一组数组形式的翻译，或者甚至是[另一种格式，比如 YAML](https://www.sitepoint.com/using-yaml-in-php-projects/ "Using YAML in Your PHP Projects") ，并且想要转换它们，该怎么办？您可以使用 dumpers 在任何 Symfony 加载程序的一种格式和另一种格式之间进行转换。

```
<?php
require 'vendor/autoload.php';
   SymfonyComponentTranslationLoaderYamlFileLoader,
   SymfonyComponentTranslationMessageCatalogue,
   SymfonyComponentTranslationDumperPoFileDumper;

$loader = new YamlFileLoader();
$iterator = new FilesystemIterator("languages/yaml");
$filter = new RegexIterator($iterator, '/.(yml)$/');
foreach($filter as $file) {
    $name = $file->getBasename('.yml');
    list($domain, $locale) = explode('.', $name);
    $array = $loader->load($file->getPathname(), $locale, $domain);
    $catalogue = new MessageCatalogue($locale);
    $catalogue->addCatalogue($array);

    $dumper = new PoFileDumper();
    $dumper->dump($catalogue, array('path'=> __DIR__ . '/languages/pofile'));
}
```

只需将 loader 替换为您用于输入的合适的 loader，将 dumper 替换为您需要的任何输出。

## 摘要

我们已经介绍了如何翻译字符串，如何使用后备语言环境，如何处理多元化，以及如何利用转储程序。我希望这篇教程能帮助你从国际化开始，在 Symfony 翻译组件的帮助下，国际化变得很简单。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章