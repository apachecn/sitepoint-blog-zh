# 鲍尔 vs 鲍尔弗普

> 原文：<https://www.sitepoint.com/bower-vs-bowerphp/>

2014 年 10 月 28 日，全世界的小狗自发地燃烧起来——或者说这个社区想让你相信。所发生的是[鲍尔弗普](http://bowerphp.org/)的*揭露*(我羞于称任何东西为“阿尔法”发布)，这就是为什么它一点也不像某些人想让你相信的[世界末日](http://www.reddit.com/r/PHP/comments/2kknrg/bowerphp_powered_by_beelab/)。

![](img/5bb122b662a131bb6a47b4337c4ebdcd.png)

## 什么？

BowerPHP 是基于 NodeJS 的前端包管理器 Bower 的 PHP 版本。我们之前介绍过 [Bower](https://www.sitepoint.com/package-management-for-the-browser-with-bower/) ,但本质上，您可以使用它来安装前端库，如 jQuery、Angular 或 Foundation，就像您使用 Composer 进行 PHP 依赖一样。您定义一个包含依赖项的 Bower 文件，运行`bower install`，然后观察奇迹的发生。

这些包通常会被安装在一个`bower_components`文件夹中，如果这个文件夹不存在，你可以直接链接到它们，或者在这样做之前通过一些附加的连接/缩小过滤器来减少请求的数量和下载的大小。

那么…如果存在节点版本，为什么是 PHP 版本呢？好问题——但是在回答这个问题之前，让我们安装 BowerPHP 并使用它来引入一些包。

## 安装

我将使用我们官方的[家园改良版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)盒子。如果你不想，你可以不做，但是这是开始跟随的最简单的方法。

为了使 BowerPHP 对我们的系统全局可用，我们执行以下命令:

```
composer global require "beelab/bowerphp 0.1.*@alpha"
```

等等，什么？就这样？

没错。

我们在几秒钟内就完成了 NPM 需要几分钟才能完成的安装，如果它能在 Windows 上托管的虚拟机上运行的话。

执行`bowerphp`看看是否有效。

## 使用

要查看一个示例用例，让我们来尝试一下。我们将在一个可从浏览器访问的项目文件夹中创建一个“Hello World”`index.html`文件，在我们的例子中，通过`homestead.app:8000`访问`Code/Laravel/public`。

```
cd ~/Code
mkdir -p Laravel/public
cd Laravel/public
echo "Hello World" > index.html
```

现在在浏览器中访问`homestead.app:8000/`应该会显示“Hello World”。

让我们安装基础。

```
bowerphp install foundation
```

![](img/8feee4c15db5adffb55ee12c33a2cae5.png)

呃…什么？没问题吗？但是..我在 Windows 里面运行一个流浪盒子！警告、漏洞、许可问题在哪里？由 [NPM 笨拙的目录结构](https://github.com/npm/npm/issues/3697)导致的错误都在哪里？鲍尔弗普只用了 15 秒钟就为我安装好了粉底。

让我们通过在 HTML 中包含资产来看看是否一切正常。用以下内容替换`index.html`的内容:

```
<!DOCTYPE html>
<html class="no-js" lang="en">
<head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>MY_APP</title>
    <meta name="description" content="MY_DESCRIPTION">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    <!-- Place favicon.ico and apple-touch-icon.png in the root directory -->

    <link rel="stylesheet" href="bower_components/foundation/css/normalize.css">
    <link rel="stylesheet" href="bower_components/foundation/css/foundation.css" />

    <script src="bower_components/foundation/js/vendor/modernizr.js"></script>
</head>
<body>
<!--[if lt IE 7]>
<p class="browsehappy">You are using an <strong>outdated</strong> browser. Please <a href="http://browsehappy.com/">upgrade your browser</a> to improve your experience.</p>
<![endif]-->

<div class="off-canvas-wrap" data-offcanvas>
    <div class="inner-wrap">
        <nav class="tab-bar">
            <section class="left-small">
                <a class="left-off-canvas-toggle menu-icon" href="#"><span></span></a>
            </section>

            <section class="middle tab-bar-section">
                <h1 class="title">MY_APP</h1>
            </section>

            <section class="right-small">
                <a class="right-off-canvas-toggle menu-icon" href="#"><span></span></a>
            </section>
        </nav>

        <aside class="left-off-canvas-menu">
            <ul class="off-canvas-list">
                <li><label>Sections</label></li>
                <li><a href="#">Dashboard</a></li>
                <li><a href="#">Author List</a></li>
            </ul>
        </aside>

        <aside class="right-off-canvas-menu">
            <ul class="off-canvas-list">
                <li><label>Users</label></li>
                <li><a href="#">Hari Seldon</a></li>
                <li><a href="#">...</a></li>
            </ul>
        </aside>

        <section class="main-section" id="content">
            <div id="charts"></div>
            <div class="panel"><p>Hello</p></div>
        </section>

        <a class="exit-off-canvas"></a>

    </div>
</div>

<script src="bower_components/jquery/dist/jquery.min.js"></script>

<script src="bower_components/foundation/js/foundation.min.js"></script>
<script src="bower_components/foundation/js/foundation/foundation.offcanvas.js"></script>

<script> $(document).foundation(); </script>

</body>
</html>
```

在浏览器中打开该页面，检查它是否工作:

![](img/c572dbc73721e96235f1ce4798618f72.png)

## 为什么它很棒

回答我们上面的问题——为什么要用 PHP 版本的 JS 工具？因为*需要*。当然，很多人并不需要它，我作为一个经历过 Windows 上 NPM 的 symlink 和 filepath-hell 的人，可能比大多数人更欣赏它，但我仍然很高兴它的存在，并希望它继续发展。如果你正在寻找一个更直接的清单，列出在**鲍尔和鲍尔弗普**战斗中的利与弊:

BowerPHP 的优点:

*   不需要使用 JS
*   与 Windows 上托管的虚拟机兼容
*   非常快
*   易于安装
*   高度可移植的 PHP 应用程序——编写一个 Bowerfile，将 BowerPHP 添加到您的`composer.json`文件中，并将`bowerphp install`放入 Composer 的`post-install-script`块中，您将在一个`composer install`之后立即安装项目的前端和后端——无需接触节点，甚至不知道它存在于系统中

BowerPHP 的缺点:

*   也许有一天，Bower 会把一些深奥的未来功能留给 BowerPHP

老实说，我想不出任何其他的缺点。可以吗？让我知道。

## 结论

鲍尔弗普很酷。这是一个雄心勃勃的“车轮的重新发明”,但这一次，车轮的形状和形式保持不变——只是材料从轻木变成了橡木，天哪，它变得更好了。社区对这个项目的抵制让我想起了 [Beau 在论坛 PHP](https://www.sitepoint.com/forum-php-report/) 上的发言——坚持下去，直到你找到支持，这里希望这篇赞扬和击掌的帖子让团队知道他们不是唯一需要无节点节点工具的人，他们应该继续做他们正在做的事情。

有了 BowerPHP，我们可以像安装后端软件包一样简单地安装我们的前端资产——结合 Assetic 的过滤、缩小和其他功能，BowerPHP 将成为您的前端超级工具。

你怎么看鲍尔弗普？无用的项目还是急需的工具？为什么？在评论里告诉我们吧！

## 分享这篇文章