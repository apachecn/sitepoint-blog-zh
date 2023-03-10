# 使用 Symfony Flex 构建图片库博客:设置

> 原文：<https://www.sitepoint.com/building-image-gallery-blog-symfony-flex-setup/>

*本文是一个零英雄项目的一部分——一个多图片画廊博客——用于性能基准测试和优化。(点击此处查看[回购](https://github.com/sitepoint-editors/multi-image-gallery-blog/)。)在这一部分，我们将设置我们的项目，以便我们可以在接下来的几篇文章中对其进行微调，并使其迅速完善。*

* * *

**有时，您必须创建一个新项目存储库，在本地运行`git init`命令，并开始一个新的令人惊叹的项目。我不得不承认，我喜欢开始新生活的感觉；就像去探险一样！**

老子说:

> 千里之行始于足下

我们可以将项目设置视为我们千里(用户！)旅程。我们不确定我们会在哪里结束，但是会很有趣的！

我们还应该牢记唐纳德·克努特教授的建议:

> 过早的优化是编程中所有罪恶(或者至少是大部分罪恶)的根源。

我们迈向稳定、健壮、高性能 web 应用的旅程将从简单但功能强大的应用程序开始——即所谓的最小可行产品(MVP)。我们将用随机内容填充数据库，进行一些基准测试，并逐步提高性能。本系列的每一篇文章都将是我们旅途中的一个关卡！

本文将介绍为我们的 Symfony Flex 项目设置项目和组织文件的基础知识。我还将向您展示一些我用来加速开发的技巧、诀窍和助手脚本。

## 我们在建造什么？

在开始任何项目之前，你应该对最终目标有一个清晰的愿景。你要去哪里？谁将使用你的应用程序，如何使用？你正在构建的主要功能是什么？一旦掌握了这些知识，您就可以准备您的环境、第三方库，并开始开发下一个大东西。

在这一系列文章中，我们将构建一个简单的图片库博客，用户可以注册或登录，上传图像，并创建简单的公共图片库，其中的描述以 Markdown 格式编写。

我们将使用新的 [Symfony Flex](https://www.sitepoint.com/symfony-flex-paving-path-faster-better-symfony/) 和 [Homestead](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) (确保你已经阅读了关于它们的教程，因为我们不打算在这里讨论它们)。我们选择 Flex 是因为 Symfony 4 即将问世(如果在您阅读本文时它还没有问世)，因为它比旧版本轻得多，非常适合逐步优化，而且它也是最流行的企业 PHP 框架发展的自然步骤。

本文引用的所有代码都可以在 [GitHub repo](https://github.com/sitepoint-editors/multi-image-gallery-blog) 获得。

我们将使用 Twig 模板引擎、Symfony 表单和带有 UUIDs 的教条 ORM 作为主键。

实体和路线将使用注释；我们将有简单的[基于电子邮件/密码的认证](http://symfony.com/doc/current/security/form_login_setup.html)，我们将准备数据设备来填充数据库。

### 应用程序入门

要测试我们准备的示例，请执行以下操作:

*   建立一个名为“博客”的空数据库。
*   从 GitHub 克隆项目[库](https://github.com/sitepoint-editors/multi-image-gallery-blog)。
*   运行`composer install`。
*   如果您现在在浏览器中打开应用程序，您应该会看到一个关于缺少数据库表的异常。这很好，因为到目前为止我们还没有创建任何表。
*   用有效的数据库连接字符串更新项目根目录中的`.env`文件(即更新凭证)。
*   运行数据库初始化脚本`./bin/refreshDb.sh`，等待它生成一些漂亮的图库。
*   在浏览器中打开应用程序，尽情享受吧！

执行`bin/refreshDb.sh`后，您应该能够看到我们网站的主页:

![Project homepage](img/7eabf15e1930a4527a8c959898cfc2c9.png)

可以用凭证`user1@mailinator.com`和密码`123456`登录 app。参见 [LoadUserData](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/src/DataFixtures/ORM/LoadUsersData.php) fixture 类，了解更多关于生成用户的详细信息。

### 从零开始

在这一节中，我们将描述如何从头开始建立一个新项目。请随意查看[示例应用代码库](https://github.com/sitepoint-editors/multi-image-gallery-blog)并查看详细信息。

通过执行命令基于`symfony/skeleton`创建一个新项目后

```
composer create-project "symfony/skeleton:^3.3" multi-user-gallery-blog 
```

…我们可以先将最低稳定性设置为“dev ”,因为有一些先进的软件包:

```
composer config minimum-stability dev 
```

…然后需要额外的包(其中一些通过别名引用，这是 Flex 带来的新功能):

```
composer req annotations security orm template asset validator ramsey/uuid-doctrine 
```

仅在开发环境中使用的依赖项需要带有`--dev`标志:

```
 composer req --dev fzaninotto/faker doctrine/Doctrine-Fixtures-Bundle 
```

Flex 正在幕后为我们做一些严肃的工作，大多数库(或包)已经注册并配置了足够好的默认值！检查`config`目录。您可以在 [composer.json](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/composer.json) 文件中检查这个项目中使用的所有依赖项。

路线由注释定义，因此以下内容将自动添加到`config/routes.yaml`中:

```
controllers:
  resource: ../src/Controller/
  type: annotation 
```

## 数据库、脚本和设备

配置`DATABASE_URL`环境变量(例如，通过编辑`.env`文件)来设置一个工作数据库连接。如果你用的是我们自己的[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)(推荐)，你已经用用户/通行证`homestead` / `secret`建立了一个名为`homestead`的数据库。可以通过执行以下命令从现有实体生成 DB 模式:

```
./bin/console doctrine:schema:create 
```

如果没有运行，尝试通过调用 PHP 二进制文件来执行控制台，如下所示:

```
php bin/console doctrine:schema:create 
```

*如果在上面的“应用程序入门”一节中该步骤执行得很好，您应该能够在数据库中看到新创建的表格(针对[画廊](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/src/Entity/Gallery.php)、[图像](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/src/Entity/Image.php)和[用户](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/src/Entity/User.php)实体)。*

如果要删除数据库模式，可以运行:

```
./bin/console doctrine:schema:drop --full-database --force 
```

### 假装直到你成功！

我无法想象今天开发一个没有数据设备的应用程序(例如，播种数据库的脚本)。通过几个简单的脚本，您可以用真实的内容填充您的数据库，这在快速应用程序开发和测试时非常有用，但也是健康 CI 渠道的一个要求。

我发现[Doctrine fixture Bundle](https://symfony.com/doc/master/bundles/DoctrineFixturesBundle/index.html)是处理数据 fixture 的优秀工具，因为它支持有序 fixture(即，您可以控制执行的顺序)、在脚本之间共享对象(通过引用)以及访问服务容器。

默认的 Symfony 服务配置不允许公共访问服务，因为最佳做法是注入所有的依赖关系。我们的 fixtures 中需要一些服务，所以我将通过在`config/services.yaml`中添加以下内容来公开`App\Services`中的所有服务:

```
App\Service\:
  resource: '../src/Service/*'
  public: true 
```

我还使用 [Faker](https://github.com/fzaninotto/Faker) 来获取随机但真实的数据(姓名、句子、文本、图像、地址……)。

看一看用随机图像播种图库的[脚本，感受一下这种组合有多酷。](https://github.com/sitepoint-editors/multi-image-gallery-blog/blob/master/src/DataFixtures/ORM/LoadGalleriesData.php)

通常，我将删除现有数据库模式、创建新数据库模式、加载数据设备和其他重复性任务的命令组合到一个 shell 脚本中，例如，`bin/refreshDb.sh`，这样我就可以轻松地重新生成模式并加载虚拟数据:

```
# Drop schema
./bin/console doctrine:schema:drop --full-database --force

# Create schema
./bin/console doctrine:schema:create

# Load fixtures
./bin/console doctrine:fixtures:load -n --fixtures src/DataFixtures/ORM

# Install assets
./bin/console assets:install --symlink

# Clear cache
./bin/console cache:clear 
```

请确保在生产中限制该脚本的执行，否则您将会遇到一些严重的问题。

有人可能会说，随机生成的数据无法再现不同的边缘情况，因此您的 CI 有时会失败或通过，这取决于数据生成。这是真的，你应该确保所有的边缘案例都覆盖了你的夹具。

每当您发现导致 bug 的边缘情况时，请确保将其添加到数据夹具中。这将帮助你建立一个更强大的系统，并防止将来出现类似的错误。

## 控制器、模板和服务

控制器类位于`src\Controller`目录下，没有扩展由`FrameworkBundle`提供的现有的`Controller`类。所有的依赖关系都是通过构造函数注入的，这使得代码耦合度更低，更容易测试。我不喜欢简单东西的“魔术”！

在新的 Symfony 目录结构中，模板位于`templates`下。
我已经创建了一个主模板`base.html.twig`，它定义了基本的 HTML 结构并引用了外部资源。

其他模板扩展它并覆盖它的块(例如，样式表、主体、标题、内容、JavaScripts 和其他块)。

通过使用`asset` Twig 函数引用本地资产，因为它将在以后为我们提供更好的控制(比如改变主机和为版本控制追加查询字符串)。其他模板组织在`src/templates`目录的子目录中。

默认情况下，服务会在 Symfony 的服务配置中自动注册和配置。这样，您就不需要手动配置带有过滤器的 Twig 扩展来支持降价，并为位于`src/Twig`下的图像实体生成 URL。

## 源代码管理/版本控制

希望你已经了解了 [Git 基础知识](https://www.sitepoint.com/git-for-beginners/)，并且理解了`.gitignore`是如何工作的。

Symfony Flex 通过将已知的捆绑文件和文件夹添加到忽略列表来管理默认项目`.gitignore`。例如，Flex 会将以下文件添加到`symfony/framework-bundle`的忽略列表中:

```
.env
/public/bundles/
/var/
/vendor/ 
```

我们将把上传的文件存储在`var/uploads`中，所以我们需要先创建它。我们可以在`composer.json`脚本部分的`post-install-cmd`事件中添加一个命令来创建所有需要的目录，并让 Composer 为我们运行(例如，`"mkdir -p var/uploads"`，而不是在每个项目设置中手动执行。点击这里阅读更多关于作曲脚本[和自动脚本](https://getcomposer.org/doc/articles/scripts.md)[的信息。](http://fabien.potencier.org/symfony4-workflow-automation.html)

我们应该将所有其他我们希望忽略的目录和文件添加到`.gitignore`中，但不在由 Flex 管理的注释和明确标记的部分中。

## 其他提示和故障排除

*   检查您的`Homestead.yaml`文件中的环境变量。如果你已经有了一个`APP_ENV`变量，Symfony 不会试图从`.env`文件中加载。为了安全起见，你最好注释掉`Homestead.yaml`中的`APP_ENV`部分。
*   由于权限问题，您的虚拟机中的缓存清除可能会失败。您可以简单地运行`sudo rm -rf var/cache/*`来手动清除缓存，或者如果失败，从 VM 外部的主机共享文件夹中清除缓存。

## 结论

在本文中，我们成功地建立了一个包含实体、控制器、模板和助手脚本的项目。数据库中填充了虚拟内容，用户可以注册并创建自己的图库。

在下一篇文章中，我们将演示如何用更大规模的数据集填充数据库，以在更真实的环境中测试应用程序的性能，如何设置简单的测试套件，以及基于 Docker 的简单 CI。

## 分享这篇文章