# 成为全栈开发者

> 原文：<https://www.sitepoint.com/full-stack-developer/>

*这篇受欢迎的文章已于 2017 年 6 月更新，纳入了现代技术。*

* * *

一个能够从[获得原型](https://www.sitepoint.com/premium/books/the-ultimate-guide-to-prototyping?aref=bskvorc)到完整 MVP(最小可行产品)的**全栈开发者**通常被认为是**的万事通，无所不能的大师**，这是有充分理由的。为了定义现代全栈开发者，我们首先需要关注全栈开发者*曾经是什么样的*。

## 那么全栈开发者

很久以前，大约在 2000 年(在互联网时代，17 年是很久以前的事了)，一个全栈开发者是这样的人:

*   用 Photoshop 或 Fireworks 等 Adobe 工具制作网页
*   把这个设计变成 HTML，CSS，和图片上的热点(啊，还记得那些吗？)
*   编写一些基本的 PHP 4.0 脚本(当时还没有面向对象的 PHP 出现)来处理服务器端的逻辑
*   将所有动态数据存储到 MySQL 中，可能会做一些优化
*   通过 FTP 上传到服务器上，然后领取薪水

*请注意，我们在这里谈论的是 PHP 全栈 Flash 或 Coldfusion 开发人员有不同的(但只是略有不同)工作流程。*

那是简单的时光，生活是美好的。一个人的经纪公司多如牛毛，人们下班后仍有时间和家人在一起。

那么*现在的*呢？

## 一个全栈开发者现在需要知道什么？

这些天来，我们有类似这些恐怖事件发生——事情怎么会变成这样？

![App developer doesn't see his kids due to schedule](img/ae7a50b358a92c49c6a7335a756875cd.png)

为了在一个现在已经饱和的市场中取得成功，我们开发人员——他们通常是完美主义者——不愿意授权，并且通常遵循“如果你想把事情做对”的座右铭。这迫使我们陷入一个角落，在那里我们不得不学习所有的东西，所以作为一个全栈开发人员通常会包括以下内容。

### 服务器管理/开发运维

开发人员**必须**知道如何进行基本的服务器管理。这包括但不限于:

*   在非 GUI 环境中，通过终端连接到远程服务器
*   基本 shell 脚本
*   管理服务器上的用户和组
*   管理服务器程序，如 Apache 和 Nginx，为应用提供服务
*   管理防火墙和权限
*   安装新软件和更新发行版

[https://giphy.com/embed/dbtDDSvWErdf2](https://giphy.com/embed/dbtDDSvWErdf2)

[通过 GIPHY](https://giphy.com/gifs/richard-ayoade-it-crowd-maurice-moss-dbtDDSvWErdf2)

除了这些基础知识，开发人员应该知道如何创建良好、健康、隔离的开发环境，无论是在 Docker 还是虚拟机中，比如使用[vagger](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/)。如果以上都是你不熟悉的东西，我们有一本关于它的极好的书[在这里出售](http://bit.ly/phpenv-sp)。

开发人员还应该非常熟悉版本控制系统，以便能够可靠地产生备份和可共享的、协作的代码集合，跟踪随时间的变化。如今，没有版本控制，现代开发人员的工作流程就不完整。我们有一个很棒的视频课程可以购买[这里](https://www.sitepoint.com/premium/courses/introduction-to-git-2902?aref=bskvorc)。

### 云

除了实际管理或虚拟化的服务器，开发人员*可能*需要了解*云*——托管在像 [Heroku](https://www.sitepoint.com/deploying-to-heroku-an-introduction/) 、谷歌云、Azure、AWS 等平台上。

[https://giphy.com/embed/fnjzX4eGNUglq](https://giphy.com/embed/fnjzX4eGNUglq)

[通过 GIPHY](https://giphy.com/gifs/animation-artists-on-tumblr-internet-fnjzX4eGNUglq)

有一点可以说的是，平台和工具[比立即有用的](https://blog.bradfieldcs.com/you-are-not-google-84912cf44afb)更具宣传性，但从长远来看，熟悉每个人都在谈论的服务会派上用场——客户现在随时可能要求更换提供商，做好准备是值得的。幸运的是，我们有 [**终极版**指南来部署所有这些云主机](https://www.sitepoint.com/ultimate-guide-deploying-php-apps-cloud/)。

### 后端

在后端，除了了解选择的语言——在我们的例子中是 PHP 及其众多的框架和 CMSes 开发人员还需要熟悉:

*   web 服务器，如 Nginx 和 Apache，它们与上面的 Devops 相关联
*   不幸的是，NodeJS 用于将 JS、CSS 和其他资产编译成可静态托管的资产。幸运的是，有一些方法可以通过使用 PHP 来避免 NodeJS。
*   像 [Composer](https://www.sitepoint.com/re-introducing-composer/) 这样的工具用于 PHP 本身的包和依赖管理——没有它，现代开发者的环境就不完整
*   良好的 API 设计，因为今天大多数新网站都是基于 API 的，仅仅与一个分离的前端对话(下面会有更多)。
*   像 ElasticSearch ( [此处介绍](https://www.sitepoint.com/introduction-to-elasticsearch-in-php/))这样的搜索引擎对性能非常重要
*   使用像 [Gearman](https://www.sitepoint.com/introduction-gearman-multi-tasking-php/) 这样的工具或者像 [Crunz](https://www.sitepoint.com/framework-agnostic-php-cronjobs-made-easy-with-crunz/) 这样的库的背景工作
*   了解使用 [Varnish](https://www.sitepoint.com/getting-started-with-varnish/) 、 [Redis](https://www.sitepoint.com/speeding-up-existing-apps-with-a-redis-cache/) 和类似的将托管成本分割成个位数的强大工具进行缓存，通常会决定一个项目的成败

### 数据库ˌ资料库

数据库是一个独立的部分，因为除了很好地掌握模式不会经常改变的数据的关系数据库(如 [MySQL](https://www.sitepoint.com/php-mysql-tutorial/) 或 PostgreSQL)，开发人员还需要了解 noSQL 数据库，如 [MongoDB](https://www.sitepoint.com/building-simple-blog-app-mongodb-php/) 、Redis 或[Cassandra](https://www.sitepoint.com/a-quick-introduction-to-apache-cassandra/)——更不用说图形数据库，如 [Neo4j](https://www.sitepoint.com/discover-graph-databases-neo4j-php/) 。

![Database illustration](img/cc4d77291ec0736832d9adccabdbf880.png)

更糟糕的是，这些都在服务器上，在开发者的控制之下。还有几个远程解决方案，如 Mongo-like [RestDB](https://www.sitepoint.com/twitter-analytics-restdb/) 或 Google 拥有的 Firebase 等。

### 前端

在前端，有真正的混乱。

[https://giphy.com/embed/mciMfMijRXIfm](https://giphy.com/embed/mciMfMijRXIfm)

[通过 GIPHY](https://giphy.com/gifs/cheezburger-cars-chaoshour-mciMfMijRXIfm)

要想全面了解如今健康的前端工作流需要什么，请查看 JavaScript 频道上的这篇精彩文章，不过是作为一个 TL；灾难恢复包括以下内容:

*   诺杰斯和 NPM
*   故事
*   像 Typescript、ES6、LESS、SCSS、SaSS 这样的东西的预处理程序和转换程序(像 Babel)
*   建设者和任务执行者喜欢咕噜咕噜和大口大口
*   像 VueJS，React，Angular 这样的框架
*   模块捆绑器，如 Webpack、Browserify、Rollup

### 设计

在设计中，开发人员需要知道如何在将应用程序转换成可用的格式(如 HTML 和 CSS)之前勾画出它的原型。然后可以与一些 JS 进行交互，后端可以用假的 JS 端点进行模拟，只有当这个 shell 应用程序完成并且其用户体验设计和界面设计准备就绪后，才能开始真正的开发。这本身就是一项艰巨的任务，需要一套特殊的工具，如:

*   Photoshop 和/或 Illustrator 或 Gimp / Inkscape 等开源软件——在[设计频道](https://www.sitepoint.com/design-ux/)找到所有相关信息
*   一个好的、快速的编辑器，比如 Atom 或 Sublime Text(这里是 T2 的第 10 个插件，用于完整的堆栈开发)
*   图案选择器，如子图案和颜色选择器，它们将颜色相互匹配
*   [CSS 网格系统](https://www.sitepoint.com/understanding-css-grid-systems/)
*   从前面的 JavaScript 模拟前端部分开始
*   在线部署原型让客户看到并给你反馈的方法——[Ngrok](https://www.sitepoint.com/use-ngrok-test-local-site/)在这方面非常有用

### 记录

为了有效地监控应用程序的健康状况，开发人员需要能够跟踪错误和访问日志，并从中提取有价值的信息。他们需要能够识别和标记趋势，并注意 CPU 或 I/O 使用量的上升，以防止及时停机。这与 Devops 有一点联系，但是需要它自己特殊的技能。

[https://giphy.com/embed/8mpct5wMjdXnW](https://giphy.com/embed/8mpct5wMjdXnW)

[通过 GIPHY](https://giphy.com/gifs/screen-8mpct5wMjdXnW)

我们有一个关于 ELK stack 的[极好的帖子，可以让你为你所有的日志需求做好准备——它结合了用于搜索日志的 ElasticSearch、用于收集日志的 Logstash 和用于仪表板的 Kibana，在仪表板中可以将它们显示成一个良好的堆栈，便于用户友好地监控。甚至有托管解决方案可以帮你解决这个问题，比如](https://www.sitepoint.com/how-can-the-elk-stack-be-used-to-monitor-php-apps/) [Logz.io](http://logz.io?utm_source=sitepoint) 。

### 移动的

最后，还要考虑移动设备。随着 iOS 和 Android 上的 webview 变得越来越有性能，以及 PWAs ( [渐进式网络应用](https://blog.tighten.co/a-brief-introduction-to-progressive-web-apps-or-pwas))的出现，原生应用正在失去其魅力，因为开发它们的过程很复杂。因此，全栈开发人员必须熟悉 PWAs，或者使用像 [React Native](https://www.sitepoint.com/build-android-app-react-native/) 或完整的 [webview](https://www.sitepoint.com/understanding-android-webviews/) 如 NativeScript、塔布里斯、科尔多瓦、Phonegap 或其他实现，以获得一个好的“客户端应用程序”来支持他们的 API(参见上面的后端部分)。

## 做一个全栈开发者值得吗？

那么经历了这一切，值得吗？

首先，应该注意的是，很少有全栈开发人员是这种全栈开发人员——许多人只关注这些技术和方面中的*大部分*,而不是全部，仅仅是因为不可能对它们都给予很好的关注。

其次，至少知道一点点可能不会让你成为一个特定技术的大师，这是真的，但它会让你有能力理解什么进入一个项目，以及一个项目实际上需要这些技术中的哪些。这是一个无价的技能，当授权，开设一个机构，或只是引导一个现有的团队从迷途回到一个明确的道路上。

我可能不是你会奉承的“JavaScript rockstar”、“Elasticsearch ninja”、“MySQL guru”、“Devops maniac”或“Mobile wrangler”，但对我来说，全栈让我能够展开翅膀，测试不同的技术，并在自由职业时为我的客户提供替代的、不寻常的解决方案。钱可以来自各个方面，我可以接受的合同范围从服务器工作到 WP 插件开发，以及介于两者之间的一切，因为我对所有这些事情都比较熟悉(不可否认，前者比后者更熟悉)。对我来说，成为全栈绝对是值得的，如果我把它与我只使用 Flash 的日子相比，虽然我那时更喜欢工作(没有 JavaScript！)，工资更低，项目更难获得。

你呢？你是全栈还是专业化？不管是哪一种，你觉得值得吗？

## 分享这篇文章