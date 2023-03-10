# Assetic 入门

> 原文：<https://www.sitepoint.com/getting-started-assetic/>

曾经有一段时间，资产管理仅仅意味着在 HTML 中插入一两个`<link>`标签和几个`<script>`标签。

然而现在，这种方法已经不够用了。首先是性能。在文件大小和 HTTP 请求数量方面，资产是一个显著的性能消耗——优化这两个方面已经成为开发过程的一个重要部分。移动设备的激增加剧了这一问题。

此外，随着客户端应用程序变得越来越复杂，管理脚本和库之间的依赖关系也变得越来越复杂。

此外，Less、Compass 和 Coffeescript 等技术需要编译资产，这又增加了管理资产的过程。

在本文中，我将介绍一个名为 [Assetic](https://github.com/kriswallsmith/assetic) 的 PHP 包，它可以帮助管理、编译和优化脚本、样式表和图像等资产。

## 什么是资产管理？

让我们先来看看资产管理实际上是什么。

简而言之，它是一种管理网站或 web 应用程序(或特定页面)所需资源及其依赖关系的方式。这可能包括确保特定的库对您的 JavaScript 可用，或者确保在应用其他样式之前应用 CSS 重置。

从几个不同的阶段来看资产管理有时会有所帮助。首先是编译，如果你使用的是 CSS 框架，比如 Less、SASS、SCSS、Stylus、Compass 等，或者是 Coffeescript 或 [Dart](https://www.dartlang.org) 之类的东西，这是一个必要的步骤。在一些基于 JavaScript 的工具中，还有一些额外的编译步骤；例如编译把手、树枝或胡子模板。

下一个阶段是优化。对于 CSS，通常在进入生产模式时会缩小样式表文件。对于 JavaScript，您可能会缩小或打包您的源文件。使用各种压缩算法通常可以显著减小图像的大小，在某些情况下，还可以通过对图像进行 BASE-64 编码并将数据嵌入到样式表中来减少 HTTP 请求的数量。

最后，串联。减少 HTTP 请求的数量可以显著提高性能，因此 CSS 和 JavaScript 文件通常会连接在一起，形成一个针对每种类型的大型文件。构建“精灵”的技术将同样的原理应用于图像，尽管这超出了本文的范围。

## 介绍 Assetic

Assetic 是一个 PHP 软件包，旨在帮助资产管理过程。

在开始使用 Assetic 时，有几个重要的概念需要理解——**集合**和**过滤器**。

一个**集合**将一堆资产组合在一起。单个文件是一种资产。Nix 用户尤其熟悉 glob，它本质上是一种收集文件名与特定模式相匹配的一堆文件的方法；Assetic 也将这些视为资产。例如，glob pattem`/assets/js/*.js`指的是`/assets/js`中扩展名为`.js`的所有文件。

一个**过滤器**被应用于一个集合，以某种方式修改它。如果您回头参考资产管理过程中的阶段的概念，您可以将每个阶段看作是由一个或多个过滤器实现的。例如，一个过滤器可能负责编译，一个负责缩小。

不同的过滤器通常应用于不同的集合。例如，您可能有一个包含以下内容的项目:

1.  使用 Less 过滤器编译的一组`.less`文件，使用 CSS 缩小过滤器缩小并连接成一个 CSS 文件。
2.  一组简单缩小的`.css`文件
3.  Coffeescript 文件集合使用 Coffeescript 过滤器编译，使用 Google Closure 编译器过滤器缩小，然后连接成一个`.js`文件。

文件类型和过滤器的确切组合将取决于您使用的技术和您的个人偏好，例如您的首选微型过滤器。

Assetic 提供了对众多模块的支持，让您可以灵活地选择自己喜欢的方法。例如，是否使用 JSMin、YUI 压缩程序或 Google Closure 编译器来压缩 JavaScript 取决于你自己。

## 安装 Assetic

你可以从 Github 下载或克隆[来安装 Assetic，或者更好的方法是通过 Composer:](https://github.com/kriswallsmith/assetic)

```
"kriswallsmith/assetic": "1.2.*@dev" 
```

Packagist 上也有针对框架的 Assetic 软件包，如 [Zend Framework](https://packagist.org/packages/widmogrod/zf2-assetic-module) 、 [Symfony](https://packagist.org/packages/symfony/assetic-bundle) 、 [Laravel](https://packagist.org/packages/barryvdh/laravel-assetic) 和 [Silex](https://packagist.org/packages/mheap/silex-assetic) 。

## 使用

让我们从一个简单的例子开始——创建一个资产集合来连接一组 JavaScript 文件:

```
$js = new AssetCollection(array(
    new GlobAsset('/assets/js/libs/*'),
    new FileAsset('/assets/js/app.js'),
));

print $js->dump(); 
```

上面的代码从`/assets/js/libs`中取出所有的`.js`文件，然后是文件`/assets/js/app.js`，并在调用`dump()`时将它们连接起来——我们很快就会看到您对输出做了什么。

您可以通过添加一个过滤器来扩展上面的示例——在本例中，它使用 JSMin 来缩小生成的 JavaScript:

```
$scripts = new AssetCollection(array(
    new GlobAsset('/assets/js/libs/*'),
    new FileAsset('/assets/js/app.js'),
), array(
    new JSMinFilter(),
));

print $scripts->dump(); 
```

> 注意:如果你希望使用 JSMin，你可以使用下面的 PHP 实现:
> `"lmammino/jsmin4assetic": "1.0.*@dev"`(在 Github [上查看这里](https://github.com/lmammino/JsMin4Assetic))。

鉴于过滤器的行为不同，您的样式表将需要一个单独的资产集合。假设您有一堆 CSS 文件，并且您正在使用 Less 来定义您的网格:

```
$styles = new AssetCollection(array(
    new FileAsset('/assets/less/grid.less', array(new LessFilter())),
    new GlobAsset('/assets/css/*'),
), array(
    new Yui\CssCompressorFilter('/path/to/yuicompressor.jar'),
));

print $styles->dump(); 
```

它使用`LessFilter`来编译`grid.less`，将它与`/assets/css`中的所有`.css`文件连接在一起，然后使用 [YUI 压缩器](http://yui.github.io/yuicompressor/)来压缩整个文件。

> 请注意，为了让上面的例子正常工作，您需要确保您已经正确安装了 YUI 压缩程序，确保其`.jar`文件的路径是正确的，并将以下内容添加到您的`composer.json` : `"leafo/lessphp": "0.4.*@dev"`

如果你更愿意使用 CssMin 而不是 YUI 压缩器，你需要[安装它](https://code.google.com/p/cssmin/)，你的代码变成:

```
$styles = new AssetCollection(array(
    new FileAsset('/assets/less/grid.less', array(new LessFilter())),
    new GlobAsset('/assets/css/*'),
), array(
    new CssMinFilter(),
));

print $styles->dump(); 
```

如果您使用的是 SASS 而不是 Less，通过将以下内容添加到您的`composer.json`中来安装[scsphp](https://github.com/leafo/scssphp):

```
"leafo/scssphp": "dev-master" 
```

然后，您可以使用`ScssphpFilter`来编译您的 SASS 资产。

你也可以优化图像。在这个例子中，我们使用 glob 从`assets/img`获取所有图像文件，并用 [OptiPng](http://optipng.sourceforge.net/) 压缩它们，然后用 Jpegs 做同样的事情，但使用 [Jpegoptim](http://freecode.com/projects/jpegoptim) :

```
$images = new AssetCollection(array(
    new GlobAsset('/assets/img/*.png', array(new OptiPngFilter())),
    new GlobAsset('/assets/img/*.jpg', array(new JpegoptimFilter()),
)); 
```

## 管理器类和资产工厂

还有其他方法来配置你的资产。`AssetManager`类本质上是一个资产的键控存储库——例如，您可以收集我们在上一节中定义的资产集合，如下所示:

```
$am = new AssetManager();
$am->set('jquery', new FileAsset('/path/to/jquery.js'));
$am->set('scripts', $scripts);
$am->set('styles', $styles); 
```

`FilterManager`类的工作方式大致相同:

```
$fm = new FilterManager();
$fm->set('sass', new SassFilter('/path/to/parser/sass'));
$fm->set('yui_css', new Yui\CssCompressorFilter('/path/to/yuicompressor.jar')); 
```

然后您可以使用`AssetFactory`类来简化整个过程:

```
use Assetic\Factory\AssetFactory;

$factory = new AssetFactory('/path/to/asset/directory/');
$factory->setAssetManager($am);
$factory->setFilterManager($fm);
$factory->setDebug(true);

$styles = $factory->createAsset(array(
    '@reset',         // load the asset manager's "reset" asset
    'css/src/*.scss', // load every scss files from "/path/to/asset/directory/css/src/"
), array(
    'scss',           // filter through the filter manager's "scss" filter
    '?yui_css',       // don't use this filter in debug mode
));

echo $styles->dump(); 
```

## 输出

我已经展示了如何选择您想要包含的内容以及如何应用过滤器；但是我还没有看到你在哪里这样做或者你如何引用它。有很多方法可以采用。

一种可能是在一个可公开访问的目录中创建一个 PHP 文件，例如`JavaScripts.php`，并从您的 HTML 中引用它，就像它是一项资产一样:

```
<script src="/assets/JavaScripts.php"></script> 
```

或者，您可以创建一个名为`stylesheets.php`的文件并内嵌它:

```
<style>
<?php include('/assets/stylesheets.php');
</style> 
```

或者，您可以生成`.css`和`.js`文件，并像平常一样引用它们。您可以使用`AssetWriter`来实现这一点:

```
use Assetic\AssetWriter;

$scripts.js = new AssetCollection(array(
    new GlobAsset('/assets/js/libs/*'),
    new FileAsset('/assets/js/app.js'),
), array(
    new JSMinFilter(),
));

// Set target path, relative to the path passed to the
// AssetWriter constructor as an argument shortly
$scripts->setTargetPath('scripts.js');
$am->set('scripts.js', $scripts.js);

// see above for instantiation of $styles
$styles->setTargetPath('stylesheets.css');
$am->set('styles', $styles);

$writer = new AssetWriter('/assets/build');
$writer->writeManagerAssets($am); 
```

作为工作流的一部分，您可以创建一个命令行脚本来完成这项工作，或者使用诸如 [Guard](https://github.com/guard/guard) 之类的工具来“监视”文件系统，并在相关文件之一发生更改时重新运行它。

## 贮藏

Assetic 附带了一个简单的基于文件的缓存机制，以确保过滤器不会不必要地运行。下面是一个缓存 YUI 压缩器输出的示例:

```
use Assetic\Asset\AssetCache;
use Assetic\Asset\FileAsset;
use Assetic\Cache\FilesystemCache;
use Assetic\Filter\Yui;

$yui = new Yui\JsCompressorFilter('/path/to/yuicompressor.jar');
$js = new AssetCache(
    new FileAsset('/path/to/some.js', array($yui)),
    new FilesystemCache('/path/to/cache')
);

// the YUI compressor will only run on the first call
$js->dump();
$js->dump();
$js->dump(); 
```

## 摘要

在本文中，我介绍了 asset IC——一个用于管理资产的 PHP 包。我已经展示了如何使用它来管理依赖性、运行编译过程、缩小/打包/压缩/优化资产，以及连接文件以最小化 HTTP 请求的数量。请务必[查阅文档](https://github.com/kriswallsmith/assetic)，了解所有可用过滤器的详细信息；或者，你甚至可以考虑实现`FilterInterface` /扩展`BaseFilter`来定义你自己的。对于补充它的软件包，当你第一次安装它时参考推荐的软件包，或者通过检查它的`composer.json`文件的`suggests`部分。

## 分享这篇文章