# 快速提示:用 Composer 设置 Drupal 8！

> 原文：<https://www.sitepoint.com/quick-tip-set-up-drupal-8-with-composer/>

![Drupal 8 logo](img/a935c48e877a70a6250c9604c8474c63.png)

现在，推荐通过 Composer 开始使用 Drupal 8。已经为此创建了一个官方项目模板。我们将使用模板直接创建我们的项目，该模板也可以在 [Packagist](https://packagist.org/packages/drupal-composer/drupal-project) 上获得。

要基于此模板创建新项目，我们可以运行以下 Composer 命令:

```
composer create-project drupal-composer/drupal-project:8.x-dev my_project --stability dev --no-interaction 
```

这个 Composer 命令将从 Packagist 获取模板，并运行几个特定于 Drupal 的[脚本](https://github.com/drupal-composer/drupal-project/tree/8.x/scripts/composer)来准备我们的项目进行安装。剩下要做的唯一一件事就是将我们的浏览器指向`web/`目录(因为那是`index.php`文件所在的位置)并照常运行安装程序。

这个模板附带了一个`/web`文件夹，其中包含 Drupal 安装的主文件夹，这些文件夹不再被认为是 Drupal 核心的一部分(比如`index.php`文件或者模块和主题文件夹)。此外，它还附带了一个 Drupal 使用的`autoload.php`文件，该文件简单地指向 Composer `vendor/`目录，向上一个文件夹。所以现在所有的 PHP 库都在一个地方处理。

模板的`composer.json`文件需要最新稳定的 Drupal 核心+一些额外的辅助工具，比如 Drush 和 [Drupal 控制台](https://drupalconsole.com/)。此外，它添加了 [Drupal 特定的 Packagist](https://packagist.drupal-composer.org/) 库，我们可以从这里安装 Drupal 贡献的模块、主题和概要文件(它们会自动安装在正确的位置)。

如果我们想添加一个 Drupal 贡献的模块，我们需要在 Drupal Packagist 上找到它，并通过 Composer 在我们的项目中需要它:

```
composer require drupal/ctools 
```

这将把 Ctools 模块直接添加到我们的`web/modules/`目录中，并更新我们的`composer.json`文件。

项目模板还附带了一个`.gitignore`文件，该文件将 Drupal 核心和所有贡献的包保存在 Git 之外，类似于常规的`vendor/`包。因此，基于更新的`composer.json`文件，我们可以维护一个更小的 Git 库，并随时重新创建我们的项目。 [Drush Make](https://www.drupal.org/node/2471553) 的很多好处现在已经被合并到一个作曲流程中。

## 结论

Drupal 8 在追赶其他主要 PHP 软件方面已经走了很长的路。通过 Composer 完全管理它的可能性，无论是作为一个主项目，还是仅仅作为一个更大的应用程序集的一部分，都是对社区努力的证明。

## 分享这篇文章