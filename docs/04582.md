# 2015 年 PHP 技巧、资源和最佳实践

> 原文：<https://www.sitepoint.com/php-tips-resources-best-practices-2015/>

多年来，PHP 有很多名声，但作为一门语言不安全从来不是其中之一。

尽管核心团队有很多缺点，但他们在处理所有的安全问题上相当迅速，将 PHP 更新到最新版本通常会消除所有的担忧。但是像我们这样的最终用户往往会把事情搞砸。我们不更新，我们使用过时的包或有我们不知道的漏洞的包，我们使用古老的扩展…我们以一些真正创造性的方式让自己暴露在风险中。

## 技巧、资源和最佳实践

让我们为 2015 年开个好头，好吗？这篇文章将列出在 2015 年用 PHP 构建任何东西之前，你的大脑/工具带中应该有的重要资源。我会尽我所能保持这篇文章的更新，所以它会无限期地保持相关，但是，正如我所说的，我需要你的帮助。

把一个 PHP 备忘单放在手边是个好主意，这样你就可以参考一些你需要经常使用的基本元素，而不会浪费不必要的时间。

### 让你的 PHP 保持最新

当我写关于遗留代码的癌症时，我关注的是应用程序代码。我仍然坚信你不应该为最小公分母而开发，否则你就有成为阻碍你的语言进步的东西的风险。不过，这不是本节的重点。最近，一个新版本的相关讨论随之而来。

CodeIgniter 和 WordPress 坚持支持 PHP 5.2(这个版本已经死了四年了，不应该存在于任何人的服务器上)， [Anthony Ferrara](https://twitter.com/ircmaxell) 在他的博客文章中回应了[。在你进一步阅读这一部分之前，我恳求你先看看那篇文章。这很重要。](http://blog.ircmaxell.com/2014/12/on-php-version-requirements.html)

这篇文章让一些懒得升级的人愤怒了。

![](img/3adad92b6fa8be42be9ab0c653ef55df.png)

他们争论遗留支持，却没有考虑他们对 PHP 生态系统的破坏。Anthony 写了另一篇文章，在文章中他进一步解释了他的立场，并以[的另一篇文章](http://blog.ircmaxell.com/2014/12/php-install-statistics.html)结束，这篇文章分析了网络上旧 PHP 版本的安装百分比，将它们与稳定性列表进行比较，以找出有多少运行 PHP 的服务器在默认情况下是不安全的和可被黑客攻击的—【今日 。结果至少可以说是令人恐惧的。

如果你正在开发一些东西，并且正在运行除了 PHP 最新主要版本之外的任何东西，我强烈建议你通读一下这些帖子。如果您的客户出于任何原因坚持使用主机 X 或版本 Y，请向他们推荐这些帖子，教育他们，并帮助他们认识到他们的方法的错误——告诉他们他们正在向他们的项目引入的漏洞，并告诉他们如果他们不及时采取行动可能会发生的恐怖事件。升级你的 PHP 版本不是你应该放在“我们可以以后再做”下面的事情。现在就做，而且要经常做。

### 避免过时的主机

受上述讨论的启发， [Phil Sturgeon](https://www.sitepoint.com/author/philsturgeon/) 编辑了一个各种主机支持的当前 PHP 版本的表格。你可以在 [PhpVersions.info](http://phpversions.info/) 找到它，或者如果你想贡献并添加一些缺失的值，可以在 Github 上找到[。](https://github.com/philsturgeon/phpversions.info)

我建议你避开所有的共享主机——现在有非常便宜的虚拟主机提供商，比如 DigitalOcean(你可以通过我的 [ref link](https://www.digitalocean.com/?refcode=ccc3ee7d288d) 随意查看它们，帮你一把)。

![](img/887ceb1f11a724780466b1ad7624aeca.png)

不要成为这些家伙。它开始时天真而简单，但当你不得不在帮别人洗衣服时借出你的伴侣时，它就不再有趣了。

当选择 VPS 时，除了使您免于与其他人共享一个环境或易受其他人介绍的系统不稳定性的影响之外，从头开始设置您自己的服务器是一种您应该熟悉的有趣且有益的体验。此外，你可以在列表中看到，几乎没有人将最新的 PHP 版本作为默认版本——为什么在开始一个新项目时，除了最新的软件之外，什么都不用呢？

### 收养 HTTPS

如今，加密非常重要。不仅仅是为了保护自己免受政府监视，也是为了确保你的客户和网站访问者也受到保护，不会泄露任何个人数据。随着伊利亚·格里戈利克和他的谷歌顶级域名(T1)的拥护者宣布 T2 将在搜索结果中更青睐 HTTPS(T3)的网站，HTTPS 的重要性日益增加是毫无疑问的，即使对于简单的网站也是如此。

![](img/75cce4e0a1cdaf1af7b36b501f075055.png)

虽然有办法让 HTTPS 无处不在，但是我们不应该依赖这些方法——作为网络开发者，我们有责任改善整个网络。HTTPS 与 PHP 没有直接关系，但是每当你开始一个新的 PHP 项目时，在你开始编码之前设置你的服务器使用 HTTPS *通常比在项目中间更容易。为了帮助你完成这个经常是神秘的、令人畏惧的和令人沮丧的任务，(至少在[让我们加密](https://letsencrypt.org/)发布之前)[克里斯·帕尔默](https://twitter.com/fugueish/status/548589307502395392)整理了这个[谷歌文档](https://docs.google.com/document/d/1oRXJUIttqQxuxmjj2tgYjj096IKw4Zcw6eAoIKWZ2oQ/preview?sle=true#)。*

### 保护您的 PHP

不要成为这些家伙的。

![](img/1baf31f2896c2bf0e78a46854a593e53.png)

遵循密码保护、生成、加密和验证方面的最佳实践。阅读书籍，使用那些在[securing HP](http://securingphp.com/)网站上推荐的软件包。

### 保持在正确的道路上

[PHP 正确的方式](https://www.sitepoint.com/php-right-way-book/)负责改善许多 PHP 项目的寿命。无论是书籍形式还是[数字形式](http://www.phptherightway.com/)，PTRW 都是确保你适合应对现代应用开发挑战的不可或缺的资源。如果你觉得它遗漏了什么，或者只是想提供错别字更正或替代资源和指南，请随意通过 Github 提供[。](https://github.com/codeguy/php-the-right-way)

### 避免不良包装

大约两年前，Symfony fame [的杨奇煜·波特尼尔宣布创建了一个 PHP](http://fabien.potencier.org/article/67/don-t-use-php-libraries-with-known-security-issues) 易受攻击的包列表。一年半后，这成为 Symfony 的[标准部分，并成为开源公共领域财产。现在，您可以将 composer.lock 文件发布到他们的 API 或 web 界面，甚至 CLI 工具，它会检查您的项目的漏洞。然而，这仍然需要最终用户*一个*步骤，而我们是懒惰的人。](http://fabien.potencier.org/article/74/the-php-security-advisories-database)

![](img/de6c87d930724244c3463751cf8f573a.png)

加入 Roave 团队，我们中最懒的。他们制作了一个[安全建议](https://packagist.org/packages/roave/security-advisories)包，使用了这个已知漏洞的数据库。正如马可·皮维塔[在他的博客文章中解释的](http://ocramius.github.io/blog/roave-security-advisories-protect-against-composer-packages-with-security-issues/)，你在你的项目中需要它，就像任何其他的包一样，但是这个包不是下载任何东西，而是作为一个元包，不下载任何东西，而是检查你的项目中是否需要坏的版本。它会警告你，甚至阻止试图下载这些软件包，不仅节省你一个检查步骤，但一个步骤，包括删除它们。

我敦促每个从事 PHP 开发的人将这一点包含在他们的项目中。通过联合攻击不安全的共同载体，我们将离大规模根除安全漏洞更近一步。

### 避免常见错误

我们之前已经列出了一些常见错误。阅读以下帖子，了解应该避免什么:

*   [TopTal 列出的 PHP 程序员最常犯的 10 个错误](http://www.toptal.com/php/10-most-common-mistakes-php-programmers-make)
*   PHP 开发人员常犯的 7 个错误
*   [Web 开发中的 18 个关键疏忽](https://www.sitepoint.com/18-critical-oversights-web-development/)

记住这些，你将会省去一大堆麻烦和头疼的事情。

### 虚拟化

用流浪汉！连 [PTRW](http://www.phptherightway.com/#virtualization) 都这么说。

Vagrant 帮助您在小型无头虚拟机中运行克隆环境，这些虚拟机将请求转发到机器内部的端口，让您可以不受干扰地使用主机的浏览器和主机的 ide。想在虚拟机内部嵌套一个虚拟机？你也可以这样做，而且完全安全！在[流浪者标签](https://www.sitepoint.com/blog/)下，我们有一堆流浪者教程和解释，所以如果你对这项技术感到困惑，请仔细阅读。

![](img/fb1e08f705fb274d5e0a0ec06ab62d4c.png)

在 SitePoint，我们有一个官方认可的叫做[家园改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)的[家园流浪盒](https://github.com/laravel/homestead)(由 Laravel 的 Taylor Otwell 编写，但兼容任何框架和 PHP 应用程序)。它可以在五分钟内运行，你将拥有一个完全封装的 PHP 环境——不用担心搞乱你的主机操作系统或其他项目。犯了错误？只需破坏、重建，您就可以在一分钟内回到起点(零代码丢失)!

请注意，我们在所有教程中都使用这个框，所以现在熟悉它不仅可以从长远来看节省您的时间，还可以帮助您轻松地完成我们所做的一切，更不用说它对您的本地开发环境的影响了。

### Blackfire.io

Blackfire.io 是 SensioLabs 的一项服务，sensio labs 是负责 Symfony 框架及其所有相关技术的家伙。它是一个透明的低开销分析器，能够分析您的代码，并提醒您从应用程序逻辑流到与 DB 引擎甚至缓存层的交互的所有问题。Blackfire 已经安装在上面提到的家园改进盒子中，所以如果您使用我们的盒子或原始家园，您就万事俱备了！

![](img/e0d6d9f08f316e486cdf4db09bdc9e49.png)

在问题投入生产之前发现问题！更多关于 Blackfire 的详细教程即将推出！

## 结论

我们查看了一些重要的链接和资源，以便在考虑性能和安全的情况下，正确启动您的 2015 PHP 项目。如果你已经在使用所有这些方法，这对你有好处——你可以帮助我们传播信息。告诉你的朋友和开发者圈子，指引他们到这里，指出那些问你如何开始我们的方式的新手，每当有人告诉你应该支持遗留代码*和旧的 PHP 版本*可以的时候，参考帖子中的具体链接。把他们送过来，我们会揍他们的！**

不同意这些吗？你能添加一些我们链接到的资源中没有提到的关键资源吗？请告诉我们——我会确保列表得到更新！

## 分享这篇文章