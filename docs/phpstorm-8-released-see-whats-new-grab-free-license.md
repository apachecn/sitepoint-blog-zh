# PhpStorm 8 发布——了解最新动态并获取免费许可证

> 原文：<https://www.sitepoint.com/phpstorm-8-released-see-whats-new-grab-free-license/>

 *编辑:赠品已关闭，获奖者已被通知。感谢您的参与！

本周标志着 PhpStorm 8 的[发布。众所周知，PhpStorm 一直是我个人最喜欢的工具，它从 Zend Studio、Netbeans 和 Sublime Text 那里赢得了我的支持，我们以前也广泛地介绍过这个工具，最著名的是由你真正编写的](https://blog.jetbrains.com/phpstorm/2014/09/phpstorm-8-released-with-blade-behat-wordpress-remote-php-interpreters-and-more/)[快捷方式指南](https://www.sitepoint.com/phpstorm-top-productivity-hacks-shortcuts/)。通过跟踪 EAPs，我们还在 PhpStorm 8 的[新特性发布前对其进行了观察。现在这一天终于到来了，让我们看看还包括什么，并以赠送免费许可证结束。](https://www.sitepoint.com/phpstorm-8-new-features/)

![](img/890b0331a0ca64fa285132e0eb1bd29f.png)

## PhpStorm 8 中的新特性

除了前面提到的对 Drupal 8、多游标、PHPUnit 4、远程解释器、PHPDoc 中的 Markdown、PSR4 检查、从引用它们的文件中打开远程 URL 的能力，当然还有 Behat 支持的支持之外，在前面提到的帖子发表后的这段时间里，还做了一些新的改进。

### 叶片

![](img/2312522c284cb1ef0d0cbb00aed49a29.png)

Laravel 的模板引擎， [Blade](https://laravel.com/docs/templates) ，是[最终支持的](https://blog.jetbrains.com/phpstorm/2014/08/blade-template-engine-support-in-phpstorm-8-eap/)。就像一些读者在上一篇“新特性”文章中指出的那样，刀片支持早就应该提供了，Laravel 的受欢迎程度一直在上升，而 PhpStorm 似乎忽略了这个框架。我绝不是说 IDE 应该内置对所有框架的支持(如果他们使用 CodeIgniter 或 CakePHP 并把它们内置到内核中，我们现在会是什么样子呢？)但是 Laravel 确实成为了事实上的新手框架(比如，最欢迎新手——不要沮丧！)并通过开箱即用的语法亮点、导航辅助和自动完成功能使其模板引擎更易于使用，这将大大有助于确保新来者在第一次遇到模板引擎时不会感到害怕。

### 语言混合

在 PhpStorm 中，您总是可以很容易地在 PHP 代码中编写 SQL 查询，它通常能够很好地识别您想要完成的一切。然而，添加动态值并不总是一帆风顺。在一个对标准和代码整洁度要求非常严格的环境中，右边的一大堆黄色警告真的会让人心烦。

语言注入现在[已经完全修改](https://blog.jetbrains.com/phpstorm/2014/09/language-injections-enhancements-in-phpstorm-8/)以更好地支持检测语言中的语言。继续阅读这篇文章——你会被现在可能实现的流畅的工作流程所震撼。

### 框架支持

![](img/c4c84001eddae2e0e4f7452d8c385037.png)

除了 Blade，实际的框架支持也得到了极大的增强。这个功能不是内置的，因为让 IDE 膨胀那么多没有意义，但是它们都可以通过插件获得[。看看提供的框架列表就知道了！](https://plugins.jetbrains.com/search?correctionAllowed=true&pr=&orderBy=relevance&search=phpStorm)

### Behat 支持已升级

![](img/bfc30ddc6097543dbaa91c756187d03e.png)

除了用于自动完成 Behat 语法的纯 intellisense 之外，对这个 BDD 超级工具的支持也得到了进一步升级。现在，您有了一个完整的仪表板来查看成功和失败的测试，而不仅仅是终端输出、详细的分步报告、您可以为各种场景和条件定义的运行配置等等。预计 SitePoint 上很快会有一些激烈的行为报道，同时，请查看他们的[深度教程](https://confluence.jetbrains.com/display/PhpStorm/Using+Behat+in+PhpStorm)。

### z 射线

![](img/596e6d49354c90028b26907ac27a2d0c.png)

我已经在我的每月[新闻摘要](https://www.sitepoint.com/php-news-may-missed-august-september-2014/)中提到了这一点，但它值得再提一次。我们[在之前的 Zend Server 文章中简要地](https://www.sitepoint.com/getting-know-zend-server-7/)看了一下 Z-Ray，但是没有迹象表明他们曾经将它包含在 PhpStorm 中，而 PhpStorm 实际上是一个竞争产品(Zend Studio vs. PhpStorm，如果曾经有过的话，是一场巨人的冲突)。

Z-Ray——迄今为止最先进的 PHP 调试工具——在 PhpStorm 中的集成是目前最强大的两种 PHP 开发工具的完美结合，可以立即将您转变为一个能力更强的程序员(如果不是开发人员的话)。

除此之外，合作还为 Zend Server 带来了折扣代码，所以如果你使用促销代码 PHPSTORM，你可以在 http://zend.com/server 便宜 20%。

如果你想了解更多关于 Z-Ray 及其与 PhpStorm 的集成的信息，请点击[这里](https://confluence.jetbrains.com/display/PhpStorm/Using+PhpStorm+with+Zend+Server)，别忘了[报名参加定于 9 月 23 日举行的网上研讨会](http://pages.zend.com/JetBrainsWebinar.html?src=jetbrains)，了解如何充分利用它的详细课程。

### 阿帕奇科多娃

![](img/1a71bd7691eb628b9c8773cd4dcb1a47.png)

如果你一直想用 Apache Cordova 进行移动应用程序开发，PhpStorm 现在也支持这个。它需要通过插件添加到[中——默认情况下 PhpStorm 试图变得轻量级——但是一旦完成，您就拥有了“在每个平台上工作-部署到每个平台”IDE 的全部功能。](https://plugins.jetbrains.com/plugin/7436-phonegap-cordova-plugin)

直接引用他们的网站:

这个集成还为 PhoneGap 和 Cordova 提供了一个新的项目生成器，以及一个插件管理器，可以帮助你从 Cordova 插件注册表或任何其他存储库中安装插件。该插件还可以方便您使用 Ionic framework 进行移动开发。

如果这种无所不包的开发力量没有让你兴奋，你应该重新评估你对编码的热爱。

### 内置暂存和单个文件编辑

scratch 插件长期以来一直被用来为基于 IntelliJ 的 ide 提供创建和编辑“Scratch”文件的能力——不属于这里也不属于那里的一次性文件。你可以用它们来编辑单个文件，作为中间的剪贴板，作为待办事项列表，草稿等等。基本上，PhpStorm 中的非项目绑定文本编辑器。

此外，PhpStorm 臭名昭著地不能在不为它们创建整个项目的情况下编辑单个文件。这一点以及在同一窗口中打开多个项目的能力是 Netbeans 一直胜过 PhpStorm 的地方。有了版本 8，上述问题中的三分之二现在都解决了。

scratch 插件现在是内置的，所以你可以随意编写你的 scratch 文件。此外，您可以使用 PhpStorm 打开和编辑单个文件，而无需打开整个项目。

## 泄漏

作为 v8 发布的庆祝姿态，Jetbrains 慷慨地向我们的读者赠送了三个个人许可证。你所需要做的就是用你最喜欢的 PhpStorm 新增功能或者你对 IDE 的看法来评论这篇文章(客观地说，不需要盲目地称赞它)，或者用 [#phpstorm8sp 标签](https://twitter.com/search?f=realtime&q=%23phpstorm8sp&src=typd)将这篇文章发给至少 50 个粉丝，你就有机会了。两周后，我会随机抽取获奖者。

你在用 PhpStorm 吗？如果没有，为什么？如果是的话，你喜欢它吗？如果有的话，你会改变什么？这些新特性中哪一个对你最有吸引力？

## 分享这篇文章*