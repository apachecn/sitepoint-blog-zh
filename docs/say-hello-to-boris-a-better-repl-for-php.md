# 向 Boris 问好:PHP 的更好的 REPL

> 原文：<https://www.sitepoint.com/say-hello-to-boris-a-better-repl-for-php/>

作为 web 开发人员，我们知道浏览器提供的 JavaScript 控制台在测试代码片段时的重要性。我们不需要编写整个 HTML 页面和 JavaScript 代码来验证我们编写的小例程的功能或逻辑。相反，我们只需在控制台中运行表达式，并立即看到结果。

类似地，REPL (Read-Eval-Print 循环)是编程语言的控制台，我们可以在其中一行一行地编写代码，看看它做了什么。PHP 有 REPL；如果您还没有使用过它，请在终端中运行`php –a`。这将把你带到交互式 PHP 提示符下，在这里你可以输入你的代码。

```
$ php -a
Interactive shell

php > echo "Hello REPL";
Hello REPL
```

所有编程语言复制器的工作方式本质上都是一样的。有一个无限循环，它主要处理三个任务:读取在提示符下输入的表达式的 read 任务，解析并执行表达式的 eval 函数，以及显示操作结果的 print 函数。

PHP 的 REPL 做得非常好，尽管它有一些限制。例如，它不能很好地处理错误；每当出现致命错误时，REPL 就会返回控制台。与其他语言相比，PHP 默认 REPL 的另一个缺点是它不将表达式的结果输出到控制台；我们必须明确地告诉它回显或打印结果。大多数其他 REPLs 总是将表达式的结果输出到控制台。

因此，鲍里斯试图解决这些问题以及其他问题。Boris 是一个小型 PHP REPL 库，由 [Chris Corbyn](https://twitter.com/d11wtq "Chris Corbyn") 用 PHP 编写。它处理致命错误的效率更高，因为它不会在错误发生时退出控制台。相反，它在控制台中报告错误详细信息和堆栈跟踪。Boris 还输出对表达式求值的结果。

## 装置

Boris 托管在 GitHub 上，所以使用 Git 很容易安装。请注意，Boris 需要 PCNTL 扩展，因此如果您的机器上还没有该扩展，那么您可以按照这些步骤安装它。然后，把鲍里斯克隆到你的机器上。

```
$ git clone git://github.com/d11wtq/boris.git
```

这将把整个 Boris 库克隆到您当前位置的一个新目录`boris`中，其中包含一个可执行的 PHP 脚本来加载和运行 Boris。(Boris 也可以使用 Composer 安装，稍后我将向您展示。)

要开始使用 Boris，进入目录并运行脚本。

```
$ cd boris
$ ./bin/boris
```

这将带您到 Boris 提示符。就像默认的 PHP 提示符一样，我们可以在这里输入代码并运行。让我们尝试一些简单的表达。

```
[1] boris> $timezone = new DateTimeZone("America/New_York");
→ object(DateTimeZone)#5 (0) {
}

[2] boris> $date =  new DateTime("now", $timezone);
→ object(DateTime)#6 (3) {
  ["date"]=>
  string(19) "2013-03-29 23:56:25"
  ["timezone_type"]=>
  int(3)
  ["timezone"]=>
  string(16) "America/New_York"
}
```

表达式的结果总是返回到控制台，这有助于我们在值/对象创建后立即检查它们。

为了更容易访问，您可以在您的`.bashrc`(或类似的)中添加 Boris 脚本的路径，并重新加载您的 shell 环境。然后，您可以在终端的任何地方运行`boris`,并被带到 Boris 提示符下。

```
export PATH="path/to/boris/bin:$PATH"
```

## 定制 Boris

Boris 的一个重要特性是能够定制其特性。如果您查看`./bin/boris`文件的内容，您会发现它只是一个初始化`Boris`实例的 PHP 文件。我们可以通过在构造函数中传递默认提示来改变它。

```
$boris = new BorisBoris('app $ ');
$boris->start();
```

但是定制不仅仅局限于提示。我们还可以定义一些在 REPL 内部可用的默认变量，如下所示:

```
$boris->setLocal("myVar", "Value");
```

然后我们可以用`$myVar`来引用这个值，这将帮助我们避免每次使用 Boris 时都要定义各种变量。

默认情况下，Boris 使用`var_dump()`显示结果，但是我们可以使用自己的检查器定制 REPL 的输出。如果你喜欢其他格式，创建一个实现`Inspector`接口的类，它有一个叫做`inspect()`的方法。

```
class MyInspector implements Inspector {
    public function inspect($variable) {
        ob_start();
        print_r($variable);
        return trim(ob_get_clean());
    }
}

$boris->setInspector(new MyInspector());
```

## 申请中的 REPL

Boris 也可以很容易地嵌入到您的独立 PHP 应用程序或库中。例如，让我们使用 Boris 和 Guzzle 创建一个命令行 web 服务客户端。Guzzle 是一个功能强大的库，用于创建 Web 服务客户端，并提供了一个简单的接口，用于以编程方式发出 API 请求。

首先，创建一个`composer.json`文件来设置所需的库。

```
{
    "require": {
        "d11wtq/boris": "dev-master",
        "guzzle/guzzle": "~3.1"
    }
}
```

然后使用 Composer 安装这些依赖项。这会将 Boris、Guzzle 和它们的依赖项下载到当前目录下的一个`vendor`文件夹中。

```
$ composer.phar install
```

接下来，创建一个可执行脚本(我将其命名为`wsclient`)来启动我们的应用程序。

```
#!/usr/bin/env php
<?php
// composer autoloader
require "vendor/autoload.php";
use GuzzleHttpClient;

// Initialize Boris with our own prompt.
$boris = new BorisBoris("wsclient > ");

// Guzzle client with our API base URL
$client = new Client("http://myapplication/api");

// We don't want to create the Client object every time.
$boris->setLocal("client", $client);

// Default inspectors are bit noisy. Let's override that.
$boris->setInspector(new GuzzleInspector());

// Start the REPL
$boris->start();
```

我们已经包括了 Composer 提供的自动加载器，这使事情变得更容易。然后，我们初始化了 Boris，并为我们的 web 服务显式创建了一个 Guzzle 客户端，这样我们就不需要一遍又一遍地这样做了。通过用`setLocal()`将客户端对象设置为局部变量，使其在 REPL 内部可用。我们对检查这里的变量和对象不感兴趣，所以我们用`GuzzleInspctor`覆盖了默认的检查器。您可以创建一个来帮助您调试来自服务器的响应，但是我为这个例子创建的这个看起来像这样:

```
<?php
class GuzzleInspector implements BorisInspector
{
    public function inspect($var) {
        ob_start();
        echo (bool) $var;
        return trim(ob_get_clean());
    }
}
```

使脚本可执行，然后启动 REPL，尝试一些东西。

```
$ chmod +x wsclient
$ ./wsclient

[1] wsclient > $request = $client->get("/user")->setAuth("user", "pass");
 → true
[2] wsclient > $response = $request->send();
 → true
[3] wsclient > echo $response->getBody();
//{"login":"user","id":123000,"avatar_url":"...
```

## 结论

如果您曾经使用过 Python、Ruby、Scala、OCaml 或任何其他提供这种功能的语言，我不需要解释 REPL 的真正威力。初次学习语言时，以及测试和调试各种代码片段时，REPL 都是一个很好的工具。

像许多其他主流语言一样，PHP 有一个 REPL，但它也有一些缺点，尤其是在错误处理方面。鲍里斯是一个小图书馆，试图填补它的空白。更有趣的是，您可以使用 Boris 轻松地为您的应用程序创建一个 CLI。

虽然 Boris 确实很酷，有时也很有用，但它也有自己的一些局限性。Boris 依赖于操作系统的分叉能力，所以不能在 Windows 中使用。此外，截至目前，它还不是一个防弹应用程序。有一些问题需要解决，更多的功能，比如函数名和类方法的自动完成，将会很方便。

我希望您会发现这个库的许多其他用例；欢迎在下面的评论区分享它们。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章