# Diffbot PHP 客户端的强大定制实体

> 原文：<https://www.sitepoint.com/powerful-custom-entities-with-the-diffbot-php-client/>

不久前，我们研究了用于处理网页的机器学习人工智能 [Diffbot](http://diffbot.com) ，作为[提取 SitePoint 作者作品集](https://www.sitepoint.com/analyze-sitepoint-author-portfolios-diffbot/)的一种手段。该教程只关注使用 Diffbot UI，而使用创建的 API 需要手动 ping API 端点。此外，从那时起，我们处理的页面的设计已经改变，因此 API 不再可靠地工作。

在本教程中，除了重新构建 API 以使其再次工作之外，我们将使用[官方 Diffbot 客户端](https://github.com/Swader/diffbot-php-client)来构建对应于我们所寻找的数据(作者作品集)的定制实体。

![Diffbot logo](img/6b41cfebf61572ee7f78bfe329c85d11.png)

## 拔靴带

我们将像往常一样使用经过改良的宅地。下面的几个命令将引导游民箱，创建项目文件夹，并安装 Diffbot 客户端。

```
git clone https://github.com/swader/homestead_improved hi_diffbot_authorfolio; cd hi_diffbot_authorfolio  ./bin/folderfix.sh
vagrant up; vagrant ssh
mkdir -p Code/Project/public; cd Code/Project; touch public/index.php
composer require swader/diffbot-php-client
```

此外，我们可以安装 Symfony 的 vardumper 作为开发需求，只是为了获得更漂亮的调试输出。

```
composer require symfony/var-dumper --dev
```

如果我们现在给`index.php`以下内容，假设我们将`homestead.app`添加到我们主机的`/etc/hosts`文件中，如果我们在浏览器中访问 [http://homestead.app](http://homestead.app) ，我们应该会看到“Hello world ”:

```
<?php // index.php  require  '../vendor/autoload.php'; echo "Hello World";
```

## Diffbot 初始化

*注意，要跟进，你需要一个免费的 Diffbot 令牌——在这里获得一个[。](http://www.diffbot.com/pricing)*

```
define('TOKEN',  'token');  use  Swader\Diffbot\Diffbot; $d =  new  Diffbot(TOKEN);
```

这就是我们初始化 Diffbot 所需的全部内容。让我们在一篇样本文章上测试一下。

```
echo $d->createArticleAPI('https://www.sitepoint.com/crawling-searching-entire-domains-diffbot')->call()->getAuthor();  // Bruno Skvorc
```

## 自定义 API

首先，我们需要从[最后一次发布](https://www.sitepoint.com/analyze-sitepoint-author-portfolios-diffbot/)开始重建我们的 API，这样它就可以再次运行了。我们通过登录开发面板并转到 https://www.diffbot.com/dev/customize/的来实现这一点。

让我们创建一个新的 API:

![API creation screenshot](img/3657139de515fc070d2ef49c3caaa93d.png)

在输入一个类似于`www.sitepoint.com/author/bskvorc/`的示例 URL 后，我们可以添加一些自定义字段，比如`author`:

![Author field creation](img/f110f2e647ca11a95d10c07108374c7e.png)

我们可以使用同样的方法来定义像`bio`和`nextPage`这样的字段，以便激活 Diffbot 的[自动分页](http://support.diffbot.com/apitoolkit/automatically-concatenating-pages-using-the-nextpage-field/):

![Next page field creation](img/28f02488bfa08d1f46e873d68a13ce89.png)

我们还需要定义一个集合，该集合将收集所有的商品卡片并对它们进行处理。制作一个集合需要选择一个元素，该元素的选择器被重复多次。在我们的例子中，这是`.article-list`类的`li`元素。

![Defining a new collection](img/9cf8a1bd3b2f42933de13c4e5f0ade54.png)

在该集合中，我们为每张卡片定义了字段(如果有疑问，浏览器的开发工具可以帮助我们识别需要指定为选择器的类和元素，以获得期望的结果):

![Defining fields inside the collection](img/f7cfdd56f2c07cff8b93f79684cf2f46.png)

![Primary category field definition](img/2e5f9a743e4df166faa60d5771f05445.png)

除了标题和主要类别，我们还应该提取出版日期，主要类别的网址，文章的网址，喜欢的数量等。为了简洁起见，我们将跳过这些定义。

如果我们现在直接访问我们的端点，而不是在 API 工具包中，我们应该得到完全合并的 9 页帖子，按照我们想要的方式进行处理。

```
http://api.diffbot.com/v3/diffpoint?token=token&url=https://www.sitepoint.com/author/bskvorc/
```

![Diffpoint custom API result](img/a383f4d4a3848c67e104b61725ccb3e8.png)

我们可以看到，API 成功地找到了集合中的所有页面，甚至返回了最早的帖子。

## 扩展客户端

让我们看看自定义 API 是否如预期的那样运行。

```
echo $d->createCustomAPI('https://www.sitepoint.com/author/bskvorc',  'diffpoint')->call()->getBio();
```

这应该与正确的简历相呼应。

这一步在某种程度上是可选的。我们可以按原样使用返回的数据，只需遍历键和数组，但是让我们假设我们的数据比一个简单的投资组合页面复杂得多，不管怎样都要做好。

我们需要两个新类:实体工厂和实体。让我们在`/src/AuthorFolio.php`和`src/CustomFactory.php`创建它们，相对于我们项目的根(`src`在根文件夹中)。

### 作者组合

让我们从新实体开始。根据[文档](http://diffbot-php-client-docs.readthedocs.org/en/latest/abstract-entity.html)，我们有一个可以扩展的抽象类。

```
<?php // src/AuthorFolio.php  namespace  My\Custom;  use  Swader\Diffbot\Abstracts\Entity;  class  AuthorFolio  extends  Entity  {  }
```

我们扩展了抽象实体，并赋予新实体自己的名称空间。这是可选的，但是很有用。此时，该实体已经可以使用了——它本质上与[通配符实体](http://diffbot-php-client-docs.readthedocs.org/en/latest/api-custom.html#namespace-Swader%5CDiffbot%5CEntity)相同，后者使用神奇的方法来解析对返回数据的各种属性的请求(这就是为什么上面示例中的`getBio`方法无需我们定义任何东西就可以工作)。但是我们的目标是让 AuthorFolio 类变得冗长，支持定制的、特定于 SitePoint 的数据，可能还有一些快捷方法。我们现在就开始吧。

API 将返回作者文章的完整列表——但不是它们的数量。要找出一个作者有多少篇文章，我们必须`count`属性`articles`，所以让我们用一个快捷的方法来包装这个过程。我们还可以用@property 标签告诉 PHPStorm 这个类将有一个`articles`属性，这样它就不会抱怨用魔法方法访问这个字段了:

```
<?php // src/AuthorFolio.php  namespace  My\Custom;  use  Swader\Diffbot\Abstracts\Entity;  /**
 * Class AuthorFolio
 * @property array articles
 * @package My\Custom
 */  class  AuthorFolio  extends  Entity  {  public  function getType()  {  return  'authorfolio';  }  public  function getNumPosts()  {  return count($this->articles);  }  }
```

我们可以定义的其他方法有`totalLikes`、`activeSince`、`favoredCategory`等。

### 定制工厂

实体准备好了，是时候定义一个定制工厂，将它绑定到我们从定制 API 获得的返回数据的类型。我们正在编写默认工厂的替代方法[，但是原始类已经包含了一些我们可以使用的方法——它被设计成可以被其子类重用。因此，我们只需要扩展原始类型，将新类型映射到我们的自定义实体，我们就完成了。](http://diffbot-php-client-docs.readthedocs.org/en/latest/class-entityfactory.html)

```
<?php // src/CustomFactory.php  namespace  My\Custom;  use  Swader\Diffbot\Factory\Entity;  class  CustomFactory  extends  Entity  {  public  function __construct()  { $this->apiEntities = array_merge( $this->apiEntities,  ['diffpoint'  =>  '\My\Custom\AuthorFolio']  );  }  }
```

我们将最初的 API 到实体列表与我们自己的定制绑定合并，从而告诉工厂类既要关注标准类型和 API，也要关注我们的新类型和 API。这意味着我们也可以继续将这个工厂用于默认的 Diffbot APIs。

### 接通工厂电源

为了使我们的类可以自动加载，我们应该将它们添加到`composer.json`:

```
 "autoload":  {  "psr-4":  {  "My\\Custom\\":  "src"  }  }
```

我们通过运行`composer dump-autoload`来激活这些新的自动加载映射。

接下来，我们实例化新工厂，将其插入 Diffbot 实例，并测试 API:

```
$d =  new  Diffbot(TOKEN); $d->setEntityFactory(new  My\Custom\CustomFactory()); $api = $d->createCustomAPI('https://www.sitepoint.com/author/bskvorc',  'diffpoint'); $api->setTimeout(120000); $result = $api->call();  dump($result->getNumPosts());
```

![Result image](img/4f6182ac45fdbb058c976a9ffdee080f.png)

*注意，我们增加了超时时间，因为在 Diffbot 端，大量分页的帖子可能需要一段时间才能呈现。*

## 结论

在本教程中，通过使用[官方 Diffbot 客户端](https://github.com/Swader/diffbot-php-client)，我们构建了自定义实体并构建了一个自定义 API 来返回它们。我们看到了利用机器学习和光学内容处理从任何类型的网站获取任意数据是多么容易，我们也看到了 Diffbot 客户端的可定制性是多么强。

虽然这是一个相当简单的例子，但不难想象在更复杂的实体上的高级用例，或者可能它们中的几个分布在多个 API 上，都通过单个 EntityFactory 处理，每个自定义 API 对应一个特殊的实体类型。对于训练有素的视觉神经网络，唯一的处理限制是一个人的想象力。

如果您想了解更多关于 Diffbot 客户端的信息，请查看[完整文档](http://diffbot-php-client-docs.readthedocs.org/en/latest/index.html)并亲自体验一下——别忘了获取一个为期两周的免费演示令牌！

## 分享这篇文章