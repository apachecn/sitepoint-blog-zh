# 掌握 Composer–提示和技巧

> 原文：<https://www.sitepoint.com/mastering-composer-tips-tricks/>

Composer 革新了 PHP 中的包管理。它提升了可重用性游戏，帮助全世界的 PHP 开发人员生成与框架无关的、完全可共享的代码。但是很少有人超越基础，所以这篇文章将会涵盖一些有用的提示和技巧。

![](img/87fe6ffc090334a7aafa1a3e476c8acd.png)

## 全球的

尽管在文档中有明确的定义，但是 Composer 可以(并且在大多数情况下应该)在全球范围内安装。全球安装意味着不用打字

```
php composer.phar somecommand
```

你可以直接打出来

```
composer somecommand
```

在任何项目中。这使得在文件系统的任何位置使用`create-project`命令启动新项目变得非常容易。

要全局安装 Composer，请遵循这些说明[。](https://getcomposer.org/doc/00-intro.md#globally)

## 初始化

要在项目中创建新的`composer.json`文件(从而初始化一个新的 Composer 支持的项目)，您可以使用:

```
composer init
```

你也可以在[中传递一些选项](https://getcomposer.org/doc/03-cli.md#init)作为默认值。

## 以正确的方式安装软件包

当阅读项目的教程或自述文件时，许多人会说:

```
Just add the following to your composer.json file:

{
	"require": {
		"myproject": "someversion"
	}
}
```

但是这有几个缺点。第一，复制粘贴可能会引入一些错误。第二，对于一个新手来说，如果你的项目中已经有了一个庞大的`composer.json`文件，那么确定在哪里放置代码可能会很繁琐，并且还会引入错误。最后，许多人将第一次在命令行中遇到 Composer，因此涵盖他们可能发现自己的所有用例是不可行的(他们有 GUI 文本编辑器还是在命令行中？如果是后者，他们是否安装了文本编辑器，如果是，是哪一个？你是解释编辑程序还是就这样不管了？如果他们的项目中不存在该文件怎么办？您是否也应该包括文件的创建？).

向`composer.json`文件添加新需求的最佳方式是使用`require command`:

```
composer require somepackage/somepackage:someversion
```

这会将所有需要的内容添加到文件中，绕过所有手动干预。

如果你需要添加包到`require-dev`，添加`--dev`选项，就像这样:

```
composer require phpunit/phpunit --dev
```

`require`命令支持一次添加几个包，用空格隔开即可。请注意，这种方法不需要指定版本，如上面的代码片段所示——以这种方式安装软件包会自动获取软件包的最新版本，并告诉您它选择了哪个版本。

## 锁定文件

`composer.lock`文件保存了当前已安装软件包的列表，这样当其他人在依赖项可能已经更新的日期克隆您的项目时，他们仍然会安装旧版本。这有助于确保每个获取您的项目的人都拥有与您开发项目时完全相同的包环境，从而避免由于版本更新而产生的任何错误。

`composer.lock`应该[几乎总是](https://blog.engineyard.com/2014/composer-its-all-about-the-lock-file)致力于版本控制。[也许是](https://philsturgeon.uk/php/2014/11/04/composer-its-almost-always-about-the-lock-file/)。

`composer.lock`还包含了`composer.json`文件的散列，所以如果你只更新项目作者，或者一些联系信息，或者一个描述，你会得到一个关于锁文件与`json`文件不匹配的警告——在这种情况下，运行`composer update --lock`会有所帮助，只更新`lock`文件，不涉及其他任何东西。

## 版本标志

在定义[包版本](https://getcomposer.org/doc/01-basic-usage.md#package-versions)时，可以使用精确匹配(`1.2.3`)、带操作符的范围(`<1.2.3`)、操作符组合(`>1.2.3 <1.3`)、最佳可用(`1.2.*`)、波浪号(`~1.2.3`)和脱字符号(`^1.2.3`)。

后两个可能需要进一步解释:

*   波浪号(`~1.2.3`)将升级到版本`1.3`(不包括在内)，因为在[中，语义版本](http://semver.org)是引入新特性的时候。代字号获取已知的最高稳定次要版本。正如文件所说，我们可以认为它是**只有**最后指定的数字是允许改变的。

*   脱字符(`^1.2.3`)表示“只需小心破坏变更”，因此将升级到版本`2.0`。根据 semver 的说法，这是引入突破性变化的时候，所以`1.3`、`1.4`和`1.9`都没问题，而`2.0`就不行了。

除非你知道你需要一个特定的版本，否则我建议总是使用`~1.2.3`格式——这是你最安全的选择。

## 配置和全局配置

默认值不是一成不变的。详见`config` [参考](https://getcomposer.org/doc/04-schema.md#config)全文。

例如，通过指定:

```
{
    "config": {
        "optimize-autoloader": true
    }
}
```

您强制 Composer 在每次安装/更新后，或者换句话说，每当生成自动加载文件时，优化 classmap。这比生成默认的自动加载程序要慢一点，并且随着项目的增长速度会变慢。

另一个有用的选项可能是`cache-files-maxsize`——在 eZ Publish 或 Symfony 这样的大型项目中，缓存可能会很快变满。增加大小将使 Composer 快速运行的时间更长。

注意，配置也可以全局设置，所以它在项目之间是一致的。见[此处](https://getcomposer.org/doc/03-cli.md#config)了解详情。例如，要将缓存大小设置添加到我们的全局配置中，我们要么编辑`~/.composer/config.json`要么执行:

```
composer config --global cache-files-maxsize "2048MiB"
```

## 概要和详细

您可以在使用 Composer 在命令行上执行的任何命令中添加一个`--profile`标志，它不仅会产生这样的最终输出:

```
[174.6MB/54.70s] Memory usage: 174.58MB (peak: 513.47MB), time: 54.7s
```

而且在它输出的每一行前面加上到目前为止命令执行的总持续时间，加上内存使用量:

```
[175.9MB/54.64s] Installing assets for Sensio\Bundle\DistributionBundle into web/bundles/sensiodistribution
```

我经常使用这个命令来识别瓶颈包，并观察不同版本的 PHP 上的统计数据是如何提高还是降低的。

同样，`--verbose`标志将确保 Composer 在执行每个操作时输出更多的信息，帮助您准确理解*和*正在发生的事情。有些人甚至在他们的`composer`命令中添加了默认的`composer --verbose --profile`。

## 自定义源

有时，如果您的项目还没有安装在 Packagist 上，您只想从 Github repo 安装。也许正在开发，也许是本地托管，谁知道呢。为此，请参见我们的指南。

同样，如果你有一个流行项目的自己的版本，而你的项目的另一部分依赖于它，你可以使用自定义源代码结合内联别名来伪造版本约束，就像 Matthieu Napoli [在这里所做的](http://mnapoli.fr/overriding-dependencies-with-composer/)。

## 加速编写器

根据马克·范·埃克的这个绝妙的技巧，你可以通过让 Composer 在 HHVM 上运行来加速它的执行。

另一种方法是强迫它使用`--prefer-dist`来下载一个项目的稳定的打包版本，而不是从它所在的版本控制系统中克隆它(慢得多)。不过，默认情况下这是打开的，所以您不需要在稳定的项目中指定它。如果您想下载源代码，请使用`--prefer-source`标志。更多信息在`install`命令[的选项里](https://getcomposer.org/doc/03-cli.md#install)这里。

## 让您的 Composer 项目更轻松

如果你是开发作曲家友好项目的人，你可能也想尽自己的一份力量。基于[这个 Reddit 线程](https://www.reddit.com/r/PHP/comments/2jzp6k/i_dont_need_your_tests_in_my_production/)，你可以使用一个`.gitattributes`文件来忽略上面`--prefer-dist`模式打包过程中的一些文件和文件夹。

```
/docs               export-ignore
/tests              export-ignore
/.gitattributes     export-ignore
/.gitignore         export-ignore
/.travis.yml        export-ignore
/phpunit.xml        export-ignore
```

这是如何工作的？当你上传一个项目到 Github，它会自动提供“下载 zip”按钮，你可以用它来下载你的项目档案。更重要的是，Packagist 使用这些自动生成的归档文件来获取`--prefer-dist`依赖项，然后在下载后将其解压缩(比克隆快得多)。如果你因此忽略了你的测试、文档和其他逻辑上不相关的文件，把它们列在`.gitattributes`中，文档就不会包含它们，变得非常非常轻。

自然，希望调试你的库或者运行它的测试的人应该指定`--prefer-source`标志。

[PHP legue](http://thephpleague.com/)已经采用了这种方法，并将其包含在他们的[包框架](https://github.com/thephpleague/skeleton/blob/master/.gitattributes)中，因此任何基于此的项目都自动“dist friendly”。

## 显示

如果您忘记了您正在运行的 PHP 或其扩展的版本，或者需要您已经安装在当前项目中的所有项目(及其描述)及其版本的列表，您可以分别使用带有`--platform`(短`-p`)和`--installed`(短`-i`)标志的`show`命令:

```
$ composer show --installed

behat/behat                            v3.0.15            Scenario-oriented BDD framework for PHP 5.3
behat/gherkin                          v4.3.0             Gherkin DSL parser for PHP 5.3
behat/mink                             v1.5.0             Web acceptance testing framework for PHP 5.3
behat/mink-browserkit-driver           v1.1.0             Symfony2 BrowserKit driver for Mink framework
behat/mink-extension                   v2.0.1             Mink extension for Behat
behat/mink-goutte-driver               v1.0.9             Goutte driver for Mink framework
behat/mink-sahi-driver                 v1.1.0             Sahi.JS driver for Mink framework
behat/mink-selenium2-driver            v1.1.1             Selenium2 (WebDriver) driver for Mink framework
behat/sahi-client                      dev-master ce7bfa7 Sahi.js client for PHP 5.3
behat/symfony2-extension               v2.0.0             Symfony2 framework extension for Behat
behat/transliterator                   v1.0.1             String transliterator
components/bootstrap                   3.3.2              The most popular front-end framework for developing responsive, mobile first projects on the web.
components/jquery                      2.1.3              jQuery JavaScript Library
doctrine/annotations                   v1.2.4             Docblock Annotations Parser
doctrine/cache                         v1.4.1             Caching library offering an object-oriented API for many cache backends
doctrine/collections                   v1.3.0             Collections Abstraction library
doctrine/common                        v2.5.0             Common Library for Doctrine projects
doctrine/dbal                          v2.5.1             Database Abstraction Layer
doctrine/doctrine-bundle               v1.4.0             Symfony DoctrineBundle
doctrine/doctrine-cache-bundle         v1.0.1             Symfony2 Bundle for Doctrine Cache
doctrine/inflector                     v1.0.1             Common String Manipulations with regard to casing and singular/plural rules.
doctrine/instantiator                  1.0.4              A small, lightweight utility to instantiate objects in PHP without invoking their constructors
doctrine/lexer                         v1.0.1             Base library for a lexer that can be used in Top-Down, Recursive Descent Parsers.
egulias/listeners-debug-command-bundle 1.9.1              Symfony 2 console command to debug listeners
ezsystems/behatbundle                  dev-master bd95e1b Behat bundle for help testing eZ Bundles and projects
ezsystems/comments-bundle              dev-master 8f95bc7 Commenting system for eZ Publish
ezsystems/demobundle                   dev-master c13fb0b Demo bundle for eZ Publish Platform
ezsystems/demobundle-data              v0.1.0             Data for ezsystems/demobundle
ezsystems/ezpublish-kernel             dev-master 3d6e48d eZ Publish API and kernel. This is the heart of eZ Publish 5.
ezsystems/platform-ui-assets-bundle    v0.5.0             External assets dependencies for PlatformUIBundle
ezsystems/platform-ui-bundle           dev-master 4d0442d eZ Platform UI Bundle
ezsystems/privacy-cookie-bundle        v0.1               Privacy cookie banner integration bundle into eZ Publish/eZ Platform
fabpot/goutte                          v1.0.7             A simple PHP Web Scraper
friendsofsymfony/http-cache            1.3.1              Tools to manage cache invalidation
friendsofsymfony/http-cache-bundle     1.2.1              Set path based HTTP cache headers and send invalidation requests to your HTTP cache
guzzle/guzzle                          v3.9.3             PHP HTTP client. This library is deprecated in favor of https://packagist.org/packages/guzzlehttp/guzzle
hautelook/templated-uri-bundle         2.0.0              Symfony2 Bundle that provides a RFC-6570 compatible router and URL Generator.
hautelook/templated-uri-router         2.0.1              Symfony2 RFC-6570 compatible router and URL Generator
imagine/imagine                        0.6.2              Image processing for PHP 5.3
incenteev/composer-parameter-handler   v2.1.0             Composer script handling your ignored parameter file
instaclick/php-webdriver               1.0.17             PHP WebDriver for Selenium 2
jdorn/sql-formatter                    v1.2.17            a PHP SQL highlighting library
knplabs/knp-menu                       v1.1.2             An object oriented menu library
knplabs/knp-menu-bundle                v1.1.2             This bundle provides an integration of the KnpMenu library
kriswallsmith/assetic                  v1.2.1             Asset Management for PHP
kriswallsmith/buzz                     v0.13              Lightweight HTTP client
league/flysystem                       0.5.12             Many filesystems, one API.
liip/imagine-bundle                    1.2.6              This Bundle assists in imagine manipulation using the imagine library
monolog/monolog                        1.13.1             Sends your logs to files, sockets, inboxes, databases and various web services
nelmio/cors-bundle                     1.3.3              Adds CORS (Cross-Origin Resource Sharing) headers support in your Symfony2 application
ocramius/proxy-manager                 0.5.2              A library providing utilities to generate, instantiate and generally operate with Object Proxies
oneup/flysystem-bundle                 v0.4.2             Integrates Flysystem filesystem abstraction library to your Symfony2 project.
pagerfanta/pagerfanta                  v1.0.3             Pagination for PHP 5.3
phpdocumentor/reflection-docblock      2.0.4              
phpspec/prophecy                       v1.4.1             Highly opinionated mocking framework for PHP 5.3+
phpunit/php-code-coverage              2.0.16             Library that provides collection, processing, and rendering functionality for PHP code coverage information.
phpunit/php-file-iterator              1.4.0              FilterIterator implementation that filters files based on a list of suffixes.
phpunit/php-text-template              1.2.0              Simple template engine.
phpunit/php-timer                      1.0.5              Utility class for timing
phpunit/php-token-stream               1.4.1              Wrapper around PHP's tokenizer extension.
phpunit/phpunit                        4.6.4              The PHP Unit Testing framework.
phpunit/phpunit-mock-objects           2.3.1              Mock Object library for PHPUnit
psr/log                                1.0.0              Common interface for logging libraries
qafoo/rmf                              1.0.0              Very simple VC framework which makes it easy to build HTTP applications / REST webservices
sebastian/comparator                   1.1.1              Provides the functionality to compare PHP values for equality
sebastian/diff                         1.3.0              Diff implementation
sebastian/environment                  1.2.2              Provides functionality to handle HHVM/PHP environments
sebastian/exporter                     1.2.0              Provides the functionality to export PHP variables for visualization
sebastian/global-state                 1.0.0              Snapshotting of global state
sebastian/recursion-context            1.0.0              Provides functionality to recursively process PHP variables
sebastian/version                      1.0.5              Library that helps with managing the version number of Git-hosted PHP projects
sensio/distribution-bundle             v3.0.21            Base bundle for Symfony Distributions
sensio/framework-extra-bundle          v3.0.7             This bundle provides a way to configure your controllers with annotations
sensio/generator-bundle                v2.5.3             This bundle generates code for you
sensiolabs/security-checker            v2.0.2             A security checker for your composer.lock
swiftmailer/swiftmailer                v5.4.0             Swiftmailer, free feature-rich PHP mailer
symfony-cmf/routing                    1.3.0              Extends the Symfony2 routing component for dynamic routes and chaining several routers
symfony/assetic-bundle                 v2.6.1             Integrates Assetic into Symfony2
symfony/monolog-bundle                 v2.7.1             Symfony MonologBundle
symfony/swiftmailer-bundle             v2.3.8             Symfony SwiftmailerBundle
symfony/symfony                        v2.6.6             The Symfony PHP framework
tedivm/stash                           v0.12.3            The place to keep your cache.
tedivm/stash-bundle                    v0.4.2             Incorporates the Stash caching library into Symfony.
twig/extensions                        v1.2.0             Common additional features for Twig that do not directly belong in core
twig/twig                              v1.18.1            Twig, the flexible, fast, and secure template language for PHP
white-october/pagerfanta-bundle        v1.0.2             Bundle to use Pagerfanta with Symfony2
whiteoctober/breadcrumbs-bundle        1.0.2              A small breadcrumbs bundle for Symfony2
zendframework/zend-code                2.2.10             provides facilities to generate arbitrary code using an object oriented interface
zendframework/zend-eventmanager        2.2.10             
zendframework/zend-stdlib              2.2.10             
zetacomponents/base                    1.9                The Base package provides the basic infrastructure that all packages rely on. Therefore every component relies on this package.
zetacomponents/feed                    1.4                This component handles parsing and creating RSS1, RSS2 and ATOM feeds, with support for different feed modules (dc, content, creativeCommons, geo, iTunes).
zetacomponents/mail                    1.8.1              The component allows you construct and/or parse Mail messages conforming to  the mail standard. It has support for attachments, multipart messages and HTML  mail. It also interfaces with SMTP to send mail or IMAP, P...
zetacomponents/system-information      1.1                Provides access to common system variables, such as CPU type and speed, and the available amount of memory.
```

## 试运行

为了查看新需求的安装是否顺利，您可以在 Composer 的`install`和`update`命令中使用`--dry-run`标志。这将把所有潜在的问题抛给你，但实际上并没有引起这些问题——实际上不会有任何改变。非常适合在实际提交之前测试大的需求和设置变更。

```
composer update --dry-run --profile --verbose
```

## 创建项目

最后但同样重要的是，我们必须提到 [`create-project`命令](https://getcomposer.org/doc/03-cli.md#create-project)，适用于任何事情和一切。

Create project 将一个包名作为参数，然后克隆这个包并在其中执行`composer install`。这对于引导项目来说非常棒——不再需要找出您想要的包的确切的 Github URL，然后克隆，然后手动进入文件夹并执行`install`。

Symfony 和 Laravel 等主要项目使用这种方法来引导一个框架应用程序，许多其他项目也在使用这种方法。

以 Laravel 为例，它是这样使用的:

```
composer create-project laravel/laravel --prefer-dist --profile --verbose
```

`create-project`命令也接受两个参数。第一个是安装的路径。如果省略，则使用项目名称。其次是版本。如果省略，则使用最新版本。

## 结论

希望这个提示和技巧列表对你有所帮助！如果我们错过了一些，请告诉我们，我们将更新帖子！请记住——如果您忘记了一些命令或开关，只需查看[备忘单](http://composer.json.jolicode.com/)。快乐作曲！

## 分享这篇文章