# 有可能在 iPad 上编写和运行 PHP 代码吗？

> 原文：<https://www.sitepoint.com/is-it-possible-to-write-and-run-php-code-on-an-ipad/>

*本文由 [Claudio Ribeiro](https://www.sitepoint.com/author/cribeiro/) 进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

我喜欢 iPad。对于媒体消费和游戏来说，这是一个非常棒的外形；它作为电子阅读器也很好用。问题是我没有尽可能多地使用它。大部分时间我都在消费媒体(网飞、Twitch、YouTube)，我在并行编码。

我可以在我的 MacBook 上做到这一点，但直到现在我才做到这一点。两件事使得这成为可能:

1.  iOS 最近增加了对画中画播放的支持。这意味着我可以一边玩视频应用，一边做其他事情。

2.  奇妙的应用程序(如 [DraftCode](https://itunes.apple.com/us/app/draftcode-php-ide/id593757593) 和[工作副本](https://itunes.apple.com/us/app/working-copy-enterprise/id965019520))可以直接从 iPad 上编写和运行 PHP 应用程序，并将更改推送到 Github。

![Picture in picture](img/8f7ee701fa346e2e4fcbcb3e51642147.png)

今天我将向你们展示我是如何在 iPad 上编码的。我不会假装这是一个完美的工作流程(什么是工作流程？)，但这对于我来说，和我第一次用笔记本电脑代替台式机一样激动。

我不久前开始写这篇文章。说实话，我不认为我将要谈论的应用程序那时已经准备好了。此后，他们都收到了更新。就好像开发人员能听到我大脑里的声音，想让我开心。我也为这个页面的大小道歉；有很多动画 gif..

## 五金器具

整个实验最重要的部分之一，就是找到正确的键盘。就移动设备而言，不缺少质量低劣的硬件。

我找了很长时间才选定了一个键盘，我喜欢这个键盘的外观。我可以有把握地说我做出了正确的选择。

这个键盘的长度大约是 iPad Mini 2 的两倍，重量也差不多。按键感觉很棒，它们之间有很大的空间。而且很安静！

这个键盘还有一个触控板。iOS 不支持鼠标或触控板(据我所知)，所以你不必使用带触控板的键盘。我喜欢这个，因为它可以兼作我的[树莓派](https://www.raspberrypi.org)的输入设备(键盘和触控板使用单个 USB 端口)。

除非你使用蓝牙键盘，否则你需要一个 USB 接口。幸运是，苹果公司生产了一种适配器(最初是为了兼容数码相机)，它可以很好地与我试过的所有 USB 键盘配合使用。

Windows 键兼作 command 键，Windows+z/Windows+x/Windows+c/Windows+v 都像你习惯的那样工作。也可以使用 Windows + →和 Windows + ←移动到行首和行尾。

微软网站上的多功能媒体键盘标价为 39.95 美元，但你可以在亚马逊上以 29.99 美元的价格买到它。苹果网站上的 Lightning USB 适配器标价为 29.99 美元，实际上亚马逊的价格要高出几美元。

## 应用程序

我已经提到了几个应用程序( [DraftCode](https://itunes.apple.com/us/app/draftcode-php-ide/id593757593) 和[工作副本](https://itunes.apple.com/us/app/working-copy-enterprise/id965019520))，事实是这两个是你唯一需要的。

你用什么工具编码？您可能会使用 IDE(或者至少是文本编辑器，比如 Atom)。您可能会使用 Git 之类的东西来组织和版本化您的源代码。

DraftCode 是一个带有语法高亮的文本编辑器。它还不支持类似 IDE 的特性，比如代码完成和静态分析，但是它有能力运行您在其中编写的 PHP 脚本。

更重要的是，它包括一个文件树，通过它你可以管理文件和文件夹。通过这个接口构建一个完整的 PHP 应用程序是可能的(尽管有些费力)。

*我们一会儿将看看如何运行 PHP 应用…*

工作副本是一个 Git 客户端。免费版支持 Github、Bitbucket 和任何其他 Git URL 源。它支持克隆、获取和合并。除了推之外，我没有用它试过别的。

如果你想在 iPad 上推行你编码的改变，你就必须购买企业版。唯一值得注意的区别(鉴于我很少使用它)是，在企业版上，您只能将代码推送到远程。

DraftCode 售价 10.99 美元，工作副本(企业版)售价 14.99 美元。当我在记录的时候，我注意到了一个奇怪的导出错误。当免费版本安装后，我可以导出到它。企业版装的时候，我不行。现阶段我推荐使用免费版的应用内企业购买(同价)，而不是直接购买企业版。

## 写你的第一个剧本

让我们来看看用 DraftCode 编写 PHP 脚本的过程。在加载(并删除所有形式的示例文件)之后，界面非常干净:

![DraftCode interface](img/0bacf288340be7dd1f4bdbb645260327.png)

在文件树的底部(左侧面板)，有几个按钮。Cog(设置按钮)显示了该应用程序的一些信息，并提供了安装 [WordPress](https://wordpress.org) 和 [phpLiteAdmin](https://www.phpliteadmin.org) 的按钮(一个 SQLite phpMyAdmin 克隆)。

然后，还有用于创建文件、文件夹、导入媒体文件和批量移动文件的按钮(按此顺序)。媒体导入器来自 iPad 的媒体库，因此它支持任何音频/视频/图像文件，否则您可以在 iPad 上播放。

让我们创建一个脚本。通过应用程序了解哪个版本的 PHP 和哪些模块可用是很有用的。您可以创建一个以任何名称命名的文件(但这种脚本的常见名称是`index.php`或`phpinfo.php`):

![Getting PHP info](img/2755340e77ea38c22239c0fce563fbf2.png)

### 尝试 SQLite

你可能已经注意到(在`phpinfo`中)提到了几次 MySQL。这并不意味着你可以在你的 iPad 上使用 MySQL 数据库，只是意味着你可以连接到一个公共的 MySQL 服务器。

尽管 DraftCode 有 SQLite 扩展。让我们用它和 PDO 来测试我们是否可以使用 SQLite 数据库:

```
date_default_timezone_set("UTC");

$handle = new PDO("sqlite::memory:");

$handle->setAttribute(
    PDO::ATTR_ERRMODE,
    PDO::ERRMODE_EXCEPTION
);

$handle->exec("
    CREATE TABLE IF NOT EXISTS messages (
        id INTEGER PRIMARY KEY,
        message TEXT
    )
");

$query = "
    INSERT INTO messages (message) VALUES (:message)
";

$statement = $handle->prepare($query);
$statement->bindParam(":message", $message);

$message = "hello world";

$statement->execute(); 
```

这大部分只是标准的 PDO 示例代码，虽然我会原谅你不熟悉它。我们生活在数据库抽象层和对象关系映射器的时代。

我们打开内存中 SQLite 数据库的句柄，并将错误模式设置为抛出异常。然后我们创建一个`messages`表，有一个单独的`message`文本字段。

接下来，我们准备一个 SQL 查询，并将一个`:message`参数绑定到这个准备好的语句。最后，我们执行准备好的语句，并完全期望 messages 表包含一行。

让我们使用下面的代码尝试获取这一行:

```
$messages = $handle->query("
    SELECT * FROM messages
");

foreach($messages as $message) {
    print "message: " . $message["message"] . "<br>" . PHP_EOL;
} 
```

SQLite 不能替代其他更强大的数据库，但是我们可以用它来开发只为单个用户服务的应用程序。

## 使用工作副本提取代码

更多的时候，我发现自己在现有的代码库上工作。如果我不得不手工重新创建整个工作目录，那就太糟糕了。幸运的是，我们可以使用工作副本从 Git 服务器上获取源代码。

如果我们计划将代码推回 Github(假设这是我们正在使用的遥控器)，建立一个身份并与 Github 连接是一个好主意:

![Connecting to Github](img/536c8e7ccda0c4e93527bf10e2eb0bda.png)

这不是绝对必要的，但至少它是一种方式(当我们想要推送已经添加/更改的代码时)。下一步是克隆存储库。我们可以使用共享对话框向 DraftCode 发送代码。Working Copy 将存储库归档，并存储一些 PHP 代码以将其提取到 iOS 剪贴板。要提取存储库(在 DraftCode 内部)，我们需要创建一个新文件，粘贴剪贴板代码，并运行它。

![Sending code to DraftCode](img/2937bace6acac4792e0c80d31fd027f1.png)

## 安装 Laravel

最新版本的 [Laravel](https://laravel.com) (在我写这篇文章时为`5.3`)支持 PHP `5.6`和 SQLite 数据库。这对我们来说是个好消息，但是我们仍然需要做一些工作来使它工作。

### 提交依赖关系

正如我们所看到的，我们想要运行的任何代码都必须通过 DraftCode 的服务器来运行。这意味着我们不能轻易地使用 Composer 来安装依赖项。理论上可以为此制作一个脚本，但这是另一个话题了。

给予 Laravel 所需的所有依赖项的最快方法是将它们存储在 Git 中。这样，我们可以将整个 Git 存储库拉下来，并将其部署到 DraftCode 的工作目录中。

*`vendor`在 Laravel 应用中默认被忽略。您必须将它从`.gitignore`中移除，才能将其提交到存储库。*

### 定制环境

`.env`是另一个被忽略的文件。当您关闭 Git 库时，您可能不会有 Laravel 应用程序将使用的版本(除非您首先有意提交了`.env`)。

如果不先给它们一个`.txt`扩展名，现在就不能创建(或编辑)`.env`或`.env.example`。在 Laravel 可以创建会话之前，您将需要具有有效的`APP_KEY`值的`.env`。

### Iconv

PHP DraftCode 提供的版本没有设置`iconv_*`函数。我们可以使用`patchwork/utf8`库来填补这些函数。

*Laravel `4.*`曾经有`patchwork/utf8`作为依赖。这在`5.0`……*被删除了

要添加它，我们可以使用:

```
composer require patchwork/utf8 
```

与大多数 PHP 填充程序一样，添加它们的最佳时机是直接跟随 Composer 自动加载程序。第一次发生在拉勒维尔是在`public/index.php`年。

事实上，`public/index.php`的默认状态是:

```
<?php

/**
 * Laravel - A PHP Framework For Web Artisans
 *
 * @package  Laravel
 * @author   Taylor Otwell <taylor@laravel.com>
 */

/*
| ...
*/

require __DIR__.'/../bootstrap/autoload.php';

/*
| ...
*/

$app = require_once __DIR__.'/../bootstrap/app.php';

/*
| ...
*/

$kernel = $app->make(Illuminate\Contracts\Http\Kernel::class);

$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
);

$response->send();

$kernel->terminate($request, $response); 
```

*这是来自[https://github . com/laravel/laravel/blob/5dc 817 BF 55598 FDA 017 E3 b7f 39957447 BDA 94846/public/index . PHP](https://github.com/laravel/laravel/blob/5dc817bf55598fda017e3b7f39957447bda94846/public/index.php)*

因此，要添加用于`iconv_*`函数的填充程序，我们需要添加下面一行:

```
require __DIR__.'/../bootstrap/autoload.php';

\Patchwork\Utf8\Bootup::initAll(); 
```

### 随机字节

Laravel 也靠`paragonie/random_compat:~1.4|~2.0`。这个库为该语言较新的加密改进提供了各种垫片。其中最主要的是以加密安全的方式生成随机数据的能力。

使得这一代密码安全的是熵的来源。该垫片使用各种来源，可以在这里看到。

不幸的是，PHP 的 DraftCode 版本支持的惟一源代码是`openssl_random_pseudo_bytes`函数。虽然评论说这个库将会退回到`openssl_random_pseudo_bytes`作为最后的手段，但是这个功能在`1.3`版本中被移除了，取而代之的是一个抛出异常的函数。

Laravel 要求`~1.4`，这意味着我们不能在项目中要求`^1.2`而不出现编写器错误。唯一的选择是从`paragonie/random_compat`的`1.2`版本中获取实现，并将其包含在 Composer 的自动加载器之前。

如果您想知道为什么我们之前需要包含它，这是因为`paragonie/random_compat`全局注册了抛出异常的`random_bytes`函数(并且一旦 Composer 加载了它)。这造成了一种情况，即一个填补库阻止所有其他填补库(相同类型)工作。采取一种奇怪的立场…

*请不要让我对`paragonie/random_compat`这个单一方面的批评让你觉得我不喜欢图书馆。这太棒了，您可能应该使用它来支持您自己的 shim 实现。*

`openssl_random_pseudo_bytes`实现源代码如下所示:

```
function random_bytes($bytes)
{
    try {
        $bytes = RandomCompat_intval($bytes);
    } catch (TypeError $ex) {
        throw new TypeError(
            'random_bytes(): $bytes must be an integer'
        );
    }

    if ($bytes < 1) {
        throw new Error(
            'Length must be greater than 0'
        );
    }

    $secure = true;
    $buf = openssl_random_pseudo_bytes($bytes, $secure);
    if (
        $buf !== false
        &&
        $secure
        &&
        RandomCompat_strlen($buf) === $bytes
    ) {
        return $buf;
    }

    throw new Exception(
        'Could not gather sufficient random data'
    );
} 
```

*这是从[这里来的](https://github.com/paragonie/random_compat/blob/0b441c047be7a1e829fb106687284b0f8cf77db7/lib/random_bytes_openssl.php)*

你可以把它保存在任何你喜欢的地方，但是我已经把它保存到`path/to/project/compat-random.php`了。我们需要将它添加到 Composer 自动加载程序之上:

```
if (!function_exists('random_bytes')) {
    require_once __DIR__.'/../compat-random.php';
}

require __DIR__.'/../bootstrap/autoload.php';

\Patchwork\Utf8\Bootup::initAll(); 
```

这样，将使用`openssl_random_pseudo_bytes`实现(如果`random_bytes`还不存在的话)，并且`paragonie/random_compat`不会注册一个抛出异常的`random_bytes`。

*这显然不如`paragonie/random_compat`中的替代垫片函数安全。你不应该在生产中运行这种黑客。*

## 自定义路线

在讨论定制路线之前，让我们先运行 Laravel 应用程序:

![Laravel on an iPad](img/cfbd792f0c9205ff343c2ff3b6036044.png)

到目前为止，一切顺利！但是，如果我们正在运行`public/index.php`，那么我们如何在我们的应用程序中改变到其他地方的路线呢？为此，我们需要再次修改`public/index.php`:

```
$_SERVER["REQUEST_URI"] = $_GET["url"];

$response = $kernel->handle(
    $request = Illuminate\Http\Request::capture()
); 
```

在每个请求之前，我们用我们提供给查询字符串参数`url`的任何内容覆盖`REQUEST_URI`。这是因为`Request::capture()`代理底层的`HTTPRequest::createFromGlobals` [Symfony 实现](https://github.com/symfony/http-foundation/blob/a253dca70944b62ea371d26a03db5c9cbb657525/Request.php)。同样的实现基于`REQUEST_URI`建立了不同的 URI 国家。

现在，我们可以创建新的路线，例如:

```
Route::get("/then", function() {
    return "hello world";
}); 
```

…并使用 URL `http://127.0.0.1/tutorial-coding-on-the-ipad/public/index.php?url=then`导航到它们。

![Navigating custom routes](img/4de27e890aca416c3f1ac91ab560da1e.png)

## 使用工作副本推送代码

一旦您对存储库进行了更改，您可能会想要提交和推送它们。向上导航到根存储库文件夹，并点击文件夹名称。你会看到一个压缩文件夹的选项。这将需要一些时间，取决于代码库的大小。完成后，您可以选择归档并与工作副本共享。

![Sending code to Working Copy](img/821030d7fe170063f569cddea55bcc3c.png)

工作副本可以识别哪些文件被更改，并允许您将它们提交回存储库。

![Committing changes](img/5190a5cf5487119a5e0c497c069d5f91.png)

## 结论

在本教程中，我们了解了在 iPad 上开发 PHP 应用程序的硬件、软件和配置要求。我们看了一些 DraftCode 的 PHP 支持的特性(和限制)。

在我们分道扬镳之前，我认为讨论一下为什么我们想在 iPad 上编码是个好主意。这对我来说是一个有趣的实验，但我不认为 iPad 会成为我选择的编码机器。这些应用在过去几周有了显著的改进，但使用起来仍然很慢(由于沙盒的限制和无法运行 CLI 脚本)。

如果你发现自己经常坐飞机，使用 iPad 有一定的吸引力。如果你能为它们找到一个整洁的外壳，iPad、键盘和适配器要比 MacBook 和充电器整洁得多。iPad 可以在飞机上充电，触摸屏使得偶尔停下来玩游戏变得可行。

也就是说，任何 Surface +键盘组合使用起来可能都更舒服。有了 Surface，你不需要安装这些专业应用——你可以安装一个普通的 IDE 和一个可用的 PHP 7.0 环境。

所以，我是说你应该去一个表面吗？号码

让我们来看看我在本教程中描述的工作流所涉及的成本:

1.  你需要一个 iPad，一个键盘和一个 USB 接口。如果你已经有一台 iPad，那么连接器和键盘的价格是 29.99 美元。或者你可以买一个蓝牙键盘。
2.  你需要 DraftCode，10.99 美元。你可以带着工作拷贝的免费版本离开(如果你对推回遥控器不感兴趣，或者你喜欢用电子邮件发送你的项目)。

假设你没有这些东西。你必须支付:

1.  269 美元→ iPad Mini 2(苹果)
2.  29.99 美元→相机套件连接器(苹果)
3.  29.99 美元→一体化媒体键盘(亚马逊)
4.  10.99 美元→草案代码
5.  14.99 美元→工作副本

总共是 381.96 美元(如果你已经有了 iPad，就是 85.96 美元)。你能买到的最便宜的 Surface 3([来自微软](https://www.microsoft.com/surface/en-us/devices/compare-devices))是 499 美元，而且你连键盘都不带。除了编码，我更想要 iPad。

它不会取代你目前的工作流程，但在必要时可以，而且尝试起来很有趣。你试过类似的东西吗？旅行时如何编码？请在下面的评论中告诉我们什么对你有用。

## 分享这篇文章