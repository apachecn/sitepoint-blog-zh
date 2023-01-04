# Sourcehunt: Cron 管理，黑客马拉松启动者，PHP-GUI…

> 原文：<https://www.sitepoint.com/sourcehunt-cron-management-hackathon-starters-php-gui/>

准备好新版本的 sourcehunt 了吗？准备好那些明星手指！

![Sourcehunt logo](img/407bcf1bded70a8c020759619779150b.png)

## [拉瓦里/克鲁兹](https://github.com/lavary/crunz) [15 ★]

crunz 是一个受 [Laravel scheduler](https://laravel.com/docs/master/scheduling) 启发的用于 PHP 的 cron 管理器，完全独立于框架。

在许多方面，它类似于 [Jobby](https://github.com/jobbyphp/jobby) 和 [cron-manager](https://github.com/MUlt1mate/cron-manager) ，所以我们现在把比较留给你……**。**

 **## Laravel Hackathon 初学者工具包 [777 ★]

我们将让自述文件接管这里…

如果你过去参加过任何黑客马拉松，那么你应该知道启动一个项目需要多少时间:决定构建什么，选择一种编程语言，选择一个 web 框架，选择一个 CSS 框架。一段时间后，你可能在 GitHub 上有了一个初始项目，只有那时其他团队成员才能开始贡献。或者做一些简单的事情，比如用脸书认证登录，怎么样？如果你不熟悉 OAuth 2.0 的工作方式，你可以在上面花上几个小时。

即使你不是用它来举办黑客马拉松，Laravel Hackathon Starter 也一定会节省开发人员几个小时甚至几天的开发时间，并且可以作为 web 开发人员的学习指南。

…

Laravel Hackathon Starter 是用 Laravel 5.2 开发的样板应用程序，让您在黑客马拉松中保持领先。

从许多方面来看，这是一个样本应用程序，与最近发布的 Laravel Spark 没有什么不同，但它带有一种*功能越多越好*的思维模式，旨在让人们在几秒钟内就能使用 MVP。这个初学者工具包的灵感来自 [Sahat 的 NodeJS 初学者工具包](https://github.com/sahat/hackathon-starter)。

## 法克里诺 [56 ★]

我们的作者 Nicola Pietroluongo 的作品 Fakerino 是流行的 Faker 库的替代品，用于在开发期间为您的应用程序生成虚假数据。它的一些主要特点是:

*   伪造的复杂数据(例如，人员:姓名、爱好、国家……)。
*   虚假的单一数据(如姓名、姓氏、整数、文本等)。
*   不同语言的假数据。
*   假的正则表达式数据(如 url => `/www\.\w+\.com/`)。
*   多次伪造数据。
*   用一行代码伪造一个数据库表行。
*   自动伪造一个小字符串或文件(例如，你好{{姓氏}})
*   在一行代码中伪造一个 PHP 对象/实体(用伪造的数据填充公共属性和 setters)。
*   支持 JSON，数组和字符串输出。
*   支持数组，Yaml，XML，PHP，Txt 和 Ini 配置。
*   从命令行界面伪造。

我们将在以后的文章中更好地了解这个库。

## 塞姆赛义德/拉腊维尔-朗曼 [398 ★]

与我们自己的 [nofw](https://github.com/swader/nofw) 没有什么不同，laravel-langman 提供了一个命令行界面，用于为多语言 laravel 应用程序添加和编辑语言。

由于 Laravel 默认情况下不使用原生 gettext，这个包需要一种不同的方法来管理语言文件。因此，它与 laravel-translation-manager 配合得非常好。

我们将在以后的文章中更深入地研究多语言 Laravel 应用程序。

## 智囊团/冲击波 [33 ★]

Blast-orm 是一个基于 Doctrine2 DBAL 的框架无关的易用 orm 包。它提供:

*   数据和关系映射
*   解耦的实体，如 POPO 的(普通的 PHP 对象)
*   自动建议实体定义以及配置自定义定义
*   数据水合到实体，反之亦然
*   收缩到单个实体类的存储库
*   从定义中整合字段
*   字段类型感知转换

这比 Propel 或 CakeORM 等替代产品好/差多少还有待观察——这两种解决方案都很优秀，非常容易接近——因此，如果您想为我们做这项分析，请联系我们！

## Gabriel rcuto/PHP-GUI[1243★]

这个疯狂的东西让 PHP 开发人员只用 PHP 就能构建桌面应用程序(全窗口框架等等)——不需要额外的扩展或第三方系统库。正如自述所说:

*本项目旨在解决现有“GUI 项目”最常见的问题:*

*   安装分机的需要
*   跨平台
*   没有外部依赖性
*   易于安装(`composer require php-gui` ) /易于使用(`$button = new Button`)

为了实现这一点，该库使用了一个 Lazarus 应用程序，这是一个为这一特定目的开发的免费 Pascal 二进制程序。你可以在文档中读到更多关于其背后的科学知识。

一个警告是，你仍然需要在你想运行应用的机器上安装 PHP，所以这对一些人来说肯定是一个障碍(例如，我在我的主机上没有 PHP-只有在我的虚拟机上)。除了我们急切地等待着将 PHP 运行时与开发的桌面应用捆绑在一起的那一天，我们还能说什么呢！

## [snapshot pl/惰性容器](https://github.com/snapshotpl/lazy-container) [11 ★]

这个包让你*“从任何互操作容器中获得一个惰性可加载对象！”*

这意味着你可以用一个尊重`container-interop`的容器将它插入任何应用程序，容器将获得延迟返回对象的能力。

的意思是，如果你有一个超级繁重的服务，一个需要一堆依赖项和子服务才能使用的服务，你可能不希望它在应用程序启动时被实例化，而是只在需要时才被实例化。这个插件让你把服务当作一个代理，一个真实自我的虚假外壳，直到它真正被需要。只有当一个方法被调用时，它才会被完全构建并返回。表演，耶！如果这不是你感兴趣的东西，我希望你能把它加入 [nofw](https://github.com/swader/nofw) ！

## [Idnan/tweet-cli](https://github.com/Idnan/tweet-cli) [8 ★]

这个实验让你直接从命令行发微博。这个包的目的仍然有疑问，但它是一个很好的概念证明，可以用于试验 Twitter API 和命令行应用程序。

这个工具只是一个 cUrl 包装器，所以请随意研究它和/或建议合适的 HTTP 客户端形式的插件，SQLite 对用户名/密码保存的支持，等等。

## [https://github.com/neveldo/TextGenerator](https://github.com/neveldo/TextGenerator)【11★】

从模板和预先提供的数据生成文本:

*   从模板生成文本
*   标签替换
*   文本功能(核心功能:随机、随机、中频、循环)
*   嵌套函数调用
*   跳过包含空值的部分，以防止生成的文本不一致

简而言之，您为包提供一个模板，然后提供一些变量进行插值，它将执行替换。然而，与典型的模板引擎不同，这个生成器将使用各种实用程序随机化、循环和定制输出。如果你发现它的真正用途，请告诉我们——我们很想知道。

* * *

四月到此为止——一如既往，请将你的标签为 [#sourcehunt](https://twitter.com/search?q=#sourcehunt) 和 [#php](https://twitter.com/search?q=#php) 的链接扔给我们——这是组合的[链接。当然，如果你想寻找用另一种语言编写的项目，就要做相应的改变。](https://twitter.com/search?q=#sourcehunt%20AND%20#php)

编码快乐！

## 分享这篇文章**