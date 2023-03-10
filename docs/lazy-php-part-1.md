# 懒惰 PHP:第 1 部分

> 原文：<https://www.sitepoint.com/lazy-php-part-1/>

懒惰有很多好处，尤其是在 PHP 方面。我说的不是躺在沙发上伸展身体，而是可以用来提高 PHP 代码速度的技术。

**PHP 是如何工作的(从远处)**
当你的服务器请求一个 PHP 脚本时，在完整的网页到达访问者的浏览器之前会发生很多事情。一个简单的观点是这样的:

1.*传入请求*:web 服务器(通常是 Apache)接收脚本请求，并将工作“委托”给 PHP 的核心引擎(Zend 引擎)。如果你使用 PHP 作为一个 Apache 模块，那么它已经被加载并准备好了。如果你使用 PHP 作为一个 CGI 可执行文件，它首先要“旋转起来”才能工作。

2.*准备操作码*:引擎读取、解析并编译脚本为“操作码”(执行时要做什么的指令列表)

3.*执行*:指令被执行，最终结果(一个网页)到达访问者浏览器。

4.*扔掉所有东西*:PHP 引擎扔掉所有东西；操作码、保存在内存中的变量、输出——批次。回到步骤 1…

在[中有关于如何优化 PHP](http://phplens.com/lens/php-book/optimizing-debugging-php.php) 的更详细的解释，在 [PHP Inside Out](http://www.edwardbear.org/pio.pdf) 中有非常详细的讨论。

我一会儿会谈到操作码缓存，但这只是一般情况。

有些人可能会说，最后一部分，尤其是抛弃内存中的所有变量，是疯狂的。实际上，这意味着你几乎不会遇到 PHP 的资源分配问题(例如，内存不足),而且跨多个服务器部署 PHP 应用程序并平衡它们之间的负载非常容易。

底线是有很多浪费。你的应用程序随着脚本的每一次点击而“重生”。

懒惰的 PHP
很明显，PHP 引擎做的工作越少，访问者越快得到他们想要的网页。

但是如果你有一个提供各种功能的复杂应用程序，使用数据库抽象、web 服务、用户认证等等。等等。怎么能指望有像样的表现呢？！？

答案是*懒惰*:做绝对最小值。

例如，如果没有错误要处理，为什么要加载所有 1000 行错误处理逻辑？如果当前请求只调用了一个函数，为什么要包含 101 个函数的库呢？如果一次只显示 10 条记录，为什么要将整个表放入 PHP 数组呢？如果内容没有改变，为什么要重新呈现 HTML？

懒惰 PHP 的诀窍是设计您的代码，以便有服务于当前请求所需的最小“足迹”和在需要时找到其余部分的“指针”。

通常，当你将一个 PHP 脚本部署到你的 web 服务器时，它不会经常改变(只有当你用一个新版本替换它时)。回到上面的四个步骤，我们突然明白，对于 PHP 脚本的每个版本，只需要准备一次操作码；第一次执行时。

如果你使用某种形式的操作码缓存，如 [Zend Accelerator](https://www.zend.com/) 、 [PHP Accelerator](http://www.phpaccelerator.co.uk) 或 [Turck MMCache](http://turck-mmcache.sourceforge.net/) (适合 Windows 用户)，你可以绕过第二步，在第一次执行后，操作码被缓存以备将来使用。

遗憾的是，几乎没有廉价的 LAMP 主机知道操作码缓存(或者有些是开源/免费使用的),所以我们大多数人不得不忍受 PHP 脚本读取、解析和编译的成本。

不过，解决这个问题的一个简便方法是仔细放置像 [include](http://www.php.net/include) 和 [require_once](http://www.php.net/require_once) 这样的命令。

例如，如果您有一个身份验证系统，并且希望跟踪失败的登录尝试，该怎么办？您可能编写了如下代码:

```
 < ?php
require_once 'lib/auth.php';
require_once 'lib/logger.php';

$Auth = & new Auth();
$Logger = & new Logger('auth.log');

if ( $Auth->isValid($_POST['username'],$_POST['password']) ) {
    // The user is valid
} else {
    $message = "Invalid username / password for ".
        $_POST['username']." from ".$_SERVER['REMOTE_HOST'];
    $Logger->log($message);
}
?>

```

记录器在每次请求时都会被加载(和实例化),但是只有在登录失败时才会用到它。怎么样；

```
 < ?php
require_once 'lib/auth.php';

$Auth = & new Auth();

if ( $Auth->isValid($_POST['username'],$_POST['password']) ) {
    // The user is valid - the stuff here
} else {
    // Now load the logger...
    require_once 'lib/logger.php';
    $Logger = & new Logger('auth.log');
    $message = "Invalid username / password for ".
        $_POST['username']." from ".$_SERVER['REMOTE_HOST'];
    $Logger->log($message);
}
?> 

```

现在，只有当有需要记录的东西时，才会包含日志记录器。

更重要的是，你可以从一个函数内部包含函数和类，所以下面也是可以的；

```
 < ?php
function updateArticle(& $User, $article) {
    // Include a class
    require_once 'lib/permissions.php';
    $Permissions = & new Permissions($User);

    $action = 'update_article';
    if ( $Permissions->actionAllowed($action) ) {
        // Update the article - store in DB for example
    } else {
        // Include a class
        require_once 'lib/logger.php';
        $Logger = & new Logger ('useractions.log');
        $message = "Illegal action: $action attempted by ".
            $User->username();
        $Logger->log($message);
    }
} 

```

[注意，上面的代码并不意味着建议任何特定的登录/验证策略]

在编写面向对象 PHP 时，惰性包含变得尤其重要，因为在你的应用程序中可能有数百个类，都在单独的文件中。将包含的文件数量限制为满足给定请求实际需要的文件数量可以降低开销。

有意义吗？

[…未完待续…]

## 分享这篇文章