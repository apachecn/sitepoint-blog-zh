# 使用 Deployer 轻松部署 PHP 应用程序

> 原文：<https://www.sitepoint.com/deploying-php-applications-with-deployer/>

*本文已于 2017 年 3 月 26 日更新为 Deployer 的最新版本。*

* * *

每个人都试图自动化他们的开发过程、测试、代码格式化、系统检查等等。这也适用于部署我们的应用程序或将新版本推送到生产服务器的情况。我们中的一些人通过使用 FTP 客户端上传代码来手动完成这一过程，其他人更喜欢使用 [Phing](https://www.phing.info) ，而 Laravel 用户更喜欢使用 [Envoyer](http://envoyer.io/) 来完成这一过程。在本文中，我将向您介绍[部署器](http://deployer.org)——PHP 的部署工具。

![Deployer LOGO](img/34945a57128bbaf5b9c8355d62334d4b.png)

## 演示应用程序

我将使用上一篇文章中的一个应用程序进行演示。该应用程序将被部署到一个数字海洋液滴。接下来，您可以从 [GitHub](https://github.com/Whyounes/500pxAPI_Test) 中克隆演示应用程序的源代码。

## 装置

Deployer 被打包成一个 PHAR 文件，我们可以将它下载到本地机器上。我们还可以将它移动到用户的 bin 目录中，使它成为全局的。查看[文档](http://deployer.org/docs/installation)了解更多详情。

```
mv deployer.phar /usr/local/bin/dep
chmod +x /usr/local/bin/dep 
```

## 定义服务器

在克隆了演示存储库之后，我们需要创建一个名为`deploy.php`的新文件，在这里我们将定义我们的部署步骤。

第一步是定义我们的部署服务器。我们通常可以使用用户名和密码进行身份验证。

```
// deploy.php

server('digitalocean', '104.131.27.106')
    ->user($_ENV['staging_server_user'])
    ->password($_ENV['staging_server_password']); 
```

我们还可以定义这个服务器的类型(`staging`、`production`等)，这允许我们在特定的服务器阶段运行任务(稍后将详细介绍任务)。

```
// deploy.php
use function Deployer\set;
use function Deployer\server;

set('default_stage', 'staging');

server('digitalocean', '104.131.27.106')
    ->user($_ENV['staging_server_user'])
    ->password($_ENV['staging_server_password'])
    ->stage('staging')
    ->env('deploy_path', '/var/www'); 
```

我们需要在使用 stage 方法时添加`default_stage`属性。否则，我们将得到`You need to specify at least one server or stage.`错误。

*注意*:如果你使用的是 PHP 7，你可以像这样把 use 语句组合在一行(`use function Deployer\{set, server}`)。你可以在这里阅读更多关于 PHP 7 的新特性[。](https://www.sitepoint.com/learn-php-7-find-out-whats-new-and-more/)

### SSH 认证

在生产服务器上使用 SSH 认证是一种常见的做法。如果您不熟悉使用 SSH 密钥进行身份验证，请查看本[指南](https://www.digitalocean.com/community/tutorials/how-to-use-ssh-keys-with-digitalocean-droplets)中的详细介绍。

```
// deploy.php
use function Deployer\{set, server};

set('default_stage', 'staging');

server('digitalocean', '104.131.27.106')
    ->identityFile()
    ->user($_ENV['staging_server_user'])
    ->password($_ENV['staging_server_password'])
    ->stage('staging'); 
```

默认情况下，`identityFile`方法使用当前用户的`~/.ssh/id_rsa`身份文件。如果需要，我们可以更改。

```
// deploy.php

// ...
    ->identityFile('path/to/id_rsa', 'path/to/id_rsa.pub', 'pass phrase')
// ... 
```

Deployer 支持多种类型的 SSH 连接，默认情况下使用本机系统命令进行身份验证。

### 安装 SSH2 扩展

部署者还支持使用 [PHP SSH2](http://php.net/manual/en/intro.ssh2.php) 扩展。安装并启用扩展后，我们需要 [`herzult/php-ssh`](https://github.com/herzult/php-ssh) 包，它为 PHP 扩展提供了一个 OOP 包装器，并将`ssh_type`配置选项设置为`ext-ssh2`。

```
// deploy.php

set('ssh_type', 'ext-ssh2');
// ... 
```

这里唯一的问题是部署者没有 PHAR 归档附带的`php-ssh`包。我们需要克隆部署者 [`repository`](https://github.com/deployphp/deployer) ，需要这个包，最后使用`build`脚本来生成一个新的 PHAR 档案。

### 使用配置文件

我们还可以使用 YAML 配置文件定义我们的服务器，并将该文件传递给`serverList`方法。你可以在[文档](http://deployer.org/docs/servers)中读到更多关于服务器的信息。

```
// servers.yml

digitalocean:
  host: 104.131.27.106
  user: root
  identity_file: ~
  stage: staging
  deploy_path: /var/www/ 
```

我们现在可以将文件加载到我们的`deploy.php`文件中。

```
serverList('servers.yml'); 
```

## 定义任务

任务是可以通过`dep`命令运行的命令。

```
dep deploy:staging 
```

`deploy:staging`参数应该是我们的`deploy.php`文件中的一个任务。我们可以上传文件，在服务器上运行命令等等。

```
// deploy.php

use function Deployer\{server, task, run, set, get, add, before, after, upload};

task('deploy:staging', function() {
    writeln('<info>Deploying...</info>');
    $appFiles = [
        'app',
        'bootstrap',
        'public',
        'composer.json',
        'composer.lock',
        'artisan',
        '.env',
    ];
    $deployPath = get('deploy_path');

    foreach ($appFiles as $file)
    {
        upload($file, "{$deployPath}/{$file}");
    }

    cd($deployPath);
    run("composer update --no-dev --prefer-dist --optimize-autoloader");

    run("chown -R www-data:www-data app/storage");
    set('writable_dirs', ['app/storage']);

    writeln('<info>Deployment is done.</info>');
}); 
```

这个文件一开始看起来很大，但是这里没有什么特别的。首先，我们使用`writeln`方法打印一条消息，通知用户部署过程已经开始。接下来，我们定义需要上传的应用程序文件夹。

`upload`方法将把我们的本地应用程序文件发送到`for`循环中的服务器。查看[文档](http://deployer.org/docs/functions)中的可用功能列表。

我们`cd`到服务器目录，并使用`run`方法更新我们的 Composer 依赖项，这允许我们在服务器上运行任何 shell 命令。

下一步也是最后一步是让服务器用户可以写必要的目录，在这里是 Apache 的`www-data`。`writable_dirs`参数告诉部署者我们的可写目录。我们也可以使用 shell 命令来实现同样的事情。

```
dep deploy:staging 
```

![Deploy 1](img/8ab7d9eaad2675a93f20d8671623bd83.png)

```
task("deploy:staging", function () {
    // ... 
})->desc('Deploy application to staging.'); 
```

`desc`方法将向我们的任务添加一条帮助消息(*命令*)。

我们当前的文件有一个大任务来处理整个部署过程。我们将把它分成小的可重用任务，并在`deploy:staging`任务中一个接一个地运行它们。

```
task('deploy:started', function() {
    writeln('<info>Deploying...</info>');
});

task('deploy:done', function() {
    writeln('<info>Deployment is done.</info>');
}); 
```

以上任务只是通知用户部署状态。Deployer 提供了`after`和`before`方法(*钩子*)在其他任务被触发时运行任务。

```
before('deploy:staging', 'deploy:started');
after('deploy:staging', 'deploy:done'); 
```

```
task('deploy:upload', function() {
    $appFiles = [
        'app',
        'bootstrap',
        'public',
        'composer.json',
        'composer.lock',
        'artisan',
        '.env',
    ];
    $deployPath = get('deploy_path');

    foreach ($appFiles as $file)
    {
        upload($file, "{$deployPath}/{$file}");
    }
});

task('deploy:writable_dirs', function() {
    $deployPath = get('deploy_path');
    cd($deployPath);

    run("chown -R www-data:www-data app/storage");
    set('writable_dirs', ['app/storage']);
});

task('deploy:composer', function() {
    $deployPath = get('deploy_path');
    cd($deployPath);

    run("composer update --no-dev --prefer-dist --optimize-autoloader");
}); 
```

剩下的就是运行`deploy:staging`任务中的所有任务。

```
task('deploy:staging', [
    'deploy:upload', 
    'deploy:writable_dirs',
    'deploy:composer',
]); 
```

因为我们将任务分成了有意义的部分，所以我们可以在以后将它们重新用于另一个生产服务器，甚至用于其他项目！

## 零停机部署

目前，我们的 Apache 服务器根目录指向`/var/www/public`目录。当我们部署新版本的应用程序时，我们需要将服务器置于维护模式几分钟，以避免用户停机。

这个问题的一个简单解决方案是创建一个发布列表，并将我们服务器的根目录指向一个当前的目录，这个目录将链接到最新的发布。

```
/current (link pointing to the current release)
/releases
    /realease_1
    /realease_2
    /realease_3 
```

## 常见部署任务

Deployer 有一个大多数 PHP 应用程序使用的常见任务列表，就像我们之前做的一样。我们将重新考虑我们的`deploy.php`,尽可能重复使用那些常见任务。

```
// deploy.php

require_once "recipe/common.php";

set('ssh_type', 'ext-ssh2');
set('default_stage', 'staging');
set('deploy_path', '/var/www');
set('copy_dirs', [
    'app/commands',
    'app/config',
    'app/controllers',
    'app/database',
    'app/lang',
    'app/models',
    'app/src',
    'app/start',
    'app/tests',
    'app/views',
    'app/filters.php',
    'app/routes.php',
    'bootstrap',
    'public',
    'composer.json',
    'composer.lock',
    'artisan',
    '.env',
]);
set('shared_dirs', [
    'app/storage/cache',
    'app/storage/logs',
    'app/storage/meta',
    'app/storage/sessions',
    'app/storage/views',
]);
set('writable_dirs', get('shared_dirs'));
set('http_user', 'www-data'); 
```

首先，我们设置一些将在任务中使用的变量。`shared_dirs`、`writable_dirs`、`http_user`都是普通任务使用的..

```
task('deploy:upload', function() {
    $files = get('copy_dirs');
    $releasePath = get('release_path');

    foreach ($files as $file)
    {
        upload($file, "{$releasePath}/{$file}");
    }
}); 
```

在这种情况下，我们保留了`deploy:upload`任务，但是您也可以使用`deploy:update_code`任务从远程 Git 主机获取您的应用程序。使用时不要忘记设置必要的属性。

```
set('repository', 'http://github.com/whyounes/500pxAPI_Test.git');
set('branch', 'master'); 
```

```
// deploy.php

task('deploy:staging', [
    'deploy:prepare',
    'deploy:release',
    'deploy:upload',
    'deploy:shared',
    'deploy:writable',
    'deploy:symlink',
    'deploy:vendors',
    'current',
])->desc('Deploy application to staging.');

after('deploy:staging', 'success'); 
```

*   `deploy:prepare`:测试连接、共享文件夹、发布文件夹等。
*   `deploy:release`:创建发布目录。
*   `deploy:upload`:我们上传文件的任务。
*   `deploy:shared`:如果共享文件夹不存在，则创建共享文件夹。使用我们之前设置的`shared_dirs`属性。
*   `deploy:writable`:设置可写目录。
*   `deploy:symlink`:创建一个从发布到`current`的符号链接。
*   `deploy:vendors`:运行 Composer 安装。
*   `current`:打印当前发布名称。
*   `success`:打印成功信息。

![Deploy 2](img/3e96b8b3f01d5e05c39f968b61a13913.png)

![Deploy dirs](img/7254258b101ec9ca4a06fa1f58475076.png)

## 部署方案

因为我们大多数人都在项目中使用框架，所以 Deployer 为 Laravel、Symfony、Yii、Zend 等知名框架提供了一些预配置的方法。查看文档中的[配方](http://deployer.org/docs/recipes)部分了解更多详情。

## 结论

Deployer 有一个非常好的 API，可以使部署过程变得简单和可配置。如果您从未使用过部署工具来自动化您的部署过程，我们真的鼓励您尝试一下！

如果您以前使用过 Deployer，我们很想听听您的想法！有问题可以在下面贴出来！

## 分享这篇文章