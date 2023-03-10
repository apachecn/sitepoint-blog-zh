# HHVM 和黑克在 Heroku

> 原文：<https://www.sitepoint.com/hhvm-hack-heroku/>

![](img/fcd5b2c5832a514ab480902c3c1b4b87.png)

云应用平台 Heroku 在他们的云上增加了 HHVM 本地支持。

PHP 长期以来一直是高流量生产应用程序的可行解决方案，并且有一段时间拥有最好的包管理器之一，更不用说自“小项目的简单黑客”时代以来它已经发生了显著的变化。PHP“开发模型”在专业圈子里已经不是“黑客”了。撇开亚当·格罗斯的不幸无知不谈，这确实是个大新闻。

## HHVM 支持

HHVM 已经在流行的库和框架兼容性方面进行了新的尝试，最新的 3.1 版本增加了对 [Assetic](https://www.sitepoint.com/getting-started-assetic) 、 [Composer](https://www.sitepoint.com/php-dependency-management-with-composer) 、 [Laravel](https://www.sitepoint.com/memberships-laravel-cashier) 、 [Monolog](https://www.sitepoint.com/logging-with-psr-3-to-improve-reusability) 、 [Guzzle](https://www.sitepoint.com/guzzle-php-http-client) 、 [React](https://www.sitepoint.com/a-first-look-at-react/) 等等的全面支持。该团队现在正在重新集中精力解决 GitHub 的开放问题，特别是那些阻碍人们部署的问题，以达到超过简单测试套件覆盖范围的采用率。[之前](http://hhvm.com/blog/4685/hack-developer-day-2014-keep-hacking)，脸书举办了一次黑客开发者日，以传播意识，并向人们介绍黑客作为其当前生产环境的可行替代方案。

Heroku 认识到了这种潜力，勇敢地一头扎了进去。如果你在 SitePoint 和其他地方阅读我的文章，你会知道我是一个高风险高收益冒险的吸盘，当我看到他们时，我很欣赏这样的努力，所以在一个由于与脸书的联系而极度害怕黑客的未来的社区，我很欣慰地看到像 Heroku 这样强大的公司率先为其他人投入生产。

在 Heroku 上的普通 PHP 和 HHVM 之间切换是微不足道的。 [PHP buildpack](https://github.com/heroku/heroku-buildpack-php) 完全支持它，并且可以在今天使用，尽管值得注意的是我们仍然在处理一个测试产品。

新的运行时还可以利用为超高流量应用程序设计的 [Heroku XL](https://blog.heroku.com/archives/2014/2/3/heroku-xl) 。使用 XL 可以轻松扩展，HHVM 使 PHP 成为现代 web 应用程序最合理的选择。

要开始创建你的第一个 PHP 应用，请看他们的[简单指南](https://blog.heroku.com/archives/2014/4/29/introducing_the_new_php_on_heroku#getting-started)。

## 结论

Heroku 对 HHVM 的支持不仅仅是切换运行时，它还带来了对 Hack 的支持，这是我们在 T1 之前已经介绍过的超级 PHP。这意味着你可以在今天的生产模式*中利用它——不必等待更广泛的采用。*

 *随着每一次发布和每一次重新编译，黑客代码变得越来越好，因此您在计算账单上节省的资源将是巨大的。

如果你想和我们分享你的工作，请告诉我们。特别是，我们正在寻找 Ubuntu 14.04 中的流浪者。这可以启动 HHVM 在当地的发展，并立即准备部署到 Heroku 以及尽快流浪程序完成。对于更多具体的盒子和流浪者配置，请继续关注——教程和下载，以及示例应用程序即将推出。* 

## *分享这篇文章*