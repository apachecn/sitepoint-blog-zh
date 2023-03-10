# Gemfury 的私人作曲家套装

> 原文：<https://www.sitepoint.com/private-composer-packages-gemfury/>

希望你熟悉 [Composer](https://getcomposer.org) ，最新的——也可能是最伟大的——PHP 包管理器。如果没有，看看[亚历山大的介绍](https://www.sitepoint.com/php-dependency-management-with-composer/)，我毫不怀疑你会立刻被它吸引。为了从这篇文章中获得最大的收获，您将需要它的工作知识。

Composer 与 [Packagist](https://packagist.org/) 协同工作，后者是一个公共包的综合存储库。

然而，迟早有一天，你会写自己的包，不管出于什么原因，不能通过 Packagist 开源和共享。

托管这些私有包有几种选择。您可以通过单独添加软件包的存储库来配置项目的`composer.json`文件。或者， [Satis](https://github.com/composer/satis) 允许你生成自己的静态库。或者， [Toran Proxy](https://www.sitepoint.com/personal-packagist-toran-proxy/) 允许你创建一个自托管的私有版本的 Packagist，一旦设置好，管理起来比在你的`composer.json`中指定存储库要容易得多。

[Gemfury](https://gemfury.com/) 是 PaaS 的替代品。除了来自托管解决方案的安心——尽管这是有代价的——一个巨大的优势是它不仅支持 PHP Composer 包，还支持 Ruby Gems、Node.js npm、Python PyPi、APT、Yum 和 Nu-Get。如果你精通多种语言，那就太好了。

让我们深入研究一下如何使用它。

## 设置您的帐户

首先，你需要一个账户。有一个 14 天的试用期，一个仅限于一个合作者的免费帐户和一个托管包，以及一系列其他计划，起价为每月 9 美元。

您可以使用您的电子邮件地址注册，也可以使用您的 Github 帐户注册。头[到网站](https://gemfury.com/)报名。

## 创建您的第一个包

为了演示如何将 Gemfury 用于私有 Composer 包，让我们从头开始创建一个包的过程，稍后我们将把它提交给服务，以便在其他项目中使用。

创建新包的最简单方法是使用以下命令:

```
composer init 
```

它会问你一系列问题；以下是该过程的示例记录:

```
Welcome to the Composer config generator

This command will guide you through creating your composer.json config.

Package name (<vendor>/<name>) [your-username/package]: your-username/coupon
Description []: Generates a discount coupon code using a super-secret algorithm
Author [Your Name <your-username@example.com>]:
Minimum Stability []: dev
License []: MIT

Define your dependencies.

Would you like to define your dependencies (require) interactively [yes]? yes
Search for a package []: faker

Found 15 packages matching faker

   [0] fzaninotto/faker
   [1] fzaninotto/faker
   [2] bobthecow/faker
   [3] tomaj/faker
   [4] typo3/faker
   [5] yiisoft/yii2-faker
   [6] willdurand/faker-bundle
   [7] denheck/faker-context
   [8] coduo/tutu-faker-extension
   [9] davidbadura/faker-bundle
  [10] kphoen/faker-service-provider
  [11] vegas-cmf/faker
  [12] burriko/cake-faker
  [13] bit3/faker-cli
  [14] emanueleminotto/faker-service-provider

Enter package # to add, or the complete package name if it is not listed []: 0
Enter the version constraint to require []: *
Search for a package []:
Would you like to define your dev dependencies (require-dev) interactively [yes]? no

{
    "name": "your-username/coupon",
    "description": "Generates a coupon code",
    "require": {
        "fzaninotto/faker": "*"
    },
    "license": "MIT",
    "authors": [
        {
            "name": "Your Name",
            "email": "your-username@example.com"
        }
    ],
    "minimum-stability": "dev"
}

Do you confirm generation [yes]? yes
```

如您所见，我们正在创建一个简单的包，它有一个依赖项， [Faker](https://github.com/fzaninotto/Faker) 。

接下来，让我们给新创建的`composer.json`添加一行，告诉它在哪里可以找到包的源代码。

```
"autoload": {        
	"psr-0": {
		"Acme\\": "src/"
	}
},
```

现在让我们创建包本身。我们要创建一个只有一个目的的类。采用复杂的绝密专有算法为电子商务平台生成折扣优惠券代码。

在您的工作目录中，创建`src`和`src/Acme`目录，然后将下面的文件命名为`Coupon.php`:

```
<?php namespace Acme;

use Faker\Factory;

class Coupon {

	public static function generate($percent) 
	{
		$faker = Factory::create();

		return sprintf('%s-%s-%d',
			strtoupper(date('M')),
			strtoupper($faker->word()),
			intval($percent));
	}

}
```

现在运行`composer install`来加载我们唯一的依赖项并配置自动加载器。

这就是我们构建的包。现在上传到 Gemfury。

若要继续，您需要您的 API 密钥。如果你去你的仪表板，你会在**设置**下找到它。

构建和上传包的最简单方法就是使用 Git，让 Gemfury 处理剩下的事情。

首先创建一个包含以下内容的`.gitignore`文件:

```
vendor/
composer.lock
```

现在初始化存储库:

```
git init
```

添加文件:

```
git add src
git add composer.json
```

现在我们将添加一个 Git 遥控器。您可以通过选择 Gemfury 仪表板上的 **Get Started** 找到相关的 URL，然后选择“PHP Composer”选项卡。它看起来有点像这样:

```
https://your-username@git.fury.io/your-username/<package-name>.git
```

请务必用您的 Gemfury 用户名替换`your-username`——如果您使用 Github 注册，它将与您的 Github 用户名相同——并将其添加为远程:

```
git remote add fury https://your-username@git.fury.io/your-username/coupon.git
```

此时，您有两个选择。第一种是使用**显式版本控制**，您可以在您的`composer.json`文件中指定版本，如下所示:

```
{
    "name": "your-username/coupon",
    "description": "Generates a coupon code",
		"version": "1.0.0",
    ...
```

> 使用[语义版本](http://semver.org/)很重要。否则，您的包可能无法正确构建；这也可能导致一些措辞奇怪的错误消息。

或者，您可以使用 Git 标签。例如，使用标签创建新版本，如下所示:

```
git tag -a 1.0.0 -m "Version 1.0.0"
```

无论您采用哪种方法，下一步都是承诺:

```
git commit -a -m "Initial commit"
```

最后，运行以下命令:

```
git push fury master --tags
```

这将把你的代码推送到 Gemfury，gem fury 将自动为你构建一个包。

现在，如果您转到您的控制面板，您应该会看到您的新存储库列表。接下来，让我们看看如何在项目中使用它。

## 使用私有包

如果你回到你的仪表板，选择左侧的“回购”，你会发现你的私人回购网址。这应该是保密的，所以要注意安全。它看起来有点像这样:

```
https://php.fury.io/SECRET-CODE/your-username/
```

是`SECRET-CODE`让它不可猜测，因此*实际上是*私有的。

现在将它添加到您的项目的`composer.json`:

```
"repositories": [{
	"type": "composer",
	"url":  "https://php.fury.io/SECRET-CODE/your-username/"
}],
```

> 您只需要添加这一个存储库，就可以使用您用 Gemfury 创建的任何私有 Composer 包。不需要在每次你想使用一个包的时候添加单独的库。

现在你可以像在 Packagist 上一样使用你的私人包。下面是一个项目`composer.json`的完整示例:

```
{
    "name": "your-username/my-ecommerce-platform",
    "authors": [
        {
            "name": "Your Name",
            "email": "your-username@example.com"
        }
    ],
    "repositories": [{
		  "type": "composer",
		  "url":  "https://php.fury.io/SECRET-CODE/your-username/"
		}],
    "require": {
    	"your-username/coupon": "1.0.1"
    }
}
```

## 其他方法

就个人而言，我相信使用 Git 和标签是管理你的包的最简单和最有效的方法。

或者，如果您愿意，您可以通过压缩您的包的源代码并通过 Gemfury dashboard 上传来自己构建它。

## 命令行工具

Gemfury 还提供了一个命令行工具。要安装它:

```
sudo gem install gemfury
```

要列出您的软件包，您可以使用以下命令:

```
fury list
```

也可以查看特定包的版本:

```
fury versions package-name
```

有关 CLI 的更多信息，请访问文档的[相关章节](http://devcenter.gemfury.com/articles/gemfury-cli.html)。

## 摘要

在本文中，我研究了 Gemfury，它是管理私有存储库的众多选项之一。作为一个 PaaS 解决方案，它没有 Toran 等自托管选项的额外负担，并且比 Satis 更易于使用。它还具有支持跨各种语言的包的巨大优势，从 PHP Composer 包到 Ruby Gems 和 Node.js npm。当然，作为一个 PaaS 解决方案，它确实是有成本的——但是为什么不使用免费试用或免费的单一套餐计划来尝试一下，看看它是否适合你。

## 分享这篇文章