# 用 FirePHP 调试 PHP 代码

> 原文：<https://www.sitepoint.com/debugging-php-code-with-firephp/>

作为一套软件项目的技术经理，我的职责之一就是做代码审查。在做评审的时候，我经常看到的事情之一是调试 PHP 代码提交并被推上链。如果你做过这个，请举手:

```
<?php
$total = $someService->getComplexTotal();
var_dump($total);
$someOtherService->processTotal($total);
```

好，所以你不用举手。但很有可能，你笑了一下。或者“是啊，他怎么知道我这样做？”或者“是的，我以前也是这样做的。但是我现在有其他的调试方式。”多么精彩的一幕啊！

我们应该使用不通过网页向非程序员显示信息的调试方法。也就是说，我们永远不会看到变量转储或 SQL 语句回显到屏幕上。这不可能发生。

最安全的调试方法需要将 IDE 配置为使用 Xdebug 或 Zend Debugger 之类的工具来跟踪当前正在执行的代码。这并不总是可行的。在无法使用完全配置好的调试环境的情况下，我求助于 FirePHP。

## 使用 FirePHP 进行更安全的 PHP 调试

FirePHP 是一个 PHP 库，用于从您的 PHP 代码在浏览器中生成控制台消息。控制台消息通常由 JavaScript 创建，但是 FirePHP 将类似的消息作为 HTTP 头与来自服务器的响应一起发送。各种浏览器扩展充当 JavaScript 控制台方法的代理，并将这些头转换成控制台日志消息。

为什么输出调试数据“更安全”？嗯，当使用像 JavaScript 的`console.log()`这样的方法时，您需要共同努力来查看调试消息。普通访问者不会在他们的浏览器上打开开发控制台，因此不会看到您的调试消息。如果您输出的是`var_dump()`的，就不可能看不到消息。

请注意，我称之为“更安全”，但是您仍然必须小心，不要让 FirePHP 调用在生产中具有可用的关键系统信息。

## 设置 FirePHP

访问 FirePHP 核心安装页面，选择直接下载库或通过 PEAR 获得安装命令。如果您使用其他框架，比如 Zend Framework，FirePHP 已经有日志编写器了，所以不需要额外下载。

如果你正在使用 Firefox，安装 [FireBug](http://getfirebug.com) 作为一套扩展的工具来调试你的 web 应用程序，然后安装 [FirePHP](https://addons.mozilla.org/en-US/firefox/addon/firephp/) 扩展来扩展 FireBug 并拦截 FirePHP 消息。

如果你使用的是 Chrome，只需安装一个扩展 [FirePHP4Chrome](https://chrome.google.com/webstore/detail/firephp4chrome/gpgbmonepdpnacijbbdijfbecmgoojma) 。(充分披露:我创建了这个 Chrome 扩展，并积极开发它。我在 Chrome 的其他插件上运气不佳。)

最后，在您选择的浏览器中打开控制台。在 Firefox 中，单击 Firebug 图标显示控制台。通过单击控制台视图中的 FirePHP 图标并注意是否选中 enabled 旁边的复选标记，验证 FirePHP 已启用。在 Chrome 中，点击设置图标，选择工具，然后点击“开发者工具”来显示控制台。当控制台打开时，FirePHP4Chrome 将自动启用。

## 使用 FirePHP 的基本日志记录

如果您曾经使用过不同级别的错误日志记录，那么您无疑会看到关于什么类型的错误应该是“警告”还是“错误”的争论或者，您是否应该使用“信息”而不是“日志”虽然我有自己的看法，但我不会用它们来烦你。我只想说，您可能有理由以不同于标准消息的方式向控制台发送不同的消息。

FirePHP 协议目前支持的四种消息类型是:日志、信息、警告和错误。我的目的是向你展示如何使用每一个。您可以根据自己的环境和调试/日志记录的上下文来选择合适的方法。

下面的代码演示了如何包含 FirePHP 库并执行每种日志记录类型。

```
<?php
require 'FirePHPCore/fb.php';

FB::log('Log message');
FB::info('Info message');
FB::warn('Warn message');
FB::error('Error message');
```

上面的代码只是包含了核心的 FirePHP 库，并调用了各种日志记录方法。调用库方法有多种方式；它支持纯全局函数过程调用、静态方法调用和完全实例化的对象调用。我使用静态方法是因为我通常只希望在一行中使用 FirePHP 来发送调试信息；我想速战速决！

下面，你会看到 Firefox 和 Chrome 上的输出截图。

![](img/d3b18cfc378be35f80dc09e0e8f674a0.png)

![](img/7f4ab569ebbe6e56627508b2bc70a2f7.png)

Firefox 的显示稍微漂亮一点。Chrome 版本没有“信息”图标，因为 Chrome 中的`console.log()`还不支持该图标。您稍后还会注意到，Chrome 中的表格输出非常简单。这是因为 Chrome 还不支持非实验特性集中的`console.table()`。虽然我在自己的开发中使用 Chrome，但在本文剩余部分的截图中，我将使用 Firefox。

为了使日志信息更有用，可以给日志消息添加一个标签。这被指定为日志记录方法的第二个参数。

```
<?php
require 'FirePHPCore/fb.php';

$turtles = $zooService->fetchAllTurtles();
FB::info($turtles, "All Turtles");
```

正如您在下面的截图中看到的，在我的例子中，动物园服务获取了三只乌龟。很明显，这条特别的日志信息是海龟，而不是著名演员或其他什么。为了节省空间，Firebug/FirePHP 已经折叠了输出。将鼠标悬停在该行上以查看整个输出。

![](img/b34f8fa0993bfaf95cd66671a9d4d1d4.png "demo of label")

## 使用 FirePHP 进行高级日志记录

正如我已经演示过的，FirePHP 非常适合生成一次性日志消息。然而，有更多的高级特性让 FirePHP 大放异彩。我将快速介绍的三个是消息分组、表和跟踪。

### 分组

让我们假设我们有一些需要在循环中进行的日志记录。它可能会失去控制，使控制台滚动。我们可能希望将它放在一个组中，这样它可以很容易地折叠和展开。在这个示例代码中，我将记录我希望分组在一起的四个信息条目。

```
<?php
require 'FirePHPCore/fb.php';

$specialHashes = array();
for ($x = 0; $x < 5; $x++) {
    $specialHashes[$x] = sha1($x . 'somesalt');
    FB::info($specialHashes[$x], "Hash #" . $x);
}
```

输出是:

![](img/7346c271f62fbc7f630592ed4a67d826.png)

日志消息比我希望的稍微长一点。我想对它进行智能分组，并允许它折叠。我修改了代码，加入了 FirePHP 内置的分组方法。

```
<?php
require 'FirePHPCore/fb.php';

$specialHashes = array();
FB::group('Special Hashes');
for ($x = 0; $x < 5; $x++) {
    $specialHashes[$x] = sha1($x . 'somesalt');
    FB::info($specialHashes[$x], "Hash #" . $x);
}
FB::groupEnd();
```

现在，新的输出:

![](img/a8c9b1be0cd586c109f68c78ef528935.png)

如您所见，现在有一个选项可以最小化标记为“特殊散列”的组。FirePHP 甚至允许默认折叠一个组。只需替换`group()`调用并添加一个选项数组，如下所示:

```
FB::group('Special Hashes', array('Collapsed'=>true));
```

### 桌子

FirePHP 可以非常容易地发送表格数据。其实既然火狐的`console.table()`方法这么棒，数据显示的很漂亮，也很好理解。(哦 Chrome，请追上！)要在 FirePHP 中发送表格数据，需要使用一个列数组。但是，数组的第一个元素必须是列名。使用我们的特殊散列示例，让我们将它们记录为一个表:

```
<?php
require 'FirePHPCore/fb.php';

$specialHashes = array();
for ($x = 0; $x < 5; $x++) {
    $specialHashes[] = array($x, sha1($x . 'somesalt'));
}

$headers = array('Hash #', 'Hash Value');
$logTable = array($headers) + $specialHashes;
FB::table("Special Hashes", $logTable);
```

您可能会注意到我做的一件有点不同的事情是创建了`$logTable`变量。因为发送给 table()方法的数组的第一个元素需要是列标签，所以我创建了一个临时变量来完成这项任务。我不想操纵`$specialHashes`数组——那是我的业务数据。`table()`方法接受一个标签参数，后跟一个列数组。

![](img/a71f2b82abc5174ae3f45218d9d9cef4.png)

当控制台从 FirePHP 加载数据时，该表是折叠的。对于上面的截图，我点击了标签来显示数据。

### 跟踪

调试 PHP 代码时，跟踪是非常宝贵的。无论您是希望将它们与显示代码中特定点的变量值的日志消息结合起来，还是需要来自异常的更多信息，trace 都是它的用武之地。非常简单，您可以使用 FirePHP 在 PHP 代码中的任何地方调用跟踪，如下所示:

```
<?php
require 'FirePHPCore/fb.php';

FB::trace('Simple Trace');
```

输出是:

![](img/d35debc1da5814dbee9cca2c19d211ca.png)

当输出显示出来时，我只需点击“简单跟踪”的标签就可以显示整个跟踪。这个例子非常简单，显示了它可以多么容易地插入到任何代码中。

让我们做一个稍微复杂一点的例子(尽管，我怀疑你是否真的想在你的项目中编写这样的代码):

```
<?php
require 'FirePHPCore/fb.php';

function one() {
    echo "This is one.<br>";
    two();
}

function two() {
    echo "This is two!<br>";
    FB::trace('Trace from function two.');
}

one();
```

这里，函数`one()`被调用。函数`two()`在函数的最后一行被调用。在`two()`完成后，它执行对 FirePHP 的跟踪调用。以下截图显示了 Firefox 的输出:

![](img/ec388b9cbe4384b268942ef7dea80031.png)

### …还有更多！

除了上面的例子，FirePHP 还可以做更多的事情。要查看关于指定其他选项、设置异常跟踪甚至使用断言的更多信息，请查看 FirePHP 总部。

## 一个额外的例子

与大多数介绍风格的文章一样，这里的信息相对来说比较一般，例子也不多。需要一个更深入的例子来说服你使用 FirePHP 吗？我们走吧:

```
<?php
try {
    $key = "user.{$userId}";
    if (!SimpleCache::has($key)) {
        FB::info($key, 'Cache miss:');
        $userService = new UserService();
        $user = $userService->fetchById($userId);
        SimpleCache::set($key, $user);
    }
    $user = SimpleCache::get($key);
}
catch (Exception $e) {
    FB::error($e);
    Router::generate500();
}
```

构建这个简单的示例代码是为了检索用户对象。该系统使用某种类型的缓存，并在用户不可用时抛出异常。这段代码第一次运行时，用户没有被缓存，所以 FirePHP 发送一条 info 消息。对于用户 ID 5，下面的屏幕截图显示了输出。

![](img/6b89ad51b5fda135401c2a237e52ccbd.png)

第二次运行代码时，不会调用方法`info()`，也不会向控制台发送任何消息。现在，让我们假设缓存变得不可用。(我本来可以创建代码，这样它就可以在缓存不存在的情况下运行，但是那样会失去所有的乐趣！)在缓存不再可用的情况下，`set()`方法会抛出一个异常。根据这段代码，屏幕将显示一条漂亮的错误 500 消息。但是，我想马上看到异常和堆栈跟踪。这就是`error()`方法的用武之地。它可以解释`Exception`对象并很好地为我记录。

![](img/155701d7f76bee3382de54c05978a73e.png)

## 摘要

调试 PHP 代码最安全的方法是使用在 IDE 中设置的调试器环境。然而，对于更快的调试和应用程序级日志记录，FirePHP 可以发挥重要作用。

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章