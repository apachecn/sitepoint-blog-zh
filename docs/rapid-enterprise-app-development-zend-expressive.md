# 使用 Zend Expressive 快速开发企业应用程序

> 原文：<https://www.sitepoint.com/rapid-enterprise-app-development-zend-expressive/>

如果你曾经做过 Zend Framework 快速入门，你可能从来没有使用过 Zend Framework。快速启动从来都不是快速的，很容易失去兴趣并转向下一件事。

Zend 富于表现力的用向导驱动的`composer create-project`命令极大地改善了这种体验。然而，设置起来仍然令人望而生畏，因为前面有太多的选择要做。本教程将指导您完成我推荐的快速开发设置，这将
产生一个企业级的、健壮的应用程序。

![Zend Framework logo](img/a94bfb7aa0b8da59bee011e1d0d1e0d1.png)

本教程不是关于设置您的环境，所以我将假设您有一个良好的工作环境，如 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 。

*如果你不熟悉[流浪者](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)或孤立的虚拟环境，我们有一本神奇的书来指导你了解我们店里的概念[这里](http://bit.ly/phpenv-sp)。*

## 项目设置

通过在保存项目的文件夹中运行以下命令来启动您的项目(`Code` on [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) ):

```
composer create-project zendframework/zend-expressive-skeleton expressive 
```

在这个过程中，你会被提示做出一些决定。使用这些答案:

*   您想要什么类型的安装？
    *   模块化的
*   您想使用哪个容器进行依赖注入？
    *   Zend ServiceManager
*   你想用哪个路由器？
    *   Zend 路由器
*   您想使用哪个模板引擎？
    *   嫩枝
*   您希望在开发过程中使用哪个错误处理程序？
    *   哎哟
*   请选择要将“Zend\Validator\ConfigProvider”注入哪个配置文件？
    *   config/config.php
*   其他同类型的包还记得这个选项吗？
    *   y

然后，运行以下命令:

```
cd expressive &&
git init &&
git config color.ui true &&
git add . &&
git commit -m "Initial commit" &&
chmod -R +w data; 
```

这将在新创建的文件夹中初始化一个存储库，并使`data`文件夹可写。

然后，启动一个 php 服务器，用

```
composer serve 
```

…浏览到 http://localhost:8080，或者如果您使用的是 [Homestead 改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)，只需访问虚拟机的 IP 或虚拟主机。

![Zend Expressive Homepage](img/e5fc4940934b21a357f203c3f2e86d3d.png)

## 理解表达

Expressive 的文件夹结构如下所示:

```
bin/
config/
data/
  cache/
public/
  index.php
src/
  App
test/
  AppTest
vendor/ 
```

大部分是不言自明的。Expressive 默认提供了一个`App`模块。您可以将所有代码放在这里，或者在构建更大的功能时构建单独的模块。

Expressive 附带了一些方便的命令:

*   `./vendor/bin/expressive`–创建、注册和取消注册模块。创建中间件类等。
*   `composer serve`–运行 php-fpm 服务器的别名
*   对您的代码执行编码标准检查。
*   对您的代码执行编码标准检查，并尽可能修复问题。
*   对你的代码运行 PHPUnit 测试。
*   `composer check`–运行`cs-check`的别名，然后测试。

Expressive 还附带了呜呜错误处理程序。要测试它，打开`src/App/src/Action/HomePageAction.php`并在`process()`方法中键入`echo $badVar`，然后刷新页面。您将看到呜呜错误处理程序。

![Whoops Error Handler](img/c396c46bcda6e8be91dce3c7b14fb94a.png)

## 必要的改进

### 基于反射的抽象工厂

Zend Expressive 使用 Zend ServiceManager 进行依赖注入。在默认设置中，您需要添加配置，并可能为您编写的每个类创建一个工厂类。这样做了大约两次后，感觉负担很重。

为了避免这种情况，我们将启用 Zend Expressive 提供的基于反射的抽象工厂。

将其添加到`dependencies`数组中的`config/autoload/dependencies.global.php`:

```
'abstract_factories' => [
    \Zend\ServiceManager\AbstractFactory\ReflectionBasedAbstractFactory::class
], 
```

现在，每当你在一个类中工作并且需要一个依赖项时，只要把它添加到你的构造函数中就可以了。反射抽象工厂会看到你的类需要什么，并自动从服务容器中获取。您现在只需要在特殊情况下创建工厂，在这种情况下，您需要不同于服务容器所提供的默认服务。

如果你关心速度。在生产中，我们可以有一个为您的类生成工厂的过程，这些类是由反射工厂用`vendor/bin/generate-factory-for-class`处理的。

### 主义

Zend Expressive 没有提供数据库工具或 ORM。经过大量研究并建立了自己的一些形式后，我选择了教义作为我的选择形式。它只是工作。

通过 Composer 安装 Doctrine 和 Symfony Yaml:

```
composer require dasprid/container-interop-doctrine symfony/yaml 
```

创建一个包含以下内容的文件`config/cli-config.php`:

```
<?php

use Doctrine\ORM\Tools\Console\ConsoleRunner;

chdir(dirname(__DIR__));
require 'vendor/autoload.php';

/**
 * Self-called anonymous function that creates its own scope and keep the global namespace clean.
 */
return call_user_func(function () {
    /** @var \Interop\Container\ContainerInterface \$container */
    $container = require 'config/container.php';

    $entityManager = $container->get(\Doctrine\ORM\EntityManager::class);
    return ConsoleRunner::createHelperSet($entityManager);
}); 
```

用以下内容替换`config/autoload/dependencies.global.php`的内容:

```
<?php

use Zend\Expressive\Application;
use Zend\Expressive\Container;
use Zend\Expressive\Delegate;
use Zend\Expressive\Helper;
use Zend\Expressive\Middleware;

return [
    // Provides application-wide services.
    // We recommend using fully-qualified class names whenever possible as
    // service names.
    'dependencies' => [
        'abstract_factories' => [
            \Zend\ServiceManager\AbstractFactory\ReflectionBasedAbstractFactory::class
        ],
        // Use 'aliases' to alias a service name to another service. The
        // key is the alias name, the value is the service to which it points.
        'aliases' => [
            'Zend\Expressive\Delegate\DefaultDelegate' => Delegate\NotFoundDelegate::class,
        ],
        // Use 'invokables' for constructor-less services, or services that do
        // not require arguments to the constructor. Map a service name to the
        // class name.
        'invokables' => [
            // Fully\Qualified\InterfaceName::class => Fully\Qualified\ClassName::class,
            \Doctrine\DBAL\Logging\DebugStack::class => \Doctrine\DBAL\Logging\DebugStack::class,
            Helper\ServerUrlHelper::class => Helper\ServerUrlHelper::class,
            Middleware\ImplicitHeadMiddleware::class => Middleware\ImplicitHeadMiddleware::class,
            Middleware\ImplicitOptionsMiddleware::class => Middleware\ImplicitOptionsMiddleware::class,
        ],
        // Use 'factories' for services provided by callbacks/factory classes.
        'factories'  => [
            Application::class                => Container\ApplicationFactory::class,
            Delegate\NotFoundDelegate::class  => Container\NotFoundDelegateFactory::class,
            \Doctrine\ORM\EntityManager::class  => \ContainerInteropDoctrine\EntityManagerFactory::class,
            Helper\ServerUrlMiddleware::class => Helper\ServerUrlMiddlewareFactory::class,
            Helper\UrlHelper::class           => Helper\UrlHelperFactory::class,
            Helper\UrlHelperMiddleware::class => Helper\UrlHelperMiddlewareFactory::class,
            Zend\Stratigility\Middleware\ErrorHandler::class => Container\ErrorHandlerFactory::class,
            Middleware\ErrorResponseGenerator::class         => Container\ErrorResponseGeneratorFactory::class,
            Middleware\NotFoundHandler::class                => Container\NotFoundHandlerFactory::class,
        ],
    ],
]; 
```

创建此文件以设置原则驱动程序`config/autoload/doctrine.global.php`。

```
<?php

return [
    'doctrine' => [
        'driver' => [
            'orm_default' => [
                'class' => \Doctrine\Common\Persistence\Mapping\Driver\MappingDriverChain::class,
                'drivers' => [],
            ],
        ],
    ],
]; 
```

为您的数据库凭证`config/autoload/doctrine.local.php`创建这个文件。

```
<?php
return [
    'doctrine' => [
        'connection' => [
            'orm_default' => [
                'params' => [
                    'url' => 'mysql://root:password1@localhost/expressive',
                ],
            ],
        ],
    ],
]; 
```

通过运行`./vendor/bin/doctrine`进行测试。您应该会看到帮助提示。

### 吞咽

Gulp 是我目前首选的前端工作流工具。有很多很多前端构建工具可用。如果您喜欢，可以看看，但是您可能会迷失在闪亮的新 JavaScript 库的海洋中。我不想在这里涉及太多，因为这更像是一个 PHP 教程，而不是 JS，但我确实想展示如何配置 gulp 来与 Zend Expressive 一起工作。

用以下内容创建一个`package.json`文件:

```
{
  "name": "expressive",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "devDependencies": {
    "del": "^3.0.0",
    "gulp": "github:gulpjs/gulp#4.0",
    "gulp-cached": "^1.1.1",
    "gulp-imagemin": "^3.3.0",
    "gulp-minify-css": "^1.2.4",
    "gulp-rename": "^1.2.2",
    "gulp-sass": "^3.1.0",
    "gulp-uglify": "^2.1.2",
    "gulp-usemin": "^0.3.28"
  },
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
} 
```

运行`npm install`。如果你在写完这篇教程后不久还在阅读它，你可能也想运行`npm update`。

然后，用这些内容创建一个`gulpfile.js`:

```
'use strict';

var cache = require('gulp-cached');
var del = require('del');
var gulp = require('gulp');
var imagemin = require('gulp-imagemin');
var minifyCss = require('gulp-minify-css');
var path = require('path');
var rename = require('gulp-rename');
var sass = require('gulp-sass');
var uglify = require('gulp-uglify');

// CSS Processing
gulp.task('clean-css', function() {
    return del('public/css', { force: true });
});

gulp.task('compile-sass', function() {
    return gulp.src('src/*/public/sass/**/*.scss', { base: './' })
        .pipe(cache('compile-sass'))
        .pipe(sass().on('error', sass.logError))
        .pipe(rename(function (path) {
            path.dirname = path.dirname.replace(/^src\/([^\/]+\/)public\/sass/, '$1');
        }))
        .pipe(gulp.dest('public/css/'));
});

gulp.task('copy-css', function() {
    return gulp.src('src/*/public/css/**/*.css', { base: './' })
        .pipe(cache('copy-css'))
        .pipe(rename(function (path) {
            path.dirname = path.dirname.replace(/^src\/([^\/]+\/)public\/css/, '$1');
        }))
        .pipe(gulp.dest('public/css/'));
});

gulp.task('minify-css', function() {
    return gulp.src(['public/css/**/*.css', '!public/css/**/*.min.css'], { base: './' })
        .pipe(cache('minify-css'))
        .pipe(minifyCss())
        .pipe(rename(function (path) {
            path.dirname = path.dirname.replace(/^public\/css/, '');
        }))
        .pipe(rename({ extname: '.min.css' }))
        .pipe(gulp.dest('public/css'))
    ;
});
gulp.task('process-css', gulp.series(['compile-sass', 'copy-css'], 'minify-css'));

// JS Processing
gulp.task('clean-js', function() {
    return del('public/js', { force: true });
});

gulp.task('copy-js', function() {
    return gulp.src('src/*/public/js/**/*.js', { base: './' })
        .pipe(cache('copy-js'))
        .pipe(rename(function (path) {
            path.dirname = path.dirname.replace(/^src\/([^\/]+\/)public\/js/, '$1');
        }))
        .pipe(gulp.dest('public/js/'));
});

gulp.task('minify-js', function() {
    return gulp.src(['public/js/**/*.js', '!public/js/**/*.min.js'], { base: './' })
        .pipe(cache('minify-js'))
        .pipe(uglify())
        .pipe(rename(function (path) {
            path.dirname = path.dirname.replace(/^public\/js/, '');
        }))
        .pipe(rename({ extname: '.min.js' }))
        .pipe(gulp.dest('public/js'))
    ;
});
gulp.task('process-js', gulp.series('copy-js', 'minify-js'));

// Image processing
gulp.task('clean-img', function() {
    return del('public/img', { force: true });
});

gulp.task('process-img', function() {
    return gulp.src('src/*/public/img/**/*.{gif,jpg,jpeg,png,svg}', { base: './' })
        .pipe(cache('process-img'))
        .pipe(imagemin())
        .pipe(rename(function (path) {
            path.dirname = path.dirname.replace(/^src\/([^\/]+\/)public\/img/, '$1');
        }))
        .pipe(gulp.dest('public/img'));
});

// Top level commands
gulp.task('default', gulp.parallel('process-js', 'process-css', 'process-img'));
gulp.task('clean', gulp.parallel('clean-js', 'clean-css', 'clean-img'));

gulp.task('watch', function() {
    gulp.watch(['src/*/public/sass/**/*.scss','src/*/public/css/**/*.css'], gulp.series('process-css'));
    gulp.watch('src/*/public/js/**/*.js', gulp.series('process-js'));
    gulp.watch('src/*/public/img/**/*.{gif,jpg,jpeg,png,svg}', gulp.series('process-img'));
}); 
```

运行`gulp`并确保其运行无误。

现在您可以运行`gulp`来编译 sass、minify css、minify js，并优化所有模块中的图像。您可以使用`gulp watch`来跟进，让它们在更改时自动得到处理。缓存吞咽模块确保只处理已更改的文件，因此应该可以非常快速地处理更改。

通过创建以下文件之一对此进行测试:

*   `src/App/public/sass/sasstest.scss`
*   `src/App/public/css/test.css`
*   `src/App/public/js/test.js`
*   `src/App/public/img/test.jpg`

然后运行`gulp`。在`public/css/App`、`public/js/App`或`public/img/App`中查找文件。

运行`gulp watch`，更改源文件，然后观察公开的文件进行更新。

## 控制台命令

最后，但绝对不是最不重要的，您将需要一种运行控制台命令的方法。为此，我们将使用 Symfony 的控制台,它已经与 Zend Expressive 一起发布，因此我们不需要手动要求它。

创建一个名为`bin/console`的文件:

```
#!/usr/bin/env php
<?php

chdir(dirname(__DIR__));
require 'vendor/autoload.php';

/**
 * Self-called anonymous function that creates its own scope and keep the global namespace clean.
 */
call_user_func(function () {
    /** @var \Interop\Container\ContainerInterface $container */
    $container = require 'config/container.php';

    $app = new \Symfony\Component\Console\Application('Application console');

    $commands = $container->get('config')['console']['commands'];
    foreach ($commands as $command) {
        $app->add($container->get($command));
    }

    $app->run();
}); 
```

然后，您可以创建 Symfony 命令，并通过`config/autoload/console.global.php`或从您的模块中注册它们，如下所示:

```
<?php

return [
    'console' => [
        'commands' => [
            \App\Command\HelloWorldCommand::class,
        ],
    ],
]; 
```

像 Expressive 中的任何其他类一样，将控制台命令需要的任何依赖项添加到构造函数中。确保在你的构造函数中调用`parent::__construct()`，否则你的命令将不起作用。

下面是一个具有依赖性的命令示例:

```
<?php

namespace App\Command;

use Doctrine\ORM\EntityManager;
use Monolog\Logger;
use Symfony\Component\Console\Command\Command;
use Symfony\Component\Console\Input\InputArgument;
use Symfony\Component\Console\Input\InputInterface;
use Symfony\Component\Console\Input\InputOption;
use Symfony\Component\Console\Output\OutputInterface;

class HellowWorld extends Command
{
    /**
     * @var EntityManager
     */
    private $entityManager;

    public function __construct(EntityManager $entityManager, $name = null)
    {
        $this->entityManager = $entityManager;
        parent::__construct($name);
    }

    /**
     * Configures the command
     */
    protected function configure()
    {
        $this->setName('hello')
            ->setDescription('Says hello')
        ;
    }

    /**
     * Executes the current command
     */
    protected function execute(InputInterface $input, OutputInterface $output)
    {
        $output->writeln("Hello World!");

        // Do something with the entityManager
        $this->entityManager->find('Blog\Entity\BlogEntity');
    }
} 
```

要运行您的命令:

```
php bin/console hello 
```

我们可以更进一步，添加一个记录器。这对于传递给封装了大量逻辑并需要全程调试日志的服务模型非常有用。

运行以下命令:

```
composer require monolog/monolog symfony/monolog-bridge; 
```

然后，将它添加到命令的 execute 方法中:

```
protected function execute(InputInterface $input, OutputInterface $output)
{
    $logger = new \Monolog\Logger('collect-product-data');
    $logger->pushHandler(new \Symfony\Bridge\Monolog\Handler\ConsoleHandler($output));
    $logger->debug('Log something);
} 
```

## 结论

现在，您已经准备好开始构建一个企业级应用程序，所有您想要的工具都唾手可得。

在下一篇文章中，我们将开始学习如何在此基础上构建模块，从博客模块开始。

## 分享这篇文章