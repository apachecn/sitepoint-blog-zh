# Sourcehunt PHP:致力于加密，验证，支付…

> 原文：<https://www.sitepoint.com/sourcehunt-php-contribute-to-crypto-validation-payments/>

上个月，我们[推出了一个名为 Sourcehunt 的新项目](https://www.sitepoint.com/sourcehunt-hacktoberfest-2015-edition-hack-to-glory)——这是一类旨在将注意力引向那些不太受欢迎的、有前途且需要曝光的开源项目的帖子。

我们呼吁新的提交，并积累了一个令人印象深刻的名单。

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

* * *

## AsgardCMS [103 ★]

Asgard 是一个开源的多语言模块化 CMS 平台。尽管它的名字和设计并不吸引人，但它的最大卖点是基于 Laravel 5.1(LTS 版本)，而不是类似项目的 5.0。

CMS 已经存在了一段时间，但不幸的是，它没能聚集起一批像样的追随者。为了有所帮助，除了 Sourcehunt 中提到的，我们还将很快对它进行更深入的研究，试图重现一些重要的网站示例，看看它如何与竞争对手相抗衡。

## memio/spec-gen [21 ★]

我们在之前介绍过的代码生成库 [Memio 有一个名为](https://www.sitepoint.com/automatic-php-code-generation-memio/) [spec-gen](https://github.com/memio/spec-gen) 的衍生项目，是 PhpSpec 测试框架的代码生成扩展。

如果你正在测试还不存在的东西(以真正的 TDD 方式)，PhpSpec 可以[为你](https://www.sitepoint.com/bdd-in-laravel-getting-started-with-behat-and-phpspec)生成类存根，这个插件使它变得更加强大。当然，这是一个特殊的用例，可以解释项目的低采用率——但是我们觉得它应该得到更多的关注。随着 PHP 社区[越来越多地意识到测试](https://www.sitepoint.com/basic-tdd-new-php-package/)，我们确信这将会在你们当中看到一些接受者！

## thunder/short code[59★]

如果你曾经使用 WordPress 不仅仅是发布默认帖子，你会对[短代码](https://codex.wordpress.org/Shortcode)很熟悉。[thunder/short code](https://github.com/thunderer/Shortcode)是一个旨在将他们带入 PHP 世界的包。

现在你可以在你自己的 CMS / app / templating engine 中添加自定义的“平民友好”短代码，让设计者和内容编写者开始工作，而不必处理方法调用和 mustache ( `{{ }}`)环境。

当然，这个库没有做模板引擎的简单扩展所做不到的事情——但是它是引擎/库不可知的，可以很容易地插入到任何其他内容解析器中，这无疑是一个优势。

## 眨眼框架[276 ★]

[Blink framework](https://github.com/bixuehujin/blink) 是“*一个用于构建长时间运行和高性能服务的微型 web 框架[…并且]也是一个应用服务器，可以直接用 PHP 服务请求，不需要 php-fpm 或 Apache 的 mod_php[…它使用 [Swoole 扩展](https://github.com/swoole/swoole-src)作为底层网络库。*

翻译:Blink 是另一种用 PHP 编写的长期运行的 PHP 服务器形式的解决方案(想想 ReactPHP，AppServer…)，但这次是用一个嵌入式框架，并在一些高流量的中国网站上进行了测试。他们提到的 Swoole 扩展是 PHP 的 C 扩展，允许长时间运行的应用程序，有效地防止了在响应服务后 PHP 应用程序的完全重新编译和内存清除。这为危险打开了新的大门，但这正是不变性可以发挥作用的地方——下次再讨论。进一步的澄清可以在这个 [Reddit 帖子](https://www.reddit.com/r/PHP/comments/3pcpg5/blink_framework_a_web_framework_build_for/cw6baku)中找到。

请放心，介绍 Blink 框架的教程即将推出——但与此同时，为什么不尝试一下，这样您就可以帮助同行评审它了？

## 代理人[662]

[代理](https://github.com/jenssegers/agent)帮你找出一个用户代理的信息。不再需要手动解析 UA 字符串来确定访问者是在手机、桌面、Firefox 还是 Safari 上——代理通过一个非常用户友好的界面来处理所有这些事情。

这是一个简单的工具，但它将在许多项目中找到归宿。虽然已经非常受欢迎(在 Reddit 上有点爆炸)，但它仍然需要一些贡献。

## PHP Humanizer [915 ★]

人性化使只对开发者可读的 T2 价值观更加平民化。请看这个例子:

```
use Coduo\PHPHumanizer\String;

echo String::humanize('field_name'); // "Field Name"
echo String::humanize('user_id'); // "User"
echo String::humanize('field_name', false); // "field name" 
```

在他们的自述文件中有更多的例子，但是你得到了要点——这是一个有趣的工具，无可否认比人们想象的更受欢迎，但是考虑到他们需要帮助的两个问题和需要审查的几个拉请求，可以肯定该项目正在寻找更多的贡献者。

## 粒子/验证器[79 ★]

一个普通验证实践的极其简单的实现，[粒子/验证器](https://github.com/particle-php/Validator)最近被标记为 2.0 版本，正在寻找早期采用者和贡献者。验证非常简单:

```
$v = new Validator;

$v->required('user.first_name')->lengthBetween(2, 50)->alpha();
$v->required('user.last_name')->lengthBetween(2, 50)->alpha();
$v->required('newsletter')->bool();

$result = $v->validate([
    'user' => [
        'first_name' => 'John',
        'last_name' => 'D',
    ],
    'newsletter' => true,
]);

$result->isValid(); // bool(false).
$result->getMessages();
/**
 * array(1) {
 *     ["user.last_name"]=> array(1) {
 *         ["Length::TOO_SHORT"]=> string(53) "last_name is too short and must be 2 characters long."
 *     }
 * }
 */ 
```

这是一个高代码质量的全测试覆盖库，它希望在 PHP 世界留下自己的印记，并且它所采用的框架和库不可知论的方法，我们觉得它值得一试。

## Pascal 短时/短时[119√]

shortuuid 是一个简单的 php 库，可以生成简洁、明确、URL 安全的 uuid。

在 URL 中有许多反对顺序 id 的论点，特别是在 API 设计中，所以用唯一的 URL 友好字符串隐藏它们才有意义。

该库使用起来非常简单，并且在涉及到相似字符时非常明确，例如，像 l、1、I、O 和 0 这样看起来相似的字符被完全省略，并且不会被错误键入。

## 文克拉/攀登[218 ★]

如何检查您的 Composer 软件包的新版本？运行`composer update`并期待最好的结果？做预演？

[Climb](https://github.com/vinkla/climb) 扫描你的项目并列出比你的`composer.json`允许的版本更新的依赖项——就这么简单:

```
alt-three/logger                1.0.2      →     1.1.0
graham-campbell/exceptions      5.0.0      →     5.1.0
jenssegers/optimus              0.1.4      →     0.2.0
vinkla/hashids                  1.1.0      →     2.2.0 
```

## flashtag/subsplit-service[218★]

想知道像 Zend 或 Symfony 这样的大项目是如何将它们的组件分离到只读的子存储库中的吗？例如， [Symfony Yaml](https://github.com/symfony/yaml) 组件是一个只读组件 repo，但是也可以在主框架中找到[。](https://github.com/symfony/symfony/tree/master/src/Symfony/Component/Yaml)

一些团队[已经黑出了自动化这个](https://github.com/Webiny/GitSplit)的解决方案，但是 [Flashtag 的 subsplit-service](https://github.com/flashtag/subsplit-service) 通过挂钩 github 的 webhooks 或者由 cronjobs 触发，将它带到了一个新的高度。当您是该工具的目标受众时，使用非常简单，我们计划很快对该工具进行深入研究并与替代工具进行比较。敬请期待！

## 帕尤姆[503 ★]

Payum 是那些[软件包](https://github.com/thephpleague/flysystem)中的一个，开始的时候很简单，但后来每个人都开始使用它，因为它超越了通常过于具体、过于局限、过于缓慢的竞争。

这是一个 Paypal、Stripe、Omnipay 和许多其他服务的支付处理器，可以轻松地与任何库和框架集成，具有一致且经过良好测试的 API 和相当广泛的文档。

Payum 最近发布了 1.0.0 版本，可以利用你的帮助来测试和处理一些问题和 PRs。如果您不能帮助编写代码，我们鼓励您帮助编写文档，因为英语(相当明显)不是团队的母语。

## 伯纳德〔389〕

伯纳德对于作业队列引擎就像[HTTP plug](https://www.sitepoint.com/breaking-free-from-guzzle5-with-php-http-and-httplug)对于 PHP 中的 HTTP 客户端一样。

它抽象了具体的实现，并使使用各种后台作业引擎来满足所有 PHP 排队需求成为可能。

Bernard 远不是一个新项目，但是在过去的几天里，开发又开始了，图书馆也有了新的活力。由于它的潜力，我们觉得有必要对它进行一些关注——你能做出贡献吗？

## 模范石/石盐[125 ★]

Halite *是一个高级加密接口，其所有底层加密操作都依赖于 lib 钠。*

Halite 依赖于优秀的 lib 钠(因此它和它的 PHP 扩展副本是先决条件)，并提供对称和非对称加密和认证。例如，下面是加密/解密文件的方法:

```
<?php
use \ParagonIE\Halite\File;
// Encrypt a file with symmetric-key cryptography
File::encrypt($inputFile, $outputFile, $encryption_key);

// Decrypt a file with symmetric-key cryptography
File::decrypt($inputFile, $outputFile, $encryption_key);

// Encrypt a file with asymmetric-key cryptography
File::seal($inputFile, $outputFile, $enc_public);

// Decrypt a file with asymmetric-key cryptography
File::unseal($inputFile, $outputFile, $enc_secret);

// Get the checksum of a file
$checksum = File::checksum($filename);

// Get the cryptographic signature of a file's checksum
$signature = File::sign($filename, $sign_secret);

// Verify the authenticity of a digitally signed file
$valid = File::verify($filename, $sign_public, $signature); 
```

这可能有点不清楚，除非你有很强的安全意识，所以我们在未来几周为你准备了一个关于 Halite 的教程，一个处理真实用例的教程。我们认为你会喜欢它。

* * *

这就是这个月的 Sourcehunt！如果你正在努力寻找可以参与的项目，请重新阅读上面的列表，看看是否有你感兴趣的项目。如果您确实发送了一两份 PR，请让我们知道，我们将在下一次 Sourcehunt 中报道该活动时重点介绍它们！

要推荐你下个月想在这个列表上看到的项目，只要大声喊出带有标签 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 和 [#php](https://twitter.com/search?q=#php) 的推文——这是组合的[链接。当然，如果你想寻找用另一种语言编写的项目，就要做相应的改变。](https://twitter.com/search?q=#sourcehunt%20AND%20#php)

## 分享这篇文章