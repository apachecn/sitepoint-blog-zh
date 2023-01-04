# PHP 5.6 测试版结束

> 原文：<https://www.sitepoint.com/php-5-6-end-beta/>

2014 年 6 月 5 日，PHP 团队[宣布了](http://www.php.net/archive/2014.php#id2014-06-05-1)5.6 版本的第四个也是最后一个测试版。这一里程碑结束了 beta 计划，开始了 RC 计划(目前计划在 6 月 19 日)，从现在开始，该计划将专注于确保版本足够稳定以供发布。按照测试版程序的惯例，自测试版 1 以来，没有添加任何新功能——所有版本都是错误修复和改进补丁。

## 变化

大约五个月前，我们[发布了一个即将到来的 5.6 新特性的列表](https://www.sitepoint.com/new-features-php-5-6/)，从那时起，PHP 团队创建了一个比在神秘的变更日志中可以看到的更容易理解的变更摘要。该列表也称为 5.6 迁移指南，是一项非常有用的工作，包含了每个重要新功能的简单演示——你可以在这里[查看](http://docs.php.net/manual/en/migration56.new-features.php)。

如果你有兴趣通过测试版看到所有的错误修复，你可以[阅读 Github 上的 changelog](https://github.com/php/php-src/blob/php-5.6.0beta4/NEWS) 。

我们在最初的帖子中没有提到，但在此期间已经添加的内容有:

### [求幂运算](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.exponentiation)

简而言之，[“通过**求幂”](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.exponentiation)这是一个让你轻松计算一个数的另一个数的幂的运算符。然而，执行的顺序对某些人来说可能是反直觉的。该示例声明:

```
$a = 2**3; // $a = 8, i.e. 2^3
$b = 2**3**2; // $b is 512
```

大多数不熟悉指数运算符的人会认为第二行分组运算是这样的:

```
(2**3)**2
```

这将归结为 64，而不是 512。但是，这里的指数运算符使用以下首选项:

```
2**(3**2)
```

归结到`2**9`，就是 512。因此，**指数运算符从右向左执行**，其他语言也是如此(比如 Python)。

### [默认 UTF-8](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.default-encoding)

默认字符编码设置为 UTF-8，并使用 [`default_charset`](http://docs.php.net/manual/en/ini.core.php#ini.default-charset) ini 设置/功能。

在一个拥有超广泛受众的时代，拥有 UTF-8 的支持是至关重要的。默认为 UTF-8 跳过了开发人员以前必须使用 mbstring 和 iconv(现在已被否决)完成的几个步骤，以便不仅支持读取和写入准确的 UTF-8 数据库数据(您仍然需要为 UTF-8 配置您的数据库，因为它与 PHP 没有任何关系)，而且能够在具有默认本地字符集的系统上编码，并在浏览器中正确显示网页。有了这个，不再需要在 ini 设置中设置`default_charset`——它在你安装 PHP 的时候就为你设置好了。

### [php://input 可重用](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.reusable-input)

在上一篇文章中我们没有提到的一个细节是 php://input 现在是可重用的——与[文档页面](http://docs.php.net/manual/en/wrappers.php.php#wrappers.php.input)所说的相反。这意味着您现在可以多次读取它，并且它总是从相同的数据中读取，这导致了[显著的内存使用减少](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.reusable-input)。

### [GMP 操作员超载](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.gmp)

GMP，一个我们在之前简单介绍过[的库，现在](https://www.sitepoint.com/arbitrary-precision-big-numbers-php/)[支持操作符重载](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.gmp)。以前，您必须使用 gmp_*函数，例如，将两个 gmp 对象/资源加在一起(即`gmp_add`)–现在，您可以使用更简单的表达式，如`+`，就像您使用常规数字一样。

我们正在寻找深入的 GMP 教程和真实世界的使用案例，因此如果您愿意投稿，[请告诉我们](http://gplus.to/sitepointphp)。

### 安全升级

添加了函数 [hash_equals()](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.hash-equals) ，引入了 [gost-crypto](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.gost) 哈希算法，实现了[各种 SSL/TLS 增强](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.openssl)。

我们不会详细讨论这些——它们都超出了这个快速概述的范围，并且足够复杂，足以保证它们有自己的帖子(愿意写吗？[让我知道](https://google.com/+BrunoSkvorc))，但是你可以在他们各自的链接上阅读详细内容。

### [pgsql 异步](http://docs.php.net/manual/en/migration56.new-features.php#migration56.new-features.postgresql)

[Pgsql](http://docs.php.net/manual/en/book.pgsql.php) ，PHP 与 PostgreSQL 数据库通信的扩展，现在支持异步操作，[在这方面加入了 MySQL](http://www.php.net/manual/en/mysqli.reap-async-query.php) 。异步可能并不完全是人们所认为的，所以在你相信这些操作之前，需要做一些实验，但是很高兴知道它仍然存在。

您可以在测试中看到异步使用 PostgreSQL】的完整示例。

## 其他变化

这些新功能并不是你应该记住的唯一事情——新文档有几个非常方便的部分可以帮助你正确过渡。

### BC 中断

[向后不兼容的变化](http://docs.php.net/manual/en/migration56.incompatible.php)列表将告诉你所有你的旧应用程序将会失败的地方，如果使用的方式和他们在 5.6 版本之前使用的方式一样。其中一些是:

*   GMP 资源现在是对象，这将打破`is_resource`以前的用法
*   mcrypt 需要有效的密钥和 iv
*   json_decode 对“真”、“假”和“空”的大小写更严格
*   [还有更](http://docs.php.net/manual/en/migration56.incompatible.php)

### 不推荐使用的功能

[弃用的特性](http://docs.php.net/manual/en/migration56.deprecated.php)页面列出了现在会在代码中触发 E_DEPRECATED 错误的所有特性。在这个版本中，它们是来自不兼容上下文的调用，`$HTTP_RAW_POST_DATA`和`always_populate_raw_post_data`，但是人们也应该注意以前被否决的特性——最值得注意的是`mysql`扩展的`mysql_*`函数，它现在被正式否决了，不应该被使用。

### 改变的功能

“已更改的函数”列表将描述与先前版本中的迭代相比，某些函数发生了哪些更改。最值得注意的是，它们是`crypt`函数，如果你省略了 salt 参数，它会引起注意，`mcrypt_create_iv()`的源现在默认为`MCRYPT_DEV_URANDOM`，一些 PostgreSQL 函数不再是实验性的，[和更多的](http://docs.php.net/manual/en/migration56.changed-functions.php)。

### 新功能

与新特性页面相似，[新功能](http://docs.php.net/manual/en/migration56.new-functions.php)页面列出了您可以使用的所有新核心和扩展功能。例如，我们在上一篇[新特性](https://www.sitepoint.com/new-features-php-5-6/)文章中提到的为 Zip 存档设置密码的功能就在这一页中提到。

### OpenSSL 变化

在这个版本中，OpenSSL 的变化是如此之多，以至于它们保证了自己的独立页面:[http://docs.php.net/manual/en/migration56.openssl.php](http://docs.php.net/manual/en/migration56.openssl.php)

该页面包含从证书和指纹更改说明和解释到 SPKI 等所有内容。如果你是一个安全爱好者，我们鼓励你通读这篇文章，在你通读之后，我们鼓励你为我们写一篇深入的解释:)[如果你能以适合更广泛受众的方式让更多人了解这些知识，请告诉我们](https://google.com/+BrunoSkvorc)。

### 其他变更和全局常数

最后两页展示了扩展中不适合任何其他类别的其他[变化，以及可用的](http://docs.php.net/manual/en/migration56.extensions.php)[新全局常数](http://docs.php.net/manual/en/migration56.constants.php)的详尽列表——尽管没有任何详细解释。

## 结论

如果你要从 PHP 以前的任何一个版本迁移到一个新版本，请注意所有这些迁移指南:[http://docs.php.net/manual/en/appendices.php](http://docs.php.net/manual/en/appendices.php)

这些指南将涵盖您应该了解的所有主要和次要版本差异，并帮助您为可能切换到最新和最好的版本做好准备。

5.6 测试版的结束。标志着 PHP 社区中一个新的重要里程碑。当我们过渡到这个新版本时，几个重要的新功能等待着我们，核心开发人员鼓励每个人测试这个版本，并就他们可能遇到的任何问题报告反馈。接下来的几个月将会专门处理发布候选版本，下一个版本(预定在 6 月 19 日)，可以有把握地说你可以开始测试 5.6 了。生产中。同时，下载测试版并开始测试！

## 分享这篇文章