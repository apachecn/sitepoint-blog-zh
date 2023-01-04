# 你可能错过的 PHP 新闻——2014 年 9 月、10 月

> 原文：<https://www.sitepoint.com/php-news-may-missed-september-october-2014/>

在一个已经熟悉的格式中，这里有更多你可能在过去一个月左右错过的 PHP 新闻。像往常一样，让我知道我是否错过了什么！

## Drupal 8–beta 1

![](img/f9690f1e98b563da0de2ec95432340dd.png)

期待已久的 Drupal 第 8 版终于发布了——尽管是测试版。你现在可以开着它转一圈，测试一下它的一些最新功能。

完整的发布帖子请见[这里](https://www.drupal.org/drupal-8.0.0-beta1)，要了解如何快速轻松地让自己的实例运行起来，请见我们的[快速提示](https://www.sitepoint.com/quick-tip-running-drupal-8-five-minutes)。

## Guzzle 5 和 Ring PHP

> "*如果你喜欢它，那么你应该给它戴上戒指*"
> -耶稣

不过说真的，Guzzle 5 已经上市了。多疯狂啊。不久前，我们还在了解它，现在它已经拥有了第 5 版和[异步请求](https://mtdowling.com/blog/2014/09/28/guzzle-ring/)。

![](img/a88c364d999d7d0ca2a38e132c32d003.png)

长话短说，在 Guzzle 中收到许多异步支持请求后，多产的 [Michael Dowling](https://mtdowling.com/about/) 创建了 [Ring PHP](https://github.com/guzzle/ringphp) ，Guzzle 请求背后的新驱动程序:

> RingPHP 将成为 Guzzle 5(发布日期 TBD)中的新处理系统。RingPHP 使 Guzzle 能够发送异步请求，大大降低了创建新处理程序的复杂性，并在 Guzzle 中产生更简单、更明确的请求状态转换。
> 
> RingPHP 深受 Clojure、Clojure 的 Ring 和 Clojure 的 HTTP Kit 的影响，因此，它利用了各种函数式编程概念，并使用 PHP 关联数组作为主要的数据结构。函数组合和简单关联数组的使用使得 RingPHP 易于理解、快速、简单地通过中间件在运行时添加行为。

如果你感兴趣，你可以在这里阅读剩下的内容，但简单来说，Guzzle 现在可以进行异步请求，同时很少或根本不兼容版本 4。旧 API 的用户不应该注意到任何区别，除了由于非阻塞重定向和我在上面链接的帖子中谈到的所有其他神奇之处而带来的巨大性能提升。

## PHP 更新

在一般的 PHP 世界中，事情越来越引人注目。其中最有趣的是:

### 三进制集合

我们现在有了一个“isset 三元组”(参见这里的)，它让我们摆脱了这样的代码:

```
$a = isset($_GET['mykey']) ? $_GET['mykey'] : "" 
```

…让我们像这样写同样的东西:

```
$a = $_GET['mykey'] ?? "" 
```

### 替代标签移除

像`"ASP tags (<%) and script tags (<script language=php>) as a means of entering or leaving PHP mode`"这样的替代标签已经被 PHP 淘汰了——在下一个版本中，它们将不再被支持。我个人甚至从来不知道 script 标记，我很高兴它消失了，即使所有使用它的站点如果更新 PHP 主版本而不更新代码，它们的所有 PHP 代码会突然暴露。我们不能为了迁就少数不称职的人而退缩，如果彻底摧毁他们的应用程序是他们继续前进的代价，那就这样吧。

见 RFC [此处](https://wiki.php.net/rfc/remove_alternative_php_tags#vote)。

在这次投票中，让我感到惊讶的是，超过零人投了反对票。除了我上面陈述的原因，我无法想象为什么有人会投反对票。

### PSR 6 号和 7 号

*注:根据 Matthew 在下面的澄清编辑的章节*

PHP-FIG 正在重新定义和编辑两个不太为人所熟悉的 PSR，这两个 PSR 对于流行的包和库的进一步标准化都非常重要。

PSR 6 侧重于缓存并为其定义一个公共接口。[元文档](https://github.com/php-fig/fig-standards/blob/master/proposed/cache-meta.md)的开头说明了一切:

> 缓存是提高任何项目性能的常用方法，这使得缓存库成为许多框架和库的最常见特性之一。这导致了这样一种情况，许多库都有自己的缓存库，具有不同级别的功能。这些差异导致开发人员不得不学习多个系统，这些系统可能会也可能不会提供他们需要的功能。此外，缓存库的开发人员自己也面临着选择，是只支持有限数量的框架，还是创建大量的适配器类。

PSR 7 将实现一个通用的 HTTP 消息接口。该小组正在"[…] *添加 IncomingRequestInterface，它扩展了 RequestInterface，用于描述对常见传入数据源的访问(例如，查询字符串参数、cookies、上传文件元数据等)。).目标是使 IncomingRequest/Response 对无处不在，并且是 MVC 和中间件框架的目标(而不是定制的、每个项目或每个框架的实现)；这将促进编写可以在不同框架之间共享的代码。*此处阅读更多[。](https://github.com/php-fig/fig-standards/blob/master/proposed/http-message-meta.md)

安东尼·费拉拉对这两者都有一些有趣的看法，也就是为什么他不同意他们目前的状态。你可以在这里阅读他的想法。

你可能想知道 PSR 5 号发生了什么。PSR 5 是由 PhpDocumentor 团队提出的 PhpDoc 标准，尽管由于 PHP-FIG 使用的 Google 组的混乱性质和讨论 PSR 的通常支离破碎的方法，我无法辨别它的当前状态或它将何时实现，如果有的话。如果你想了解文档块的标准化，请看这里的。

### PHP 7 时间表

![](img/cb710e10308931d3dacf1ae77e715d6c.png)

最后，内部团队为 PHP 7 的发布确定了一个模糊的时间表——计划在 2015 年 11 月发布。让我们看看他们是否能赶上最后期限！

PHP 7 时间表:[https://wiki.php.net/rfc/php7timeline](https://wiki.php.net/rfc/php7timeline)

## 拉勒维尔家园的 HHVM

拉勒维尔的宅地再次略有升级。盒子本身已经得到了改进，包含了预装在 3.3.0 版本的 HHVM(长期支持)，并在默认情况下激活了 Xdebug 的远程调试。家园回购的文件也被更新，以允许 HHVM 的激活。

自然地，这也意味着我已经将 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 与其上游同步，吸收了所有原始 Homestead 的更改，但保持了您所习惯的简单高效。

要激活 HHVM 的宅地或宅地改良，请参见原始官方[文档](https://laravel.com/docs/4.2/homestead)。

## symphony 2-dx+选项

在过去的几个月里，Symfony2 一直在竭尽全力让开发者更容易接触到它。这种开发人员体验的升级不仅使使用框架变得更加愉快，而且也使一些怀疑者相信了框架的有用性和效率。

![](img/0a24bb8bb6ab184ee80b98b2bfaf1f03.png)

例如，一个更智能的 assets:install 命令现在将[确保](https://symfony.com/blog/new-in-symfony-2-6-smarter-assets-install-command)在不支持符号链接的系统上硬拷贝文件而不是符号链接——所有这些都是默认的。不再有像在这里遇到的[那样的 symlink 安装问题。新的](https://www.sitepoint.com/13-steps-get-ez-publish-5-x-homestead/)[快捷方式](https://symfony.com/blog/new-in-symfony-2-6-new-shortcut-methods-for-controllers)已经被添加到控制器中，调试工具栏将能够[调试 Ajax 请求](https://symfony.com/blog/new-in-symfony-2-6-ajax-requests-in-the-web-debug-toolbar)等等。

你可以在这里找到所有新的发布更新[，用代码演示详细描述。](https://symfony.com/blog/category/living-on-the-edge)

最后但同样重要的是，有一本官方的(免费的)Symfony2 最佳实践书籍——点击这里获取！

## composer–虚拟包和适当要求

![](img/87fe6ffc090334a7aafa1a3e476c8acd.png)

网上出现了三个关于 composer 的非常有趣的帖子。第一个[是](https://devedge.wordpress.com/2014/09/27/composer-and-virtual-packages/)告诉我们虚拟包和包中的`provides`关键词。第二个是对第一个帖子的回应，并对其进行了一些更正，对问题进行了更深入的解释。

最后，[第三帖](https://blog.doh.ms/2014/10/13/installing-composer-packages/)是一个很好的向导，通过正确地要求你的包裹。

## AuraPHP 2–稳定！

![](img/f461593cb8eff80512fbee9a02a2b668.png)

Aura Di 和 Aura Web 包已经稳定，这使得维护人员可以将所有其他 Aura 包也升级到 2.0 稳定版，从而将整个项目推进到 2.0 waters。这个重要的里程碑意味着不仅组件有了新的主要版本，而且所有使用它们的框架都自动有了新的主要版本(Aura 的目标是组件第一，框架第二)。

有关这一进展的完整报告，请参见保罗·琼斯的帖子[这里](https://auraphp.com/blog/2014/10/05/stable-project-releases/)，有关 Aura 2 的快速入门，请参见[哈里的教程](https://harikt.com/blog/2014/10/06/getting-started-with-aura-v2/)。

## 月经 1.0

![](img/184b76296c4c718aed39da1a44e924fc.png)

我们[已经写过的关于](https://www.sitepoint.com/first-look-themosis-framework-wordpress-developers/)的 WordPress 的[框架已经到了 1.0 版本。如需深入了解，请继续关注——教程即将推出。](https://framework.themosis.com/)

Themosis 项目也将自己更名为一个专门从事 WP 开发的机构，并保留了 framework.themosis.com 子域作为自己的框架之家。在他们的主页上阅读这些变化，并让我们知道你是否使用过这个软件——我们很乐意发表你的作品。

## Yii 2

![](img/1f180750f7cb57279ebd0fe7af262248.png)

说到新版本——Yii 2 终于发布了，而且已经退出测试。我们已经在之前的文章中讨论了新版本带来的优势，并在最近重新讨论了这个话题，列出了[为你的下一个项目](https://www.sitepoint.com/7-reasons-choose-yii-2-framework/)选择 Yii 2 的 7 个理由。如果你已经用 Yii 建立了任何东西，并且想要展示它，或者只是对它的教程化和传播感兴趣，请联系我们！

## Phalcon 2 beta 3

![](img/78f212e724bb65e2f07eef7eb0f256d8.png)

[费尔康框架](https://www.sitepoint.com/up-and-running-with-the-fastest-php-framework-on-php7-in-5-mins/)已经达到 [beta 3](https://blog.phalconphp.com/post/100083107700/phalcon-2-beta-3-released) 状态。据说，这可能是正式发布前的最后一个测试版，因为所有的测试都通过了，API 也达到了同等水平。如果你还没有尝试过 Phalcon 2，一定要试一试。要真正快速设置，请查看我们的[快速提示](https://www.sitepoint.com/quick-tip-install-zephir-phalcon-2-vagrant/)快速设置并运行。

## 结论

毫无疑问，这是 PHP 世界中多产的一个月！我错过什么了吗？在下面的评论里告诉我，我们在那里讨论吧！如果你对下一篇新闻有任何建议(大约一个月后到期)，请给我发电子邮件，我会决定它是否缺乏曝光度，足以被列入人们可能错过的新闻名单。感谢阅读！

## 分享这篇文章