# jadephi 简介

> 原文：<https://www.sitepoint.com/introduction-jadephp/>

有几十种模板引擎，其中最著名的有 [Smarty](http://www.smarty.net/) 、 [Twig](http://twig.sensiolabs.org/) (用于即将发布的 Drupal 版本)和 Blade(Laravel 的默认设置)——当然还有普通的 PHP。具体来说，抛开 PHP 不谈，Ruby / Ruby on Rails 的 [eRuby / ERB](http://en.wikipedia.org/wiki/ERuby) 和 [Haml](http://haml.info/) ，Javascript 有几十个流行的选择，包括[小胡子](http://mustache.github.io/)、[手柄](http://handlebarsjs.com/)、[霍根](http://twitter.github.io/hogan.js/)和 [EJS](http://embeddedjs.com/) 。有些语法略有不同，有些则更明显。

与大多数引擎显著不同的一个是 [Jade](http://jade-lang.com/) ，这是一个通常与 Javascript 应用程序相关联的引擎——例如，Express for Node.js 支持它的开箱即用。在这篇文章中，我要看的是玉；或者更确切地说是 PHP 端口 [JadePHP](https://github.com/everzet/jade.php) 。

## 哈姆和杰德

谈论 Jade 而不提及 Haml 是一种疏忽，Jade 的灵感来自 Haml——事实上，在 PHP 中使用 Haml 的有[和](http://phaml.sourceforge.net/) [几个](https://code.google.com/p/phamlp/) [库](http://phphaml.sourceforge.net/)。Jade 分享了它的总体理念，即让模板化变得“美丽”，并使用作者描述的模板化“俳句”。不管实际上*意味着什么*，不可否认 Haml 和 Jade 确实有一些共同的特征，这些特征使它们与大多数模板语言有着本质的不同。

## 有什么区别？

大多数模板引擎包括编写目标标记，并用占位符和/或基本逻辑“注入”它——从某种意义上说，是一个超集。Jade 仍然有占位符和逻辑，但也为编写类似 XML 的元素提供了一种速记方式。一般来说，这意味着 HTML，尽管你也可以把它用于 RSS 和 XML 本身。

事实上，如果你愿意，你可以把 Jade 作为 HTML 的简写，而不用利用它更“传统”的模板特性。

## 如何使用存储库

相当令人沮丧的是，该代码目前无法通过 Composer 获得[——尽管如果有人有一两个小时的时间，打包它应该是一个足够简单的任务。然而，你可以通过克隆库并`include`ing 或`require`ing 包含的`autoload.php.dist`(Github 库包含 Symfony 的`UniversalClassLoader`)来让它工作。](https://github.com/everzet/jade.php/issues/8)

这里有一个例子，改编自项目的 README，假设存储库已经下载到一个名为`jade`的目录中:

```
require('./jade/autoload.php.dist');

use Everzet\Jade\Dumper\PHPDumper,
        Everzet\Jade\Visitor\AutotagsVisitor,
        Everzet\Jade\Filter\JavaScriptFilter,
        Everzet\Jade\Filter\CDATAFilter,
        Everzet\Jade\Filter\PHPFilter,
        Everzet\Jade\Filter\CSSFilter,
        Everzet\Jade\Parser,
        Everzet\Jade\Lexer\Lexer,
        Everzet\Jade\Jade;

$dumper = new PHPDumper();
$dumper->registerVisitor('tag', new AutotagsVisitor());
$dumper->registerFilter('javascript', new JavaScriptFilter());
$dumper->registerFilter('cdata', new CDATAFilter());
$dumper->registerFilter('php', new PHPFilter());
$dumper->registerFilter('style', new CSSFilter());

// Initialize parser & Jade
$parser = new Parser(new Lexer());
$jade   = new Jade($parser, $dumper);

$template = __DIR__ . '/template.jade';

// Parse a template (both string & file containers)
echo $jade->render($template); 
```

这将编译文件`template.jade`和`echo`的内容。

你实际上在哪里使用它取决于你的工作流程，你是否在使用一个框架，等等。或许，您可以使用诸如[看守](https://github.com/facebook/watchman)、[守卫](https://github.com/guard/guard)或[资源看守](https://github.com/jasonlewis/resource-watcher)之类的服务来监视文件系统对您的 Jade 模板的更改，并在开发过程中的适当时候编译它们。

## 简单的例子

让我们看一个简单的例子，它展示了一个完整的——如果是基本的——HTML 页面，只有两个变量，没有逻辑(还没有！)

```
!!! 5
html(lang="en-us")

    meta(charset="utf-8")
    meta(http-equiv="X-UA-Compatible", content="IE=Edge;chrome=1")

    title(dir="ltr")= pageTitle

    meta(name="viewport", content="width=device-width, initial-scale=1.0")

    link(rel="stylesheet", media="screen", href="/css/styles.css")

    body
    header
    h1 My Jade Application
    div#content
    div.inner
        =$bodyContent

    script(data-main="js/main.js", src="js/libs/require.js") 
```

> **重要的**:你需要使用*两个空格*来缩进。这是 Jade PHP 目前唯一理解的方法，使用不同的东西会导致错误或无效标记。

很明显，Jade 看起来与您熟悉的 HTML 非常不同。首先，没有尖括号和结束标记。也没有花括号、双花括号或任何标记要注入模板的变量的常用方法。(也就是说，你*可以*使用双花括号语法，但本质上它不是 Jade 的一部分。)

它展示的是一种非常简洁的生成标记的方法。让我们看看产生的 HTML:

```
<!DOCTYPE html>
<html lang="en-us">
    <meta charset="utf-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=Edge;chrome=1" />
    <title dir="ltr"><?php echo pageTitle ?></title>
    <meta name="viewport" content="width=device-width" initial-scale="1.0" />
    <link rel="stylesheet" media="screen" href="/css/styles.css" />
    <body>
        <header>
            <h1>My Jade Application</h1>
        </header>
        <div id="content">
            <div class="inner">
                <?php echo $bodyContent ?>
            </div>
        </div>
        <script data-main="js/main.js" src="js/libs/require.js"></script>
    </body>
</html> 
```

让我们浏览一下 Jade 模板中的关键行，了解一下 HTML 的简写是如何工作的。

`!!! 5`是 HTML5 doctype 的简称。这是你唯一能看到三重感叹号语法的地方。你也可以用`!!! xml`得到`<?xml version="1.0" encoding="utf-8" ?>`，对于过渡你可以用`!!! transitional`得到`<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">`或者默认`!!! default`给你`<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "https://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">`。

> 注意；在 Jade 的最新 Javascript 版本中，`!!! 5`声明被替换为`doctype html`；鉴于 Github 回购的不活跃，JadePHP 有可能也会效仿，尽管可能性不大。

一个 HTML 标签由它的名字单独指定，不需要关闭它；例如:

```
body
  header 
```

…如果您就此打住，会导致:

```
<body>
    <header></header>
</body> 
```

请注意如何使用缩进来表示文档的结构。

您可以将标签的内容放在名称后面，中间留一个空格:

```
h1 My Jade Application 
```

…变成:

```
<h1>My Jade Application</h1> 
```

如果您想要将大块内容拆分成多行，请使用管道“|”字符:

```
p 
  | Curabitur blandit tempus porttitor. Vivamus sagittis lacus vel augue laoreet rutrum faucibus dolor auctor. 
  | Aenean eu leo quam. 
  | Pellentesque ornare sem lacinia quam venenatis vestibulum. 
```

这将编译为:

```
<p>Curabitur blandit tempus porttitor. Vivamus sagittis lacus vel augue laoreet rutrum faucibus dolor auctor. Aenean eu leo quam. Pellentesque ornare sem lacinia quam venenatis vestibulum.</p> 
```

您可以使用类似于 CSS 选择器的格式向 HTML 元素添加 id 和类:

```
div#content
  div.inner 
```

…结果是:

```
<div id="content">
    <div class="inner"> 
```

其他属性如`src`、`href`、`lang`、`media`等可以用括号指定:

```
html(lang="en-us") === <html lang="en-us">

link(rel="stylesheet", media="screen", href="/css/styles.css") === <link rel="stylesheet" media="screen" href="/css/styles.css" /> 
```

等号用于变量替换。正如你在上面看到的，当你编译一个 Jade 模板时，它会像这样转换:

```
= $pageTitle 
```

…转换成以下内容:

```
<?php echo $pageTitle ?> 
```

## 添加一些逻辑

可以用破折号注入基本的条件逻辑。这里有一个具体的例子:

```
header
    h1= $pageTitle
    - if ($loggedIn):
    p.greeting Welcome back!
    - else:
    a(href="/login") Please login
    - endif; 
```

当您编译模板时，这将导致以下结果:

```
<header>
    <h1><?php echo $pageTitle ?></h1>
    <?php if ($loggedIn) ?>
    <p class="greeting">Welcome back!</p>
    <?php else ?>
    <a href="/login">Please login</a>
    <?php endif ?>
</header> 
```

迭代遵循非常相似的路线:

```
ul
  - foreach ($items as $item):
  li= $item 
```

## 过滤

您可以使用过滤器来获取文本块，并以某种方式对其进行处理，例如:

```
:php
    | $value = 10;
    | $computed_value += 100;
    | print $computed_value; 
```

…将评估为:

```
<?php
    $value = 10;
    $computed_value += 100;
    print $computed_value;
?> 
```

也许更有用的是 javascript 和 CSS 过滤器，例如:

```
:style
| body {
|   background: yellow;
| } 
```

…将评估为:

```
<style type="text/css">
body {
  background: yellow;
} 
```

您可以按如下方式设置这些过滤器(有关这些声明的上下文，请参见上面的示例代码):

```
$dumper->registerFilter('javascript', new JavaScriptFilter()); 
$dumper->registerFilter('php', new PHPFilter());
$dumper->registerFilter('style', new CSSFilter()); 
```

第一个参数对应于您用来“标记”模板中的文本的文本，它以冒号为前缀；所以在上面的例子中，你可以分别使用`:javascript`、`:php`和`:style`。

通过实现`Everzet\Jade\Filter\FilterInterface`，你甚至可以定义自己的过滤器。

## 为什么要用玉？

关于使用哪种模板语言的争论最终是徒劳的。虽然您可以提出某些基准，在某些情况下哪些性能优于其他基准，哪些是专门为某些环境设计的，比如客户端还是服务器端，但最终的选择往往是非常个人化的，取决于您觉得在什么环境下编写脚本更合适。我无意煽风点火，也不想卷入关于哪个是“最好的”编程语言的争论。

杰德的速记方法并不适合所有人。有些人会认为它更容易阅读，有些人会强烈反对。然而，如果你认为这是一种有价值的方法，那么这就是选择它的一个令人信服的理由。

您可能决定使用 Jade 的另一个原因——考虑到许多模板选项——是如果您在不同的技术之间交替使用。如果您经常在 Node.js 和 PHP 开发之间切换，那么保持一致性是有一定逻辑的。如果一个引擎可以用于多种语言，为什么要完全使用其他的呢？

## 摘要

在本文中，我研究了 JadePHP，它是主要面向 Javascript 的模板引擎 Jade 的一个端口。我已经给了你一些如何使用它的建议，以及一些关于为什么你会想使用它的想法。你打算试一试吗，或者它对你来说似乎不必要的简洁？请在评论中告诉我你对它的看法。

## 分享这篇文章