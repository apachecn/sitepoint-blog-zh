# PHP 开发人员常犯的 7 个错误

> 原文：<https://www.sitepoint.com/7-mistakes-commonly-made-php-developers/>

早在 6 月底，自由市场 TopTal 发表了一篇关于 PHP 程序员最常犯的 10 个错误的文章。这个列表并不详尽，但写得很好，指出了一些非常有趣的陷阱，即使我个人并不认为这些错误很常见。

我鼓励你通读一下——它有一些你应该知道的真正有价值的信息——尤其是前八点。几天前， [Anna Filina 在列表](http://afilina.com/common-php-mistakes/)上增加了七个新条目。虽然不太具体和常见，但她的观点仍然有分量，应该在开发时加以考虑。

## PHP 开发人员经常犯的 7 个错误

TopTal 的一个人让我看一下他们的列表，并可能做出贡献，我们社交网络上的一些粉丝也表示有兴趣看到该列表继续下去，所以我想借此机会将我自己的一些条目添加到该列表中，我需要反复警告我的团队成员或粉丝。

### 1.使用 mysql 扩展

这是一则很老的新闻了，但是许多开发商忘记了这个事实，这是令人担忧的。当使用 SQL 数据库，特别是 mysql 时，太多的开发人员仍然选择 MySQL 扩展。mysql 扩展[已被官方弃用](http://php.net/manual/en/migration55.deprecated.php)。它不安全，不可靠，不支持 SSL，并且缺少一些现代 MySQL 的特性。它还会生成弃用通知，不会破坏你的应用程序，它们只是出现在你的应用程序的顶部。有趣的是，这意味着也可以简单地搜索所有使用这种不安全设置的网站，只需简单地寻找[这个](https://www.google.com/search?q=%22The+mysql+extension+is+deprecated+and+will+be+removed+in+the+future%22&oq=%22The+mysql+extension+is+deprecated+and+will+be+removed+in+the+future%22&aqs=chrome..69i57j0l2.1997j0j7&sourceid=chrome&es_sm=91&ie=UTF-8#q=%22The+mysql+extension+is+deprecated+and+will+be+removed+in+the+future%22&start=30)。由于这种混乱，这些应用程序面临的伤害是惊人的。

不要使用 mysql，选择一个替代品:MySQL 或 PDO。例如，使用 MySQLi 几乎和在 API 调用的末尾添加字母“I”一样简单:

```
$c = mysql_connect("host",  "user",  "pass"); mysql_select_db("database"); $result = mysql_query("SELECT * FROM posts LIMIT 1"); $row = mysql_fetch_assoc($result);
```

相对

```
$mysqli =  new mysqli("host",  "user",  "pass",  "database"); $result = $mysqli->query("SELECT * FROM posts LIMIT 1"); $row = $result->fetch_assoc();
```

这就是使设置更加安全所需要的一切。

不过，你应该选择 PDO。第二点对此有更多的介绍。

### 2.不使用 PDO

不要误解我的意思，mysql 比古老的 MySQL 扩展领先了好几代。它保持最新、安全、可靠和快速。然而，它是 mysql 特有的。相反，使用 PDO 可以让您使用一些非常实用的面向对象语法，并让您准备好使用其他 SQL 数据库，如 PostgreSQL、MS SQL 等等。更重要的是，PDO 将允许你使用[命名参数](http://php.net/manual/en/pdo.prepare.php)，这是一个非常有用的特性，很少有人能想象在适当利用它之后去做别的事情。最后但同样重要的是，还有[这个](http://stackoverflow.com/questions/13569/mysqli-or-pdo-what-are-the-pros-and-cons):你可以将获取的数据直接注入到一个新对象中，这在大型项目中是一个令人愉快的省时方法。

### 3.不重写 URL

另一个经常被忽视且容易解决的问题。像`myapp.com/index.php?p=34&g=24`这样的网址在这个时代是不可接受的。由于编写一个涵盖所有服务器和框架的好的 URL 重写指南非常困难，几乎每个框架都有一个关于如何设置干净的 URL 的指南([拉勒维尔](http://three.laravel.com/docs/install#cleaner-urls)、[菲尔康](http://docs.phalconphp.com/en/latest/reference/tutorial.html)、 [Symfony](http://symfony.com/doc/current/cookbook/configuration/web_server_configuration.html) 、 [Zend](https://zf2.readthedocs.org/en/latest/ref/project.structure.html?highlight=Caching) )以及任何不值得使用的指南——他们显然不关心现代实践。

### 4.抑制错误

我[在之前的一篇文章中写过这个](https://www.sitepoint.com/why-suppressing-notices-is-wrong/)，但是还是值得再提一下。每当你发现自己在使用@运算符时，请重新考虑，并从不同的角度更仔细地处理这个问题。请相信我的话，当我说围绕一个应用程序的功能的 20 行样板 cURL 代码比前面有@操作符的单行代码更好。

我通过个人实验发现，一个好的方法就是我在最初的帖子中提倡的方法——把你所有的注意变成致命的错误。确保*没有*被记录到错误日志中，因为实际上*没有什么要记录的*比在你的眼前举着@假装便便没有打到风扇要好。

我们最近报道了一些用于生产就绪 PHP 应用程序的 Heroku 附加组件，其中之一是优秀的 paper trail——一个可以让你将应用程序的所有错误推送到后端的附加组件，便于以后搜索、分组和删除；因此，即使确实发生了一些错误，最好让它们被记录下来，并通过修复代码来消除它们，而不是让它们沉默，在用户面前装聋作哑。

### 5.在条件中赋值

即使是有经验的开发人员有时也会出现口误，把`if ($condition == 'value') {`写成`if ($condition = 'value') {`。我们的手会打滑，我们的键盘不会记录按键，我们最终会从实际发生赋值的代码的另一部分粘贴——这种情况会发生，而且我们通常只有在运行应用程序时才会发现。

有几种方法可以完全避免这种情况:

1.  使用像样的 IDE。任何好的 IDE(比如 PhpStorm)在检测到“条件赋值”问题时都会发出警告。
2.  使用[【尤达条件】](https://en.wikipedia.org/wiki/Yoda%20conditions)。你会在许多流行的项目中看到这些，甚至是大型框架。通过颠倒比较(如，`if ('value' = $condition) {`)，较弱的 ide 也会注意到这个问题。有些人认为 Yoda 语法令人讨厌且毫无意义，是一条不应该存在的生命线(“对你的代码要更加小心，该死”)，但每个人都有自己的看法——如果它对某人有帮助，我完全支持它。如果我们都是精英主义者，WordPress 和 Zend Framework 就不会存在了。
3.  只要把它记在心里，你就会形成一种眼睛反射，每次写的时候都会检查一下。所需要的只是练习，但即使是最好的开发人员也会遇到这种情况，这就是 1。第二。派上用场了。

### 6.过于透明

说这话可能会引起一些争议，但不管怎样，还是要说。除非你对该框架的开发者有 100%的信心，或者不运行高利润、高流量的关键业务应用，否则你应该始终努力掩盖你的后端方式——如果发现该框架的安全漏洞，不公布你的应用基于哪个框架实际上有助于防止攻击。例如:

> 如果您使用 Symfony2 translator 并有一个带{_locale}参数的路由，请立即升级！[http://t.co/jihXHB8MzT](http://t.co/jihXHB8MzT)
> 
> ——2014 年 7 月 15 日，美国的耶利米((@ jderusse)[2014 年 7 月 15 日](https://twitter.com/jderusse/statuses/489160300743393280)

在这条推文中，一个严重的代码注入问题的知识正在向公共领域传播。如果你在工作，这是很好的，可以立即升级，没有 devops 的问题，并让团队先聚在一起，但对于大多数人和公司使用 Symfony，这并不是这样。即使 Symfony 可以通过 Composer 升级(正如 Ryan 在下面的评论中提到的)，但在多层环境的大型团队中，通常需要一段时间才能获得批准。所有使用这种翻译方法的网站都宣称是 Symfony 的用户。)因此在修复之前会暴露于此漏洞。

在上面的例子中使用 Symfony 仅仅是一个例子。这些年来，无数其他软件也出现了类似的情况。当我还在商业上使用 Zend Framework 时，我们也遇到了这种情况，并因此遭受了攻击。WordPress 也有安全失误的时候，我们知道他们支持的网站比例有多高。这些事情时有发生，有时，当处理承载公司大部分收入流的应用程序时，开源和透明并不是最好的方法。

### 7.不删除开发配置

最后但同样重要的是，应该提到开发配置移除。最近(这是一个诚实的巧合，我在这里再次提到 Symfony)，Cnet [由于没有删除他们的开发配置而遭受了攻击](http://www.cnet.com/news/cnet-attacked-by-russian-hacker-group/)。

> 嗯嗯不:[http://t.co/rAQis1ycWq](http://t.co/rAQis1ycWq)[#保安](https://twitter.com/hashtag/security?src=hash) [#symfony](https://twitter.com/hashtag/symfony?src=hash)
> 
> —马可·皮埃塔(@ Ocramius)[2014 年 7 月 15 日](https://twitter.com/Ocramius/statuses/489175503744811008)

Cnet 是世界上最大的科技新闻网站之一，它基于 Symfony。如您所知，Symfony 为您的应用程序提供了两个入口点:`app.php`和`app_dev.php`。通过将您的浏览器指向一个，您可以获得生产环境。通过指向带有`_dev`后缀的那个，你显然得到了开发版本，它具有调试器、敏感数据等等。这是好是坏是许多讨论的主题(再次感谢 Ryan 指出这一点)，但不可否认的是，它让一些笨拙的开发人员暴露于 Cnet 遭受的错误。此外，在`app_dev`上访问的任何其他网址都将被重定向到其他`app_dev`网址。换句话说，它不仅仅是在开发模式下启动的索引页面，而是整个网站——在 Cnet 的情况下，这是很大的访问量。

如果你关注 Twitter 上的讨论，它会很快变得令人悲伤——更令人悲伤的是，它本可以在一秒钟内避免:

1.  开发人员可能已经从生产服务器上删除了`app_dev.php`
2.  开发者可以让白名单中的 IP 访问`app_dev.php`，这是默认情况下的工作方式，除非你放松这些限制。

这两种方法都可以完全避免所有问题。请记住，在推向生产时，请确保您的开发配置要么完全不可访问，要么只能由一组列入白名单的 IP 访问。

## 结论

你觉得这份名单怎么样？是涵盖了常见的方面还是太深奥了？你是否有一些以上三篇文章都没有提到的常见陷阱？请在下面的评论中告诉我，如果你的建议是正确的，我们会更新帖子！

## 分享这篇文章