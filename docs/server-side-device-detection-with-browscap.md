# 使用 Browscap 进行服务器端设备检测

> 原文：<https://www.sitepoint.com/server-side-device-detection-with-browscap/>

确保网站能够适应多种设备——尤其是移动设备——变得越来越重要和复杂。人们浏览网页的各种方式如雨后春笋般出现。这在很大程度上是由使用移动设备访问网络的增长所推动的，尽管它也适用于平板电脑、互联网电视等设备。

近年来，响应式网页设计已经成为一种流行的机制，通过使用信息客户端相应地调整布局，使网站和应用程序适应多种设备。从某种意义上说，这是一种“一刀切”的方法。然而，通常会出现为同一个站点提供不同版本或者使用稍微不同的方法的情况。

该问题的另一个解决方案是使用服务器端设备检测，然后根据该信息采取某些措施。一种可能性是简单地将对移动站点的请求转发到不同的 URL。另一种可能性是，在服务器上生成布局时，以编程方式修改布局，或者实际上修改内容。

采用服务器端方法是本文的基础，本文详细介绍了浏览器功能项目(browscapabilities Project，简称 Browscap ),以提供这些决策所依据的信息。

## 用户代理字符串介绍

在我们研究服务器端检测之前，我们需要理解 web 应用程序可用的信息，这些信息以用户代理字符串的形式出现。

当您发出 HTTP 请求从 web 浏览器中检索页面时，它包含一条名为*用户代理字符串*的信息。这提供了关于谁发出请求的信息，并且包括各种元素。

通常情况下，用户代理字符串指定使用什么浏览器——这是 analytics services 如何确定您使用的是 Chrome、Firefox、Safari、IE 还是众多其他浏览器中的一种。此外，它还可以提供有关请求应用程序版本的信息。例如，这对于识别 IE6 用户数量的减少非常有用。

当然，浏览器实际上只是一种特定类型的应用程序。请记住，HTTP 请求不只是在您的 web 浏览器访问网页或其他资源时发出的。请求可能来自网络爬虫、提要阅读器(即:RSS)、验证器、库(如 cURL)或潜在的无限数量的网络服务客户机之一。

用户代理字符串也用于提供有关操作系统的信息。Windows，Linux 还是 Mac？iOS 还是 Android？这也是由版本号限定的。

用户代理字符串通常还会提供关于正在使用的物理设备的信息，无论是台式机、笔记本电脑、手机还是平板电脑等。

让我们看一些用户代理字符串的例子:

`Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:24.0) Gecko/20100101 Firefox/24.0`

–这表示该请求是由 Firefox(具体来说是版本 24)从基于 Intel 的 Mac 发出的。

`Mozilla/5.0 (iPad; CPU OS 6_0 like Mac OS X) AppleWebKit/536.26 (KHTML, like Gecko) Version/6.0 Mobile/10A5355d Safari/8536.25`

–iPad 上的 Safari(运行 iOS6)

`Opera/9.80 (J2ME/MIDP; Opera Mini/9.80 (S60; SymbOS; Opera Mobi/23.348; U; en) Presto/2.5.25 Version/10.54`

–基于 Java 的手机上的 Opera Mini 版本 9

`Mozilla/5.0 (Linux; U; Android 4.0.3; ko-kr; LG-L160L Build/IML74K) AppleWebkit/534.30 (KHTML, like Gecko) Version/4.0 Mobile Safari/534.30`

–Android 手机上的移动 Safari

你可以在[useragentstring.com/pages/useragentstring.php](http://www.useragentstring.com/pages/useragentstring.php)在线查看数百个用户代理字符串。

## 访问浏览器数据

考虑到变量的数量、可能版本的数量，当然还有正在使用的不断扩大的物理设备范围，您会发现不同排列的数量非常大，而且每天都在增加。[浏览器功能项目](http://tempdownloads.browserscap.com/)试图对这些排列进行分类。你可以把它想象成一个用户代理字符串的数据库，它把用户代理字符串与我们可以从中收集到的一些信息联系起来。

实际上，它不仅仅是一个由用户代理字符串控制的数据库；它是一系列用于提取相关信息的规则和模式，与已知的浏览器、设备和其他信息进行交叉引用。

PHP 以`get_browser()`函数的形式提供了一个本地包装器来浏览数据。(参见[php.net/get-browser](http://php.net/get-browser))。然而，它的使用有两个主要缺点。因为 PHP 没有附带`browscap.ini`数据文件，所以您需要手动下载一个并设置您的 `php.ini`指向适当的位置。更重要的是，因为数据库定期更新，所以当它更新时，您需要手动替换它。

幸运的是，有一些库可以解决这两个问题，并且不需要任何额外的配置。这里有很多，但我推荐 github.com/browscap/browscap-php。

## 集成 Browscap 库

在下面的例子中，我将使用 Slim 框架。您可能更喜欢不同的框架，或者根本不喜欢，但是因为这篇文章是专门关于 Browscap 的，所以我不想在自动加载、路由或中间件等基础知识上陷入困境。这篇文章的[代码是可用的，所以你可以跟随。](https://github.com/phpmasterdotcom/ServerSideDetectionWithBrowscap)

最简单的入门方法是使用 Composer 下载依赖项。首先有两个 Slim 框架和 Browscap 库。创建一个项目目录，并在其根目录下创建一个`composer.json`文件来指定这些依赖关系:

```
{
    "require": {
        "slim/slim": "2.2.*",
        "browscap/browscap-php": "1.0.*@dev"
    }
}
```

(注意:在撰写本文时，Slim Framework 的当前版本是 2.3.0。然而，我故意使用 2.2.x 来兼容我们稍后将使用的布局视图库。)

然后运行 composer:

```
php composer.phar install
```

现在，您的项目根目录中应该有一个包含这两个库的 vendor 文件夹，以及一个自动加载器。在项目根目录下创建一个`public`文件夹，并在其中创建一个`index.php`文件。从包括自动加载器开始:

```
<?php
require '../vendor/autoload.php';
```

现在让我们创建一个瘦应用程序的基本框架，它非常简单:

```
$app = new SlimSlim();

$app->get('/', function () {
  print 'Hello World';
});

$app->run();
```

在这一阶段，您可能希望通过浏览新的索引页面来验证所有东西都已正确安装。

接下来，我们将设置 Browscap 库。这涉及三个关键任务:

*   下载实际数据文件(`browscap.ini`)
*   处理数据以提高查找效率
*   保持数据最新

由于库需要下载和创建一些文件，我们需要一个缓存目录。让我们保持简单，在项目根目录下创建一个缓存目录——只是不要忘记让它可被 web 服务器写入。

当我们构造一个`Browscap`类的实例时，我们指定缓存目录:

```
use phpbrowscapBrowscap;

// create a new Browscap object (loads or creates the cache)
$bc = new Browscap('../cache');
```

让我们通过抓取信息并将其直接转储到屏幕上来检查一下:

```
// get information about the current browser's user agent
$current_browser = $bc->getBrowser();

echo '<pre>';
print_r($current_browser);
echo '</pre>';
```

第一次运行可能需要一点时间，因为库需要下载 INI 文件并对其执行一些处理——幸运的是，后续请求会快得多。

如果您查看缓存目录，您会注意到两个文件；`browscap.ini`是原始数据文件，`cache.php`是相同的数据，但是为了更快的查找进行了优化。不同的库以不同的方式处理和优化 INI 数据——您可能希望尝试几个并执行基准测试，看看哪一个是最有效的。

## 解释 Browscap 数据

以下是上一节代码的输出示例:

```
stdClass Object
(
    [browser_name] => Mozilla/5.0 (Macintosh; Intel Mac OS X 10_7_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/27.0.1453.116 Safari/537.36
    [browser_name_regex] => ^mozilla/5.0 (.*intel mac os x.*) applewebkit/.* (khtml, like gecko).*chrome/27..*safari/.*$
    [browser_name_pattern] => Mozilla/5.0 (*Intel Mac OS X*) AppleWebKit/* (KHTML, like Gecko)*Chrome/27.*Safari/*
    [Parent] => Chrome 27.0
    [Platform] => MacOSX
    [Win32] => 
    [Comment] => Chrome 27.0
    [Browser] => Chrome
    [Version] => 27.0
    [MajorVer] => 27
    [MinorVer] => 0
    [Beta] => 1
    [Frames] => 1
    [IFrames] => 1
    [Tables] => 1
    [Cookies] => 1
    [JavaScript] => 1
    [JavaApplets] => 1
    [CssVersion] => 3
    [Platform_Version] => unknown
    [Alpha] => 
    [Win16] => 
    [Win64] => 
    [BackgroundSounds] => 
    [VBScript] => 
    [ActiveXControls] => 
    [isMobileDevice] => 
    [isSyndicationReader] => 
    [Crawler] => 
    [AolVersion] => 0
)
```

您在这里实际看到的内容当然取决于您的机器、您使用的浏览器以及版本。但是请注意，关键的信息片段已经被提取到返回对象的属性中，例如:

*   `browser_name`是原始用户代理字符串。注意这种模糊性——它同时提到了 Chrome 和 Safari 是哪一个？
*   告诉我们我正在使用 Chrome 浏览器
*   是我正在使用的 Chrome 版本
*   告诉我们我正在使用苹果麦金塔电脑

此外，我的设置还有许多特征。

`Tables`、`Cookies`、`Frames`、`IFrames`、`Javascript`、`JavaApplets`、`BackgroundSounds`、`VBScript`和`ActiveXControls`提供了一些关于我的浏览器功能的信息——也许有些比现在的其他浏览器更有用！

我们可以从`CssVersion`中收集更多关于浏览器功能的信息，它指定了浏览器支持的 CSS 规范的最高版本。

`isSyndicationReader`和`Crawler`有助于识别特定类型的应用程序。例如，如果你要写一个分析服务，你可能希望使用 Crawler 从你的访问者数据中排除那些“点击”。或者，当请求被可能是搜索引擎的爬虫识别时，大量缓存的应用程序或网站可能希望确保它是新鲜的。

是一个有趣的问题——也可能是最有用的。移动设备的实际构成有待解释，但一般来说，它会给我们所有重要的区别，比如说，台式电脑和移动电话。

处理`isMobileDevice`的“边缘案例”超出了本文的范围，而且通常取决于项目。例如，iPad 或 Android 平板电脑是否应该获得移动版网站的桌面，或者是否应该有特定的平板电脑版本？

我们在示例中调用了`getBrowser()`而没有任何附加参数，这意味着用户代理字符串将从请求中获取。如果您想试验或做一些额外的测试，您可以传递一个用户代理字符串作为第一个参数，例如:

```
$current_browser = $bc->getBrowser(
    "Opera/9.80 (J2ME/MIDP; Opera Mini/9.80 (S60; SymbOS; Opera Mobi/23.348; U; en) Presto/2.5.25 Version/10.54"
);
```

此外，将 true 作为第三个参数传递会导致该函数将 Browscap 数据作为数组而不是对象返回，作为其默认格式。

## 使用 Browscap 重定向到移动站点

现在让我们看一些如何使用 Browscap 数据的实际例子。假设你有一个单独的移动网站，可能在一个子域上——例如*m.example.com*——或者可能使用不同的 TLD，例如 *example.mobi.* 你可以简单地拦截所有对桌面站点的请求，并通过使用钩子将移动访问者重定向到相应的站点。在这种情况下，我们使用`slim.before`，它在运行 Slim 应用程序之前被调用:

```
$app->hook('slim.before', function () use ($app) {
    // create a new Browscap object (loads or creates the cache)
    $bc = new Browscap('../cache');

    // get information about the current browser's user agent
    $current_browser = $bc->getBrowser();

    // redirect to the mobile site if this is mobile
    if ($current_browser->isMobileDevice) {		
        $url = 'http://m.example.com';
        $app->response()->redirect($url, 301);
    }
});
```

当然，这在 iteslf 中并不十分有用；比方说，*http://example.com/about*的请求会被重定向到*http://m.example.com*，也就是说移动网站的主页。但是，如果您知道移动网站的网站结构与桌面网站的网站结构相匹配，您可以通过分析请求的 URL 使其更有用一些:

```
if ($current_browser->isMobileDevice) {
    $path = $app->request()->getResourceUri();
    $url = 'http://m.example.com' . $path;
    $app->response()->redirect($url, 301);
}
```

## 使用 Browscap 进行布局切换

Browscap 数据的另一个常见用途是为桌面和移动站点执行服务器端布局切换。让我们以我们的基于瘦的应用程序为例，做到这一点。

首先要做的是更新`composer.json`文件来指定一个新的依赖项，这是一个我们将用来实现布局的方便的小库:[github.com/petebrowne/slim-layout-view](http://github.com/petebrowne/slim-layout-view)。

```
{
    "require": {
        "slim/slim": "2.2.*",
        "browscap/browscap-php": "1.0.*@dev",
        "petebrowne/slim-layout-view": "0.1.*"
    }
}
```

现在运行:

```
php composer.phar update
```

这将为一个新的视图类安装代码，它将把我们的应用程序的输出包装在一个布局视图中。

现在在你的项目根目录下，创建一个`templates`目录。在那一个`layouts`目录中，还有那两个文件——`desktop.php`和`mobile.php`。

简单来说，`desktop.php`应该是这样的:

```
<!DOCTYPE html>
<html>
 <body>
  <h1>This is the Desktop Layout</h1>
  <?php echo $yield ?>
 </body>
</html>
```

`mobile.php`大概是这样的:

```
<!DOCTYPE html>
<html>
 <body>
  <h1>This is the Mobile Layout</h1>
  <?php echo $yield ?>
 </body>
</html>
```

这些都是非常简单的例子，但是它们足以测试布局切换。

现在将 Slim 的实例化改为:

```
$app = new SlimSlim(array(
  'view' => 'SlimLayoutView',
  'templates.path' => '../templates',
  'layout' => 'layouts/desktop.php'
));
```

我们在这里做的是告诉 Slim 使用布局，指定我们刚才创建的模板目录，并默认为我们的桌面布局。

现在在模板中创建一个名为`home.php`的文件，例如:

```
<p>Hello World!</p>
```

并将您的路线更改为:

```
$app->get('/', function () use ($app) {	
  $app->render('home.php');
});
```

访问该页面，您应该会看到标题“这是桌面布局”，然后是“Hello World！”。

好了，现在我们需要添加动态切换移动布局的能力。为此，我们将构建一些中间件。这个中间件将在请求生命周期中运行，负责检测移动访问者并相应地切换布局。

要创建中间件，我们只需扩展`SlimMiddleware`并定义一个`call()`方法。请记住，调用 Slim 中的下一个中间件也是每个中间件的责任。

这是我们的框架中间件:

```
class LayoutSwitcherMiddleware extends SlimMiddleware
{
    public function call() {
        // get reference to application
        $app = $this->app;

        // call the next middleware
        $this->next->call();
    }
}
```

现在我们需要将它添加到应用程序中，所以在我们实例化 Slim 框架之后，添加下面这行代码:

```
$app->add(new DeviceSwitcherMiddleware());
```

如果您访问该页面，设备切换器应该会运行——您可以随意回显一些内容来验证情况是否如此。

切换布局很简单；我们需要做的就是从 Browscap 库中获取设备信息，尝试识别一个移动访问者，如果是这样的话，就覆盖默认布局。

为此，在行`$this->next->call()`之前添加以下行:

```
// create a new Browscap object (loads or creates the cache)
$bc = new Browscap('../cache');

// get information about the current browser's user agent
$current_browser = $bc->getBrowser();

// switch to the corresponding layout if this is mobile
if (!$current_browser->isMobileDevice) {
    $app->config('layout', 'layouts/mobile.php');
}
```

如果您使用移动设备访问该页面，您应该会看到正在使用的移动布局。

在实践中，您的桌面和移动布局可以包括单独的样式表，以不同的方式处理导航，包括或排除区域，等等。

你们当中眼尖的人会注意到我犯了一个很大但大家都知道的疏忽。我认为——我希望这是一个普遍的观点——虽然默认情况下为移动网站提供服务很好，但如果移动用户愿意，他们应该总是可以访问桌面版本的网站。我不会讲太多细节，但是一种方法可能是提供一个带有标志的链接——例如*？desktop=true* ，它设置一个 cookie 来覆盖自动布局切换行为。

## 将服务器端布局切换与 RWD 相结合

尽管服务器端布局切换和响应式 web 设计是解决同一个问题的两种不同的方法，但是没有理由说它们不能统一使用。例如，没有理由不能为不同组的设备使用不同的布局，但也要保持这些布局的响应性——毕竟，即使在您可能对移动设备进行分类的情况下，屏幕分辨率等方面的差异也可能是巨大的。

## 另一个例子——一个软件网站

了解某人在浏览网站时使用的操作系统也很有用。让我们假设您正在构建一个简单的网站来推广一个适用于 Windows、Mac 和 Linux 的应用程序。在这种网站上，简化这种网站的下载页面以尝试和猜测用户正在寻找的版本是很常见的。例如，如果你使用 Mac 访问该网站，它会最显著地显示相应的下载链接——当然，不要忘记，人们可能仍然希望下载替代版本。

在模板文件中，有一种方法可以做到这一点:

```
<?php if (!strncmp($current_browser->Platform, 'Win', 3)): ?>

<p><a href="/downloads/windows.zip" class="btn btn-large btn-primary">Download for Windows</a></p>
<p>Alternatively, download for <a href="/downloads/mac.dmg">Mac</a> or <a href="/downloads/linux.tar.gz">Linux</a>.</p>

<?php elseif (!strncmp($current_browser->Platform, 'Mac', 3)): ?>

<p><a href="/downloads/mac.dmg" class="btn btn-large btn-primary">Download for Mac</a></p>
<p>Alternatively, download for <a href="/downloads/windows.zip">Windows</a> or <a href="/downloads/linux.tar.gz">Linux</a>.</p>

<?php elseif ($current_browser->Platform == 'Linux'): ?>

<p><a href="/downloads/linux.tar.gz" class="btn btn-large btn-primary">Download for Linux</a></p>
<p>Alternatively, download for <a href="/downloads/windows.zip">Windows</a> or <a href="/downloads/mac.dmg">Mac</a>.</p>

<?php else: ?>

<p>Select your version:</p>
<ul>
 <li><a href="/downloads/windows.zip">Windows</a></li>
 <li><a href="/downloads/mac.dmg">Mac</a></li>
 <li><a href="/downloads/linux.tar.gz">Linux</a></li>
</ul>

<?php endif; ?>
```

您将在附带的代码中找到它的实现(带有样式和图标)。

Windows 计算机可以用各种字符串来表示，例如 Win2000、WinXP、WinVista、Win7…因此，我们尝试通过查看字符串的开头来识别 Windows。另一种方法是检查 Browscap 设备数据中的 Win16、Win32 和 Win64 标志。当然，如果你的目标是不同版本的 Windows，例如分离 Vista 和 Windows 7 版本，你可以分别检查 WinVista 和 Win7。

## 最后一个例子

这一个是相当不言自明的！

```
$current_browser = $bc->getBrowser();
if (($current_browser->Browser == 'IE') && ($current_browser->MajorVer == 6)) {
    header('Location: http://www.ie6countdown.com/');
    exit;
}
```

## 摘要

在本文中，我介绍了服务器端设备检测的概念，详细介绍了浏览器功能项目(Browscap)。我已经展示了如何使用它来替代——甚至补充——客户端技术，使网站适应多种设备，例如响应式 Web 设计。虽然我的例子使用了 Slim 框架，但是无论您喜欢哪种方法，原则都是一样的。

我也给了你一些关于如何使用 Browscap 数据的想法——如果你能想到其他的，一定要在评论中告诉我！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章