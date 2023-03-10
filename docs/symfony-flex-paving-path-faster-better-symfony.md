# Symfony Flex:为更快、更好的 Symfony 铺平道路

> 原文：<https://www.sitepoint.com/symfony-flex-paving-path-faster-better-symfony/>

Symfony Flex 是 Symfony 安装程序的现代替代品，而不是下一个 Symfony 版本的名称。正如介绍文字所说:

> 在内部，Symfony Flex 是一个 Composer 插件，可以修改 require 和 update 命令的行为。在启用 Flex 的应用程序中安装或更新依赖项时，Symfony 可以在执行 Composer 任务之前和之后执行任务。

新的 Symfony 将被称为 Symfony 4，虽然本教程将只涉及 Flex 工具，但也会提到一些 Symfony 4 的升级。

* * *

## 仍在开发中

Symfony Flex 可以被视为一个 [Composer](https://www.sitepoint.com/re-introducing-composer/) 包装器，因为它在安装和配置期间为您的 Symfony 项目提供了额外的选项。它是本着简单的思想开发的，显然受到了 Laravel 用户友好性的很大影响。请记住，Laravel 之所以达到目前的受欢迎程度，是因为它的易用性和为新来者提供的低门槛，Symfony 希望效仿这一点。

应该指出的是，Flex 和 Symfony 4 仍在开发中，预计将于今年(2017 年)11 月底发布。因此，这篇文章中提到的一些功能在你阅读时可能已经改变，但我们会尽最大努力保持更新。

最值得注意的是，如果 Symfony/Console 不可用，使用 makefile 和 make 工具来构建项目仍然悬而未决，因为它似乎无法在某些操作系统上正常工作。杨奇煜[最近就此举行了一次调查](https://www.surveymonkey.com/r/D857Q9F)，征求社区对替换的建议，绝大多数社区投票赞成只要求制作 Symfony/Console。

![Survey result](img/70a6c71d4bb115a8dadd3ee3ef796c01.png)

## 有什么不同？

最值得注意的是，Flex 尊重即将到来的 Symfony 4 更新，这些更新归结为以下主要变化:

*   PHP 7+是必需的
*   所有文件夹都是可选的。如果您的项目没有使用它，它就不必在那里。这使得目录树更加简单，可读性更好。此外，通常无用的文件如`.htaccess`、`LICENSE`和`README`也被删除了——需要它们的项目可以很容易地添加它们。
*   没有更多的`web`文件夹。取而代之的是`public`文件夹，就像所有其他主要框架一样。这整合了跨生态系统的用户体验。
*   临时文件放在项目文件夹的根目录下的`/var`下，而`/var/cache`子文件夹保留用于长期缓存，就像用于将应用程序部署为只读工件的合并类文件
*   源代码放在`/src`下面。没有`/app`。
*   配置进入`/config`。
*   模板进入`/templates`。
*   Flex 将有自己的 Symfony 验证的包列表，这些包由一个别名单独引用。所以执行`composer require cli`实际上会触发 Flex，它会查看它的包列表，找到标记为`cli`的包(在本例中是 Symfony 控制台)，并安装它。这些“官方”包被称为食谱，可以在这里找到[。要接受用户提交的配方，Flex 的配置中需要设置一个标志为真:`composer config extra.symfony.allow-contrib true`。那些食谱可以在](https://github.com/symfony/recipes)[这里](https://github.com/symfony/recipes-contrib)找到。通过官方认可一些软件包，Symfony 在许多方面变得像 Laravel 一样固执己见。虽然这在某些方面不好，但在很多方面都非常好:一种统一的、固执己见的方式来构建大多数人使用的 Symfony 应用程序，以便每个人都在同一页面上。
*   捆绑包片段不再需要自定义激活和添加到大量文件中。Flex 可以自动完成这项工作，以及删除它们。
*   Symfony 4 将使用像 Laravel 这样的环境变量，而不是配置文件中的参数

## 拔靴带

*像往常一样，我们假设你已经运行了一个健康的虚拟机环境，比如 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) ，这样你就可以跟着做了。*

好吧，让我们用一个示例应用程序来做点手脚。所有 Symfony 应用程序现在都可以从最简单的 Symfony Skeleton 应用程序启动:

```
 composer create-project symfony/skeleton flexy 
```

请注意创建的目录结构。

![Directory structure](img/e79557edcd8a49318b6a6cbad408195b.png)

在`/public`中，我们不再有`app.php`和`app_dev.php`，只有`index.php`档。环境的类型(测试/开发/生产)现在由环境变量决定，并从`/config`文件夹中读取配置。

*注意安装过程的最后提到了`make cache-warmup`被调用，并且您可以运行`make serve`。这就是新 Symfony 使用上面提到的“有争议的”Makefile 方法的地方。这可能会改变。*

开箱即用，在浏览器中打开这个框架会抛出一个错误，因为还没有定义任何路由。让我们解决这个问题。

```
index:
    path: /
    defaults: { _controller: 'App\Controller\DefaultController::index' } 
```

> `config/routes.yaml`

我们需要创建这个控制器及其`index`动作:

```
<?php

namespace App\Controller;
use Symfony\Component\HttpFoundation\Response;

class DefaultController
{
    public function index()
    {
        return new Response('Hello');
    }
} 
```

这将产生一个简单的 Hello 屏幕，如下所示:

![Hello Symfony](img/1a845974f4775d9f04da8ed2bbaae487.png)

### 执行权限

如果你试图用`composer req cli`安装 Symfony/Console 这样的二进制文件，你可能会遇到以下问题:

```
~ bin/console
-bash: bin/console: Permission denied 
```

这是使用虚拟机时的一个已知问题，可以通过以下方法轻松解决:

*   用`php bin/console`运行控制台，而不是直接运行，或者
*   通过执行:`chmod +x bin/console`，向主机上的文件添加“执行”权限(不是从虚拟机内部)。这将允许从 VM 内部直接执行`bin/console`。

## 添加捆绑包

我们构建的“你好”视图有点赤裸裸。让我们添加一些模板。

```
composer req template 
```

这里我们可以使用`template`、`twig`、`templates`或`templating`，定义见[嫩枝配方别名](https://github.com/symfony/recipes/blob/master/symfony/twig-bundle/3.3/manifest.json)。

Symfony 4 / Flex 方法将为我们自动激活这个包，并设置一个带有`base`布局视图的文件夹(`/templates`)，以及一个配置文件(`config/packages/twig.yaml`)。

我们现在可以自由定义 Hello route 的视图了:

```
{% extends '../base.html.twig' %}

{% block body %}
    {{ greeting }}
{% endblock %} 
```

> `/templates/default/index.html.twig`

现在，我们可以修改控制器以返回以下内容，而不是纯文本响应:

```
<?php

namespace App\Controller;

use Symfony\Bundle\FrameworkBundle\Controller\Controller;

class DefaultController extends Controller
{
    public function index()
    {
        return $this->render('default/index.html.twig', ['greeting' => 'hello']);
    }
} 
```

注意，我们不得不扩展`FrameworkBundle`的控制器来访问`render`方法，但这是我们必须做的所有附加配置。我们的问候路线现在酷多了。

![Screenshot of templated view](img/98cbd7b67c4032d3dfe9c13c78db9531.png)

### 大捆

现在，让我们尝试添加一个大捆绑包——一个包含其他几个的捆绑包。用于创建后端的`admin`包是一个不错的选择。此外，这是 Symfony 团队决定正式认可的一个项目，它引入了`orm`配方，这指的是教义——另一个 Symfony 推荐(你能看到行动中的固执己见吗？)

```
composer req admin 
```

在使用管理包之前，我们需要创建一个实体。为此，我们需要一个数据库。创建新的数据库和用户。这个过程应该没问题:

```
mysql -u homestead -psecret
create database flexy character set utf8mb4 collate utf8mb4_unicode_ci; 
```

如果您觉得有必要，也可以随意创建特定于数据库的用户。然后，修改`.env`文件:

```
DATABASE_URL="mysql://homestead:secret@127.0.0.1:3306/flexy?charset=utf8mb4&serverVersion=5.7" 
```

最后，让我们创建一个实体。假设我们正在制作一个网站，让用户向网站提交内容——比如 Reddit，提交链接。我们将有一个名为“提交”的实体，就像这样:

```
<?php

namespace App\Entity;
use Doctrine\ORM\Mapping as ORM;

/**
 * Class Submission
 * @package App\Entity
 *
 * @ORM\Entity
 * @ORM\Table(name="submission")
 */
class Submission
{
    /**
     * @ORM\Column(type="integer")
     * @ORM\Id
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    public $id;

    /**
     * @ORM\Column(type="string", length=255)
     */
    public $name;

    /**
     * @ORM\Column(type="string", length=255)
     */
    public $url;

    /**
     * @ORM\Column(type="text")
     */
    public $description;
} 
```

该实体需要在`config/packages/easy_admin.yml`文件中注册:

```
easy_admin:
    entities:
        - App\Entity\Submission 
```

现在我们将让教义为我们创建这张表。

```
bin/console doctrine:schema:update --force 
```

请注意，如果数据库不存在，您也可以让 Doctrine 创建数据库。查看`doctrine:database:create`中的功能。

如果我们现在访问我们应用程序的`/admin` URL，我们应该会看到类似这样的内容:

![Easy Admin Bundle Back End Screen](img/836db88a9ea257fba88ac7a2998f545c.png)

添加提交内容现在应该非常有效:

![A submission was added](img/165f451849c51313584cdbe0b434b392.png)

### 非官方捆绑

没有得到 Symfony 官方支持的捆绑包怎么办？

为此，我们需要扳动作曲家标志的开关。

```
composer config extra.symfony.allow-contrib true 
```

这也将允许从[这个库](https://github.com/symfony/recipes-contrib)中提取食谱。比方说，我们希望提交的 ID 为`uuid`,而不是一个简单的自动递增的整数。为此，我们可以使用拉姆齐的 UUID 主义 T4 包。当请求 contrib recipes 时，它们通常没有别名，必须完整引用，就像常规包一样。

```
composer req ramsey/uuid-doctrine 
```

因为这是一个社区贡献的包，Symfony 会在你完成下载后给你一个警告。

![A warning about the contrib recipe](img/de0953fdb0f830bc96d99ed85216f741.png)

*注意:contrib repo 是一个死捆绑包和软件包的伟大过滤器——每个关心他们工作的捆绑包/软件包开发人员都会把它转移到那里，所以你可以确定丢失的那些已经停止开发了。*

一旦安装了这个包，我们就可以在我们的项目中使用它了。

首先，我们需要告诉教义它现在是可用的(在我看来，这应该由配方自己来做——还不够自动化！):

```
doctrine:
    dbal:
        url: '%env(DATABASE_URL)%'
        types:
            uuid:  Ramsey\Uuid\Doctrine\UuidType
    orm:
    ... 
```

> `config/packages/doctrine.yaml`

接下来，我们更改提交实体，在它的`id`属性上使用这个类型:

```
...
class Submission
{
    /**
     * @var \Ramsey\Uuid\Uuid
     *
     * @ORM\Id
     * @ORM\Column(type="uuid", unique=true)
     * @ORM\GeneratedValue(strategy="CUSTOM")
     * @ORM\CustomIdGenerator(class="Ramsey\Uuid\Doctrine\UuidGenerator")
     */
    public $id;
... 
```

现在让我们更新数据库并清除当前实体:

```
bin/console doctrine:schema:drop --force
bin/console doctrine:schema:update --force 
```

最后，让我们尝试重新访问`/admin`并添加一个新实体。

![New entity added](img/890d6438b9ad9f50982e4a6395e50d66.png)

毫无疑问，我们的新实体有一个主键 UUID。

*注意:当在 InnoDB 类型的数据库中使用 UUID 作为主键时，建议使用另一种类型，但是为了简洁起见，我们使用了默认值。完整说明[此处](https://github.com/ramsey/uuid-doctrine)。*

### 添加第三方工具

其他第三方工具可以像以前一样使用，只是它们不能由 Flex 自动配置。您必须手动注册它们，并以同样的方式删除它们。因此，建议您将任何需要额外配置才能顺利与 Symfony 一起工作的包移到`contrib` recipes repo 中，以便其他人可以从更顺畅的 Flex 工作流中受益。

## 结论

Symfony Flex 是安装和管理 Symfony 应用程序的现代方式，是走向 Symfony 4 大门的红地毯。不用说，我们对 Symfony 最近进军现代开发和高 DX 领域感到非常兴奋，我们将密切关注它。我们希望这个介绍对你有用！

我们没有用另一个框架对这个最终结果进行基准测试，纯粹是因为当你考虑你可以做的可能的优化时，它没有意义，还因为我们有一个性能月即将到来(整个 11 月)，在此期间我们将消化各种与性能相关的问题，并浏览一整套你可以应用到你的项目中的技术——无论是框架还是应用类型。敬请期待！

## 分享这篇文章