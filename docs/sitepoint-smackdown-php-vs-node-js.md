# site point smack down:PHP vs node . js

> 原文：<https://www.sitepoint.com/sitepoint-smackdown-php-vs-node-js/>

![The PHP vs Node gloves are on](img/7f00a679b73c547652841f151d763cc7.png)

网络是不断变化的技术景观。服务器端开发人员在 Java、C 和 Perl 等老牌主流语言和 Ruby、Clojure 和 Go 等新的以 web 为中心的语言之间有着令人困惑的选择。假设您的应用程序工作正常，那么您选择什么并不重要。

但是那些 web 开发新手如何做出明智的选择呢？

我希望不要引发一场圣战，但是我正在让两个开发学科相互对抗:

*   [**PHP**](http://php.net/)
    PHP was created by Rasmus Lerdorf in 1994\. It is processed by an interpreter normally installed as a module in a web server such as Apache or Nginx.

    PHP 代码可以和 HTML 混合使用。这不一定是最佳实践，但是那些语言新手可以很快产生有用的代码。它为这种语言的流行做出了贡献，现在超过 80%的世界网络服务器使用 PHP。这在很大程度上得益于[WordPress](https://wordpress.org/)——一个为[四分之一的网站](http://w3techs.com/technologies/overview/content_management/all)提供动力的 PHP 内容管理系统。

*   [**Node.js**](https://nodejs.org/)
    Node.js was created by Ryan Dahl in 2009\. It uses Google’s [V8 JavaScript engine](https://code.google.com/p/v8/), which also powers client-side code in the [Chrome web browser](http://www.google.com/chrome/). Unusually, the platform has built-in libraries to handle web requests and responses — you don’t need a separate web server or other dependencies.

    Node.js 相对较新，但已经迅速获得关注。它被微软、雅虎、LinkedIn 和 PayPal 等公司使用。

## C#，Java，Ruby，Python，Perl，Erlang，C++，Go，Dart，Scala，Haskell 等等哪里去了？

一篇比较各种选择的文章会很长。你能读一下吗？你希望一个开发人员知道所有这些吗？我将这种攻击局限于 PHP 和 Node.js，因为:

1.  很好的对比。它们都是开源的，主要针对 web 开发，也适用于类似的项目。
2.  PHP 是一门历史悠久的语言，但 Node.js 是一个年轻的新贵，正受到越来越多的关注。PHP 开发人员应该相信 Node.js 的炒作吗？他们应该考虑转行吗？
3.  我了解并热爱这些语言。我从 20 世纪 90 年代末开始使用 PHP 和 JavaScript 进行开发，有了几年的 Node.js 经验。我也涉足过其他技术，但在这篇评论中无法对它们做出公正的评价。

此外，我比较了多少种语言并不重要。有人，在某个地方，会抱怨我没有包括他们的最爱！

## 关于 SitePoint 对决

开发人员花了很多年来磨练他们的技术。有些人被强加了语言，但那些达到忍者水平的人通常会根据一系列因素做出自己的选择。这是主观的；你将促进和捍卫你的技术决策。

也就是说，SitePoint 对决不是*“用任何适合你的，伙计”*评论。我会根据自己的经验、要求和偏见提出建议。你会同意一些观点，不同意另一些观点；太好了，你的评论将帮助其他人做出明智的选择。

## 评价方法

下面十回合比较 PHP 和 Node.js。每一回合都考虑了一个可以应用于任何 web 技术的一般开发挑战。我们就不深究了；很少有人会关心随机数生成器或者数组排序算法的相对优劣。

总赢家将是赢得最多回合的技术。准备好了吗？让战斗开始吧…

## 第一轮:开始

你能多快建立一个“Hello World”网页？在 PHP 中:

```
<?php
	echo 'Hello World!';
?>
```

代码可以放在 PHP 引擎解释的任何文件中——通常是扩展名为`.php`的文件。在浏览器中输入映射到该文件的 URL，就完成了。

诚然，这不是故事的全部。代码只能通过安装了 PHP 的 web 服务器运行。(PHP 有一个内置的服务器，尽管最好使用更健壮的)。大多数操作系统提供服务器软件，如 Windows 上的 IIS 或 Mac 和 Linux 上的 Apache，尽管它们需要启用和配置。使用预构建的设置，如 [XAMPP](https://www.apachefriends.org/) 或虚拟操作系统映像(如[流浪者](https://www.sitepoint.com/re-introducing-vagrant-right-way-start-php/))通常更简单。甚至更简单:上传你的文件到几乎任何网络主机。

相比之下，安装 Node.js 轻而易举。你可以[下载安装程序](https://nodejs.org/download/)或者[使用软件包管理器](https://github.com/joyent/node/wiki/Installing-Node.js-via-package-manager)。因此，让我们在`hello.js`中创建我们的网页:

```
var http = require('http');
http.createServer(function (req, res) {
	res.writeHead(200, {'Content-Type': 'text/plain'});
	res.end('Hello World!');
}).listen(3000, '127.0.0.1');
```

您需要使用`node hello.js`从终端启动应用程序，然后才能在浏览器中访问 http://127.0.0.1:3000/我们用五行代码创建了一个小型的 web 服务器，令人惊奇的是，即使那些有丰富客户端 JavaScript 经验的人也很难理解它。

**PHP** 概念上更简单，赢了这一轮。懂几个 PHP 语句的可以写点有用的。它有更多的软件依赖性，但 PHP 概念对新开发人员来说不那么令人畏惧。

在了解一些 JavaScript 和编写 Node.js 应用程序之间有一个更大的智力飞跃。开发方法不同于大多数服务器端技术，您需要理解相当复杂的概念，比如闭包和回调函数。

## 第 2 轮:帮助和支持

如果没有来自官方文档和资源的一些开发帮助，比如[课程](https://learnable.com/topics/all)、[论坛](https://community.sitepoint.com/)和 [StackOverflow](http://stackoverflow.com/) ，你不会走得太远。 **PHP** 轻松拿下本轮；它有很棒的[手册](http://php.net/docs.php)和二十年的 Q & As。无论你在做什么，总有人会遇到类似的问题。

Node.js 有很好的文档,但是比较年轻，可用的帮助也比较少。JavaScript 和 PHP 一样早就存在了，但是大部分的帮助都与浏览器内开发有关。这很少有帮助。

## 第三轮:语言语法

语句和结构是否符合逻辑并且易于使用？

与一些语言和框架不同，PHP 不会强迫你以特定的方式工作，而是与你一起成长。您可以从几个多行程序开始，添加函数，发展到简单的类似 PHP4 的对象，最终编写出漂亮的面向对象的 MVC PHP5+应用程序。你的代码可能开始时很混乱，但是它会随着你的理解而工作和发展。

PHP 语法可以在不同版本之间改变，但是向后兼容性通常是好的。不幸的是，这导致了一个问题: *PHP 很乱*。例如，如何计算一个字符串中的字符数？难道是`count`？`str_len`？`strlen`？`mb_strlen`？有数百个函数，它们的命名可能不一致。试着不看手册写几行代码。

JavaScript 相对简洁，只有几十个核心语句。也就是说，这种语法吸引了开发人员的注意，因为它的原型对象模型看起来很熟悉，但实际上并不熟悉。你还会发现对数学错误(`0.1 + 0.2 != 0.3`)和类型转换混乱(`'4' + 2 == '42'`和`'4' - 2 == 2`)的抱怨——但这些情况很少引起问题，所有语言都有怪癖。

PHP 有好处，但我将第三轮授予 **Node.js** 。原因包括:

1.  JavaScript 仍然是世界上最容易被误解的语言——但是，一旦这些概念出现，它会让其他语言看起来很麻烦。
2.  与 PHP 相比，JavaScript 代码更简洁。例如，你将不再需要在 JSON 和——谢天谢地——UTF-8 之间来回翻译。
3.  全栈开发者可以在客户端和服务器端使用 JavaScript。你的大脑不需要切换模式。
4.  理解 JavaScript 让你更想用它。我不能说 PHP 也是如此。

## 第 4 轮:开发工具

这两种技术都有很多编辑器、ide、调试器、验证器和其他工具。我考虑过和局，但是有一个工具给了 **Node.js** 一个优势:[NPM——节点包管理器](https://www.npmjs.com/)。npm 允许您安装和管理依赖项、设置配置变量、定义脚本等等。

PHP 的 [Composer 项目](http://getcomposer.org/)受 npm 影响，在某些方面更好。然而，PHP 默认不提供它，它有一个更小的活动库，对社区的影响也更小。

npm 对 Grunt 和 Gulp 等构建工具的发展负有部分责任，这些工具彻底改变了开发。PHP 开发人员可能希望/需要在某个时候安装 Node.js。反之则不然。

## 第 5 轮:环境

这些技术可以在哪里使用和部署？支持哪些平台和生态系统？Web 开发人员经常需要创建不完全适用于 web 的应用程序，例如构建工具、迁移工具、数据库转换脚本等。

有很多方法可以使用 PHP 进行桌面和命令行应用程序开发。你不会用到它们。本质上，PHP 是一种服务器端开发技术。它擅长这项工作，但很少超越这些界限。

几年前，JavaScript 会被认为更具限制性。有一些边缘技术，但它的主要位置是在浏览器中。Node.js 改变了这种看法，JavaScript 项目出现了爆炸式增长。你可以在任何地方使用 JavaScript 在浏览器中，在服务器上，在终端上，在桌面上，甚至在嵌入式系统上。Node.js 让 JavaScript 无处不在。

## 第六轮:整合

开发技术受到限制，除非它们能够与数据库和驱动程序集成。 **PHP** 在这方面很强。它已经存在很多年了，它的扩展系统允许与大量流行和晦涩的 API 直接通信。

Node.js 正在快速追赶，但是您可能很难为较老的、不太流行的技术找到成熟的集成组件。

## 第 7 轮:托管和部署

将你闪亮的新应用程序部署到实时网络服务器有多容易？这是 PHP 又一次明显的胜利。联系随机选择的虚拟主机公司，你会发现大多数都提供 PHP 支持。你可能会得到廉价的 MySQL。PHP 更容易用沙箱保护，更危险的扩展可以被禁用。

Node.js 是一个不同的野兽，服务器端应用程序永久运行。您将需要一个真实/虚拟/云或专业服务器环境，最好具有根 SSH 访问。这对一些主机来说太过分了，尤其是在共享主机上，你可能会让整个系统瘫痪。

Node.js 托管将变得更简单，但我怀疑它是否比得上 FTP 传输一些 PHP 文件。

## 第 8 轮:绩效

PHP 并不笨，有一些项目和选项可以让它变得更快。即使是最苛刻的 PHP 开发人员也很少担心速度，但 Node.js 的性能通常会更好。当然，性能在很大程度上是开发团队的经验和细心的结果，但是 Node.js 有几个优点…

### 更少的依赖性

所有对 PHP 应用程序的请求都必须通过一个 web 服务器路由，该服务器启动 PHP 解释器来运行代码。Node.js 不需要这么多的依赖，虽然你几乎肯定会使用像 [Express](http://expressjs.com/) 这样的服务器框架，但是它是轻量级的，并且是你的应用程序的一部分。

### 更小、更快的解释器

Node.js 比 PHP 解释器更小更灵活。它更少受到传统语言支持的阻碍，谷歌在 V8 性能上进行了巨大的投资。

### 应用程序永久开启

PHP 遵循典型的客户机-服务器模型。每个页面请求都会启动您的应用程序；您加载配置参数，连接到数据库，获取信息并呈现 HTML。Node.js 应用程序永久运行，只需初始化一次。例如，您可以创建一个数据库连接对象，每个人都可以在每个请求中重用它。不可否认，在 PHP 中有很多方法可以使用像 [Memcached](http://memcached.org/) 这样的系统来实现这种类型的行为，但是这并不是这种语言的标准特性。

### 事件驱动的非阻塞 I/O

PHP 和大多数其他服务器端语言使用明显的阻塞执行模型。当您发出一个命令(如从数据库获取信息)时，该命令将在执行下一条语句之前完成执行。Node.js(通常)不会等待。相反，您需要提供一个回调函数，在动作完成后执行，例如

```
// fetch records from a NoSQL database
DB.collection('test').find({}).toArray(process);
console.log('finished');

// process database information
function process(err, recs) {
	if (!err) {
		console.log(recs.length + ' records returned');
	}
}
```

在本例中，控制台将在“返回 N 条记录”之前输出“完成”,因为在检索到所有数据时会调用`process`函数。换句话说，当其他进程繁忙时，解释器可以自由地做其他工作。

请注意，情况很复杂，需要注意的是:

*   Node.js/JavaScript 在单线程上运行，而大多数 web 服务器是多线程的，可以并发处理请求。
*   一个用户长时间运行的 JavaScript 进程会阻止所有其他用户运行代码，除非你拆分任务或者使用 [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers) 。
*   标杆管理是主观的，有缺陷的；您会发现 Node.js 击败 PHP 的例子和 PHP 击败 Node.js 的反例。开发人员擅长证明他们所相信的一切！
*   编写异步事件驱动的代码是复杂的，并且有其自身的挑战。

我只能从经验出发:我的 Node.js 应用程序明显比 PHP 的同类产品快。你的可能不是，但你永远不会知道，直到你尝试。

## 第 9 轮:程序员激情

这可能延伸了*“一般 web 开发挑战”*的目标，但这很重要。如果你每天都害怕写代码，技术是好是坏并不重要。

进行比较有点困难，但是相对来说，很少有 PHP 开发人员对这种语言充满热情。你上一次阅读 PHP 文章或看到吸引观众的演示是什么时候？也许该说的都说了？或许曝光率更低？也许我没有找对地方？PHP7 有一些不错的特性，但是这项技术已经停滞了几年。也就是说，很少有 PHP 开发人员斥责这种语言。

JavaScript 分裂了社区。有爱它的，也有恨它的；很少有开发商持观望态度。然而，对 Node.js 的反应在很大程度上是积极的，该技术正处于风口浪尖。这部分是因为它是新的，赞美可能不会持续太久，但现在， **Node.js** 赢得了这一轮。

## 第 10 轮:未来

使用哪种服务器端语言并不特别重要；即使项目被放弃它也会继续工作(耶 ColdFusion！)的使用可能已经趋于平稳，但许多人仍在继续使用 PHP。这是一个安全的赌注，支持看起来肯定会再持续 20 年。

Node.js 的崛起非常迅速。它提供了一种现代的开发方法，使用与客户端开发相同的语法，并支持革命性的 HTML5 特性，如 web 套接字和服务器发送的事件。关于这种语言的分叉有一些混乱，但是使用继续以指数速度增长。

Node.js 将不可避免地蚕食 PHP 的市场份额，但我怀疑它能否超越。这两种技术都有光明的未来。我宣布这一轮打成平局。

## 总赢家

最终比分:五回合对 **Node.js** ，四回合对 **PHP** ，一场平局。结果比我预期的更接近，可能是任何一种方式。

Node.js 有一个陡峭的学习曲线，不适合新手开发者，但它赢得了这场对决。*就*。如果你是一名热爱这门语言的优秀 JavaScript 程序员，Node.js 不会让你失望。它感觉更新鲜，提供了一种解放性的 web 开发体验— *你不会错过 PHP* 。

但是不要打折扣。PHP 还活着，没有什么理由去追赶 Node.js 的潮流，因为它看起来更快、更新或更时尚。PHP 更容易学习，但支持熟练的专业编程技术。援助无处不在，部署也很简单。即使是死忠的 Node.js 开发者也应该考虑用 PHP 开发更简单的网站和应用。

我的建议是:*评估选项，根据你的需求选择一种语言*。那可比靠*【vs】*这样的文章实用多了！

* * *

你同意克雷格的结论吗？这种比较公平吗？子弹被操纵了吗？不要生气——开始写吧……

**你可能也会对我们由 SitePoint 的 PHP 和 JS 编辑对这篇文章的[回复权感兴趣！](https://www.sitepoint.com/php-vs-node-js-smackdown-right-of-reply/)**

## 分享这篇文章