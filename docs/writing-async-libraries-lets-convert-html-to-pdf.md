# 编写异步库——让我们将 HTML 转换成 PDF

> 原文：<https://www.sitepoint.com/writing-async-libraries-lets-convert-html-to-pdf/>

*这篇文章由 [Thomas Punt](https://www.sitepoint.com/author/tpunt/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

* * *

我几乎不记得有哪次会议没有讨论异步 PHP 的话题。我很高兴这些天它被如此频繁地提及。不过，这些演讲者没有说出一个秘密…

> 制造异步服务器、解析域名、与文件系统交互:这些都是容易的事情。制作自己的异步库很难。而且是你花大部分时间的地方！

![Vector image of parallel racing arrows, indicating multi-process execution](img/27a03d396b9c2711e586b84222fe9cc1.png)

这些简单的事情之所以简单，是因为它们是概念的证明——让 [async PHP 与 NodeJS](https://philsturgeon.uk/php/2013/11/12/benchmarking-codswallop-nodejs-v-php/) 竞争。你可以看到他们早期的界面是多么的相似:

```
var http = require("http");
var server = http.createServer();

server.on("request", function(request, response) {
    response.writeHead(200, {
        "Content-Type": "text/plain"
    });

    response.end("Hello World");
});

server.listen(3000, "127.0.0.1"); 
```

> 此代码已使用节点`7.3.0`进行了测试

```
require "vendor/autoload.php";

$loop = React\EventLoop\Factory::create();
$socket = new React\Socket\Server($loop);
$server = new React\Http\Server($socket);

$server->on("request", function($request, $response) {
    $response->writeHead(200, [
        "Content-Type" => "text/plain"
    ]);

    $response->end("Hello world");
});

$socket->listen(3000, "127.0.0.1");
$loop->run(); 
```

> 这段代码用 PHP `7.1`和`react/http:0.4.2`进行了测试

今天，我们将探讨几种方法，让您的应用程序代码在异步架构中运行良好。不要担心——您的代码仍然可以在同步架构中工作，所以您不必放弃任何东西来学习这项新技能。除了一点时间…

> 你可以在 Github 上找到这个教程[的代码。我已经用 PHP `7.1`和最新版本的 ReactPHP 和 Amp 测试过了。](http://github.com/asyncphp/paper)

## 有希望的理论

异步代码有一些常见的抽象。我们已经看到了其中之一:回调。回调，顾名思义，描述了它们如何处理缓慢或阻塞的操作。同步代码充满了等待。提出要求，等待事情发生。

因此，相反，异步框架和库可以使用回调。请求一些东西，当它发生时:框架或库将回调你的代码。

在 HTTP 服务器的情况下，我们不会先发制人地处理所有请求。我们也不会坐等请求的出现。我们简单地描述了当请求发生时应该调用的代码。[事件循环](https://www.sitepoint.com/an-introduction-into-event-loops-in-php)负责剩下的事情。

第二个常见的抽象是承诺。回调是等待未来事件的钩子，而承诺是对未来值的引用。它们看起来像这样:

```
readFile()
    ->then(function(string $content) {
        print "content: " . $content;
    })
    ->catch(function(Exception $e) {
        print "error: " . $e->getMessage();
    }); 
```

这比单独的回调要多一点代码，但这是一种有趣的方法。我们等待某事发生，然后*再*做另一件事。如果出现问题，我们*会发现*错误并做出明智的反应。这可能看起来很简单，但是它还没有被足够的提及。

我们仍然在使用回调，但是我们已经将它们包装在一个抽象中，这个抽象在其他方面帮助了我们。其中一个好处是它们允许多个解决方案回调…

```
$promise = readFile();
$promise->then(...)->catch(...);

// ...let's add logging to existing code

$promise->then(function(string $content) use ($logger) {
    $logger->info("file was read");
}); 
```

我还想让我们关注点别的事情。承诺为思考同步代码如何变成异步代码提供了一种公共语言——一种公共抽象。

让我们用 promises 把一些应用程序代码变成异步的…

## 制作 PDF 文件

应用程序生成某种摘要文档是很常见的——可以是发票或库存清单。假设您有一个通过 Stripe 处理支付的电子商务应用程序。当客户购物时，您希望他们能够下载交易的 PDF 收据。

有许多方法可以做到这一点，但一个真正简单的方法是使用 HTML 和 CSS 生成文档。您可以将其转换为 PDF 文档，并允许客户下载。

我最近需要做一些类似的事情。我发现支持这种操作的好库并不多。我找不到一个允许我在不同的 HTML → PDF 引擎之间切换的抽象。所以我开始建造自己的。

我开始思考我需要抽象来做什么。我选定了这样一个界面:

```
interface Driver
{
    public function html($html = null);
    public function size($size = null);
    public function orientation($orientation = null);
    public function dpi($dpi = null);
    public function render();
} 
```

为了简单起见，我希望除了`render`方法之外的所有方法都同时充当 getters 和 setters。给定这组预期的方法，接下来要做的事情是使用一个可能的引擎创建一个实现。我将 [domPDF](https://github.com/dompdf/dompdf) 添加到我的项目中，并开始使用它:

```
class DomDriver extends BaseDriver implements Driver
{
    private $options;

    public function __construct(array $options = [])
    {
        $this->options = $options;
    }

    public function render()
    {
        $data = $this->data();
        $custom = $this->options;

        return $this->parallel(
            function() use ($data, $custom) {
                $options = new Options();

                $options->set(
                    "isJavascriptEnabled", true
                );

                $options->set(
                    "isHtml5ParserEnabled", true
                );

                $options->set("dpi", $data["dpi"]);

                foreach ($custom as $key => $value) {
                    $options->set($key, $value);
                }

                $engine = new Dompdf($options);

                $engine->setPaper(
                    $data["size"], $data["orientation"]
                );

                $engine->loadHtml($data["html"]);
                $engine->render();

                return $engine->output();
            }
        );
    }
} 
```

> 我不打算详细介绍如何使用 domPDF。我认为[文档](https://github.com/dompdf/dompdf)在这方面做得足够好，让我可以专注于这个实现的异步部分。

我们一会儿会看看`data`和`parallel`方法。这个`Driver`实现的重要之处在于它收集了数据(如果已经设置了数据，则为默认值)和定制选项。它将这些传递给我们希望异步运行的回调。

domPDF 不是一个异步库，转换 HTML → PDF 是一个众所周知的缓慢过程。那么我们如何让它异步呢？嗯，我们可以编写一个完全异步的转换器，或者我们可以使用现有的同步转换器；但是在并行线程或进程中运行它。

这就是我制作`parallel`方法的目的:

```
abstract class BaseDriver implements Driver
{
    protected $html = "";
    protected $size = "A4";
    protected $orientation = "portrait";
    protected $dpi = 300;

    public function html($body = null)
    {
        return $this->access("html", $html);
    }

    private function access($key, $value = null)
    {
        if (is_null($value)) {
            return $this->$key;
        }

        $this->$key = $value;
        return $this;
    }

    public function size($size = null)
    {
        return $this->access("size", $size);
    }

    public function orientation($orientation = null)
    {
        return $this->access("orientation", $orientation);
    }

    public function dpi($dpi = null)
    {
        return $this->access("dpi", $dpi);
    }

    protected function data()
    {
        return [
            "html" => $html,
            "size" => $this->size,
            "orientation" => $this->orientation,
            "dpi" => $this->dpi,
        ];
    }

    protected function parallel(Closure $deferred)
    {
        // TODO
    }
} 
```

这里我实现了 getter-setter 方法，认为可以在下一个实现中重用它们。`data`方法充当将各种文档属性收集到一个数组中的快捷方式，使它们更容易传递给匿名函数。

`parallel`方法开始变得有趣起来:

```
use Amp\Parallel\Forking\Fork;
use Amp\Parallel\Threading\Thread;

// ...

protected function parallel(Closure $deferred)
{
    if (Fork::supported()) {
       return Fork::spawn($deferred)->join();
    }

    if (Thread::supported()) {
        return Thread::spawn($deferred)->join();
    }

    return null;
} 
```

我是 [Amp 项目](https://github.com/amphp)的超级粉丝。这是一个支持异步架构的库集合，它们是[异步互操作](https://github.com/async-interop)项目的主要支持者。

他们的一个库叫做`amphp/parallel`，它支持多线程和多进程代码(通过 Pthreads 和进程控制扩展)。那些`spawn`方法返回 Amp 对承诺的实现。这意味着 render 方法可以像任何其他承诺返回方法一样使用:

```
$promise = $driver
    ->html("<h1>hello world</h1>")
    ->size("A4")->orientation("portrait")->dpi(300)
    ->render();

$results = yield $promise; 
```

这段代码有点负荷。Amp 还提供了一个事件循环实现和所有助手代码，能够将普通的 PHP 生成器转换成协程和承诺。你可以在我写的另一篇文章的[中读到这是如何可能的，以及它与 PHP 的生成器有什么关系。](https://medium.com/async-php/co-operative-php-multitasking-ce4ef52858a0#.9n1plj6z2)

> 返还的承诺也在变得标准化。Amp 返回 [Promise spec](https://github.com/async-interop/promise) 的实现。它与我上面展示的代码略有不同，但仍然执行相同的功能。

生成器的工作方式类似于拥有它们的语言中的协程。协程是可中断的函数，这意味着它们可以用来做短时间的突发工作，然后在等待时暂停。暂停时，其他功能可以使用系统资源。

实际上，这看起来像是:

```
use AsyncInterop\Loop;

Loop::execute(
    Amp\wrap(function() {
        $result = yield funcReturnsPromise();
    })
); 
```

这看起来比一开始就写同步代码要复杂得多。但是它允许在我们等待`funcReturnsPromise`完成的时候发生其他事情。

让步承诺是我所说的抽象概念。它给了我们一个框架，通过这个框架，我们可以做出有回报的函数。代码可以以可预测和可理解的方式与这些承诺进行交互。

看看使用我们的驱动程序呈现 PDF 文档会是什么样子:

```
use AsyncInterop\Loop;

Loop::execute(Amp\wrap(function() {
    $driver = new DomDriver();

    // this is an AsyncInterop\Promise...
    $promise = $driver
        ->body("<h1>hello world</h1>")
        ->size("A4")->orientation("portrait")->dpi(300)
        ->render();

    $results = yield $promise;

    // write $results to an empty PDF file
})); 
```

这不如在异步 HTTP 服务器中生成 pdf 有用。有一个叫做 Aerys 的 Amp 库，它使得这种类型的服务器更容易创建。使用 Aerys，您可以创建以下 HTTP 服务器代码:

```
$router = new Aerys\Router();

$router->get("/", function($request, $response) {
    $response->end("<h1>Hello World!</h1>");
});

$router->get("/convert", function($request, $response) {
    $driver = new DomDriver();

    // this is an AsyncInterop\Promise...
    $promise = $driver
        ->body("<h1>hello world</h1>")
        ->size("A4")->orientation("portrait")->dpi(300)
        ->render();

    $results = yield $promise;

    $response
        ->setHeader("Content-type", "application/pdf")
        ->end($results);
});

(new Aerys\Host())
    ->expose("127.0.0.1", 3000)
      ->use($router); 
```

> 再说一遍，我现在不打算深入 Aerys 的细节。这是一个令人印象深刻的软件，完全配得上它自己的帖子。你不需要理解 Aerys 是如何工作的，就可以看到我们的转换器代码看起来有多自然。

## 我的老板说“不异步！”

如果您不确定多长时间能构建一次异步应用程序，为什么要经历这么多麻烦呢？编写这段代码让我们对新的编程范式有了有价值的了解。并且，仅仅因为我们以异步方式编写代码并不意味着它不能在同步环境中工作。

要在同步应用程序中使用这些代码，我们需要做的就是将一些异步代码移入其中:

```
use AsyncInterop\Loop;

class SyncDriver implements Driver
{
    private $decorated;

    public function __construct(Driver $decorated)
    {
        $this->decorated = $decorated;
    }

    // ...proxy getters/setters to $decorated

    public function render()
    {
        $result = null;

        Loop::execute(
            Amp\wrap(function() use (&$result) {
                $result = yield $this->decorated
                    ->render();
            })
        );

        return $result;
    }
} 
```

使用这个装饰器，我们可以编写看似同步的代码:

```
$driver = new DomDriver();

// this is a string...
$results = $driver
    ->body("<h1>hello world</h1>")
    ->size("A4")->orientation("portrait")->dpi(300)
    ->render();

// write $results to an empty PDF file 
```

它仍在异步运行代码(至少在后台)，但这些都不会向消费者公开。您可以在一个同步应用程序中使用它，并且永远不知道它的内部发生了什么。

## 支持其他框架

Amp 有一组特殊的要求，使其不适用于所有环境。例如，base Amp(事件循环)库需要 PHP `7.0`。并行库需要 Pthreads 扩展或进程控制扩展。

我不想把这些限制强加给每个人，并想知道如何才能支持更广泛的系统。答案是将并行执行代码抽象到另一个驱动系统中:

```
interface Runner
{
    public function run(Closure $deferred);
} 
```

我可以为 Amp 和 ReactPHP(限制更少，尽管更老)实现这一点:

```
use React\ChildProcess\Process;
use SuperClosure\Serializer;

class ReactRunner implements Runner
{
    public function run(Closure $deferred)
    {
        $autoload = $this->autoload();

        $serializer = new Serializer();

        $serialized = base64_encode(
            $serializer->serialize($deferred)
        );

        $raw = "
            require_once '{$autoload}';

            \$serializer = new SuperClosure\Serializer();
            \$serialized = base64_decode('{$serialized}');

            return call_user_func(
                \$serializer->unserialize(\$serialized)
            );
        ";

        $encoded = addslashes(base64_encode($raw));

        $code = sprintf(
            "print eval(base64_decode('%s'));",
            $encoded
        );

        return new Process(sprintf(
            "exec php -r '%s'",
            addslashes($code)
        ));
    }

    private function autoload()
    {
        $dir = __DIR__;
        $suffix = "vendor/autoload.php";

        $path1 = "{$dir}/../../{$suffix}";
        $path2 = "{$dir}/../../../../{$suffix}";

        if (file_exists($path1)) {
            return realpath($path1);
        }

        if (file_exists($path2)) {
            return realpath($path2);
        }
    }
} 
```

> 我习惯于将闭包传递给多线程和多进程工作者，因为这就是 Pthreads 和进程控制的工作方式。使用 ReactPHP 进程对象完全不同，因为它们依赖于`exec`来执行多进程。我决定实现我习惯的相同的闭包功能。这对异步代码来说并不重要——这纯粹是一种品味的表达。

[SuperClosure](https://github.com/jeremeamia/super_closure) 库序列化闭包及其绑定变量。这里的大部分代码都是您期望在 worker 脚本中找到的。事实上，使用 ReactPHP 的子进程库的唯一方法(除了序列化闭包)是将任务发送到 worker 脚本。

现在，我们可以传递 runner 实现，而不是用`$this->parallel`和特定于 Amp 的代码加载我们的驱动程序。作为异步代码，这类似于:

```
use React\EventLoop\Factory;

$driver = new DomDriver();

$runner = new ReactRunner();

// this is a React\ChildProcess\Process...
$process = $driver
    ->body("<h1>hello world</h1>")
    ->size("A4")->orientation("portrait")->dpi(300)
    ->render($runner);

$loop = Factory::create();

$process->on("exit", function() use ($loop) {
    $loop->stop();
});

$loop->addTimer(0.001, function($timer) use ($process) {
    $process->start($timer->getLoop());

    $process->stdout->on("data", function($results) {
        // write $results to an empty PDF file
    });
});

$loop->run(); 
```

> 不要因为 ReactPHP 代码看起来与 Amp 代码有多么不同而感到惊慌。ReactPHP 没有像 Amp 那样实现相同的协程基础。相反，对于大多数事情，ReactPHP 更喜欢回调。这段代码仍然只是并行运行 PDF 转换，并返回生成的 PDF 数据。

有了抽象的 runners，我们可以使用任何我们喜欢的异步框架，并且我们可以期望我们正在使用的驱动程序返回该框架的抽象。

## 我能用这个吗？

一开始作为实验的东西，变成了多驱动、多 runner 的 HTML → PDF 库；叫做[纸](https://github.com/asyncphp/paper)。这就像是 [Flysystem](http://flysystem.thephpleague.com) 的 HTML → PDF 等价物，但它也是如何编写异步库的一个很好的例子。

当你试图开发异步 PHP 应用程序时，你会发现库生态系统中的缺口。不要因为这些而气馁！相反，利用这个机会考虑如何使用 ReactPHP 和 Amp 提供的抽象来创建自己的异步库。

你最近是否构建了一个有趣的异步 PHP 应用程序或库？请在评论中告诉我们。

## 分享这篇文章