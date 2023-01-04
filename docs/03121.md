# Sourcehunt PHP Xmas 2015:赠送拉取请求的礼物！

> 原文：<https://www.sitepoint.com/sourcehunt-php-xmas-2015-give-the-gift-of-pull-requests/>

在我们结束这一年之前，还有一期 [Sourcehunt](https://www.sitepoint.com/sourcehunt-hacktoberfest-2015-edition-hack-to-glory) 要发布，作为对所有未被充分代表的开源项目的一份小小的圣诞礼物。我们再给每个人找几个投稿人，好吗？

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

## [mindplay-dk/unbox](https://github.com/mindplay-dk/unbox) [8 ★]

不缺少兼容[容器互操作的](https://github.com/container-interop/container-interop)容器实现。Unbox 以其文档化的方式脱颖而出 Github repo 提供了一个令人印象深刻的全面的方法列表及其解释，将它们很好地包装在一组演示中。

如果包含的性能基准是可靠的，Unbox 可以轻松地位于 Pimple 和 PHP-DI 之间，更接近 Pimple(轻量级)。我们计划在明年年初对容器互操作实现进行全面的比较，但是同时，如果您正在寻找一个经过良好测试并且文档非常完善的容器来熟悉它，为什么不选择这个呢？

## [anob ii/sainsburys-http-service](https://github.com/anobii/sainsburys-http-service)[4★]

这是一个相当奇特的宝石——它标榜自己是 REST 或 HTTP RPC 服务的微框架，但实际上是现有框架——Slim 3 的包装。

通过依赖 Slim 3，它自动继承了用于依赖注入的[容器互操作](https://github.com/container-interop/container-interop)，用于日志记录的 [PSR-3](https://www.sitepoint.com/logging-with-psr-3-to-improve-reusability/) ，以及用于请求和响应处理的 PSR-7，但是有一些额外的限制。例如，控制器不能是闭包，而是独立的非继承类。

repo 中包含的示例应用程序非常简单有趣，但是我个人看不出这个包比自己用 Composer 组装一些小东西有什么优势。

你愿意为我们兜一圈，告诉我们它带来的一切吗？我们会付钱的！

## makasim/yadm [3 ★]

Yadm 是 MongoDB 的 ODM。它大大简化了 Mongo 数据库的读写。它使用起来超级简单，学习曲线很浅，就像 [Medoo 的](https://www.sitepoint.com/getting-started-medoo-examples-use/)一样，所以你很快就能上手运行。

文档可以使用示例和一些语言修复的帮助，所以如果不是代码方面的，为什么不这样做呢？

## [x parse/element finder](https://github.com/xparse/ElementFinder)[0★]

描述说明了一切:

*用优雅的 xpath/css 表达式从 html 中提取数据，用 regexp 在一行中准备数据。*

这与内置的 [xpath](http://php.net/manual/en/simplexmlelement.xpath.php) 方法有什么不同？首先，它提供了一个流畅的 preg_match / split / replace 接口，允许您当场替换字符。此外，它还可以利用 Symfony 的 CSSselector 组件使用 CSS 选择器来遍历 HTML。它比一个有铃铛和哨子的包装纸稍微有点小，但是那些铃铛和哨子确实派上用场了。

文档可以使用更多的例子，作者也提出了一些值得关注的问题，所以为什么不加入反馈，甚至一两个公关呢？

## [API-平台/API-平台](https://github.com/api-platform/api-platform) [493 ★]

到目前为止，API 平台是一个相当受欢迎的项目，但仍然值得一提，纯粹是因为我们还没有时间在频道上深入讨论它(还没有！).这是一个在 Symfony 和 JSON-LD 上构建 API 优先应用程序的框架。

随着 Symfony 3 及其一些辅助组件的发布，它*可能*看起来有点过时，而且一开始使用起来太复杂，因为 Symfony 现在有了 [Guard](https://www.sitepoint.com/easier-authentication-with-guard-in-symfony-3) 来简化身份验证，还有一个微内核，可以有效地使它的设置几乎像 Silex 一样简单，但我仍然鼓励您检查一下这个野兽并形成自己的观点。如果您有一个应用程序想要演示并写下您对该平台的体验，请告诉我们，我们很乐意公布您的发现！

就贡献而言，这个项目相当成熟，因此没有太多问题，但是有几个人可能需要你的帮助！

## [火石/潜伏者](https://github.com/flint/Lurker) [115 ★]

这个包的唯一目的是监视文件的变化。在像 Robo 这样的项目中，当你试图[从你的工作流程](https://www.sitepoint.com/look-ma-no-nodejs-a-php-front-end-workflow-without-node/)中去除 NodeJS 的诅咒时，这是非常有用的。

## [朋友 PHP/pick le](https://github.com/FriendsOfPHP/pickle)【671】

Pickle 是手工编译 PHP 扩展的一个很有前途的替代品。事实上，它看起来也是古老梨的可行替代品。

项目维护人员目前正致力于用 Composer 实现 Pickle，这样我们就可以`require` PHP 扩展并把它们和软件包一起安装，但与此同时，还有许多问题需要解决，其中一些是 bug。如果你想为下一个备受关注的 PHP 包做贡献，现在你的机会来了！

## php-school/cli-menu [389 ★]

很明显，CLI-menu 是一个用来构建漂亮的 CLI 菜单的包。这些家伙没有在一些流行的控制台上构建，而是从零开始，做了一件很棒的工作。

他们的菜单构建简单，非常漂亮，而且整个项目都有很好的文档记录。遗憾的是，目前没有 Windows 支持(就命令行而言，Windows 有点特殊，这就是为什么我们在我们流行的 [Homestead 改进的](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)快速提示中推荐 Git Bash ),但它正在被开发，至少以某种形式。

这个包最初是作为 http://www.phpschool.io/网站的驱动程序开发的，这个网站看起来是一个很好的地方，可以在未来关注高质量的教程！

## humhub/humhub [2991 ★]

最后但同样重要的是，基于相对较新的 [Yii2 框架](https://www.sitepoint.com/7-reasons-choose-yii-2-framework/)，有 Humhub——一个构建社交网络应用的工具包。就个人而言，我觉得这个想法非常有趣，但是代码让我困惑——所使用的方法远非最佳实践。

例如，所有的东西都和前端控制器放在同一个文件夹中，所以它仍然是可以公开访问的，除非你正在使用 Apache(注意包含的`htaccess`文件，它对许多使用 Nginx 的现代服务器没有影响)。的确，这个项目似乎需要[*阿帕奇*](https://www.humhub.org/docs/guide-admin-requirements.html)*。*

 *`composer.json`文件也有一些不稳定的版本约束——很多包都需要`*`(即“任何版本”)。这使得它很容易破损，因为软件包会随着重大变更而进行重大版本升级。

同样，没有以 VM 友好的方式运行工具包的说明，也没有提供方便本地开发的浮动配置，这是大多数现代开发人员感兴趣的，而文档中充满了打字错误和失误。

因此，尽管这个项目很有趣，功能也非常丰富(从主页上运行的演示就可以看出)，但它在代码和文档方面都需要一些帮助。有数百个未决问题，该项目非常受欢迎，所以如果你想在已经很强劲的回购上留下印记，这是一个绝佳的机会。

对 Humhub 更深入的研究将会在明年早些时候出现！

* * *

这就是本月的 source hunt——2015 年的最后一次！如果你正在努力寻找可以参与的项目，请重新阅读上面的列表，看看是否有你感兴趣的项目。如果您确实发送了一两份 PR，请让我们知道，我们将在下一次 Sourcehunt 中报道该活动时重点介绍它们！

要推荐你下个月想在这个列表上看到的项目，只要大声喊出带有标签 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 和 [#php](https://twitter.com/search?q=#php) 的推文——这是组合的[链接。当然，如果你想寻找用另一种语言编写的项目，就要做相应的改变。](https://twitter.com/search?q=#sourcehunt%20AND%20#php)

圣诞快乐，贡献快乐！* 

## *分享这篇文章*