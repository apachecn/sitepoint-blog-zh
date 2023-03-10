# 从 PHP 跳到 Go:亵渎、虚张声势还是常识？

> 原文：<https://www.sitepoint.com/moving-php-go/>

*这篇关于从 PHP 迁移到 Go 的文章最初发表于[别处](https://dannyvankooten.com/laravel-to-golang/)，经作者许可后在此转载。*

* * *

![A gopher chasing an elephpant away](img/3ae8fd6a13b6fa0a623abf74f54e56d9.png)

今年早些时候，我做了一个可以说是糟糕的商业决策。我决定在 [Go](https://golang.org/) 中重写 [Laravel](https://www.sitepoint.com/premium/courses/laravel-5-2930?aref=bskvorc) 驱动[bozzilla](https://platform.boxzillaplugin.com/)的应用程序。

但不后悔。

![Boxzilla platform page](img/3d92678114c88c22c4ec98f41f1f412a.png)

仅仅几周之后，我就开始部署 Go 应用程序。构建它是我几个月来最有趣的事情，我学到了很多，最终结果是对旧应用程序的巨大改进。更好的性能、更容易的部署和更高的测试覆盖率。

该应用程序是一个相当简单的数据库驱动的 API & account area，用户可以登录下载产品，查看他们的发票或更新他们的支付方式。

[Stripe](https://stripe.com/) 和 [Braintree](https://www.braintreepayments.com/) 用于接受订阅付费。发票使用[钱鸟](https://www.moneybird.com/)处理，一些交易邮件使用[邮件枪](https://www.mailgun.com/)发送。

虽然 Laravel 在这方面做得足够好，但有些事情对我来说总是感觉过于复杂。每隔几个月发布一个新的“主要”版本是怎么回事？如果新版本包含重大改进，我会觉得很好，但很多时候对我来说，这只是微小的命名和目录结构变化。

## 为什么要去？

去年我一直在迁移一些服务，所以我对这门语言并不完全陌生。作为一名销售基于 WordPress 产品的开发人员，我工作的一部分是在一个古老的技术栈中工作，这个栈主要关注终端用户。

如果我不是自由职业者，我会简单地申请一份新工作来弥补这种性感技术的缺乏。作为自己的老板，我应该让自己的日常工作变得有趣，而不仅仅是追逐眼前的美元。如果收入允许(确实如此)，为什么不找点乐子呢？

编写 Go 代码是一种乐趣，工具非常棒，不仅开发速度快，而且最终结果通常也快得惊人。仅仅是阅读关于 Go 项目的目的，就让我对这门语言产生了兴趣。

我认为在接下来的几年里，我们会看到很多人从 PHP、Python 和 JavaScript 等动态类型语言转向其他语言。

## 移植代码库

将代码迁移到 Golang 主要是为了获得正确的数据库交互&将刀片模板移植到我们可以在 Go 中使用的地方。

ORM 是一个总是以妨碍我的方式结束的东西，所以我选择了一个可模仿的数据访问层和简单的 SQL 查询。 [Meddler](https://github.com/russross/meddler) 用于清除一些样板文件，以便将查询结果扫描到结构中。

为了支持分层模板和部分模板，我开源了一个包裹 Go 标准 html/模板包的小包装器 [grender](https://github.com/dannyvankooten/grender) 。这允许我相对容易地移植刀片模板文件，因为我可以使用相同的层次结构和部分模板。

为了与 Stripe 集成，有官方的 [stripe-go](https://github.com/stripe/stripe-go) 包。对于布伦特里来说，有一个非官方的[布伦特里-go](https://github.com/lionelbarrow/braintree-go) 包，它被忽视了一段时间，但最近得到了新的关注。由于 Moneybird 中还没有管理发票的 Go 包，所以我构建并开源了 [moneybird-go](https://github.com/dannyvankooten/moneybird-go) 。

## 比较苹果和橘子

由于 Go 是一种编译语言，具有比 PHP 更好的标准库，所以像我将要做的那样比较这两种语言是不公平的。也就是说，我认为分享一些数字会很有趣。

### 表演

wrk 用于为两个应用程序执行一些简单的 HTTP 基准测试，为登录页面返回 HTML。

|  | 并发 | 平均值。潜伏 | 请求/秒 | 传输/秒 |
| --- | --- | --- | --- | --- |
| 拉勒韦尔 | one | 3.87 毫秒 | Two hundred and sixty-one point four eight | 1.27MB |
| 拉勒韦尔 | One hundred | 108.86 毫秒 | Nine hundred and seventeen point two seven | 6.04 兆字节 |
| 去 | one | 325.72μs | Seven thousand three hundred and sixty-five point four eight | 3427 兆字节 |
| 去 | One hundred | 11.63 毫秒 | Nineteen thousand nine hundred and sixty-seven point three one | 92.91 兆字节 |
| 去 | Two hundred | 37.68 毫秒 | Twenty-two thousand six hundred and fifty-three point two two | 105.41 兆字节 |

不幸的是，一旦我将并发“用户”的数量增加到 100 个以上，Laravel 应用程序(或 PHP-FPM 套接字)就会崩溃。

[NetData](https://my-netdata.io/) 提供了下面的图表，看看服务器在所有这些负载下是如何支撑的。

**有 100 个并发连接的 Golang**
![Go with 100 concurrent connections](img/56aa64c4f6a42bb01e2d39a56b13ada7.png)

**具有 100 个并发连接数的 Laravel**
![Laravel with 100 concurrent connections](img/2d197e41519aee5f68790fd1c08a6f4d.png)

请注意，我是在运行应用程序的同一台机器上运行基准测试的，因此这对两个图都有很大影响。

### 代码行

让我们比较两个应用程序中的代码行，包括所有的供应商依赖项。

```
find . -name '*.php' | xargs wc -l
156289 total 
```

Laravel 版本包含 156，000 多行代码。这还不包括开发依赖，Laravel 需要开发依赖来运行测试等。

```
find . -name '*.go' | xargs wc -l
33624 total 
```

另一方面，Golang 版本包含 33000 行代码。对于完全相同的功能，这只是五分之一的代码。

让我们排除 Laravel 应用程序中的外部依赖，这样我们就知道我实际上写了多少行。

```
find . -name '*.php' -not -path "./vendor/*" | xargs wc -l
13921 total 
```

对于 Golang 来说。

```
find . -name '*.go' -not -path "./vendor/*" | xargs wc -l
6750 total 
```

如果只看托管代码行，结果会稍微多一些。尽管如此，它仍然是完全相同的应用程序，只使用了一半的代码。

#### 测试覆盖率

在 Golang 中，测试是一等公民，测试文件就在实际的源文件旁边。

```
license.go
license_test.go
subscription.go
subscription_test.go 
```

这使得应用测试驱动开发非常方便。

在我们的 Laravel 应用程序中，我们主要通过集成测试来检查请求处理程序是否返回了正确的响应。总体测试覆盖率很低，主要是因为紧耦合，而紧耦合又是我的错。第二次编写相同的应用程序也很有帮助。

## TLDR

我做了一件你永远不应该做的事情:用不同的语言重写了一个应用程序，因为我喜欢它。得到了很多乐趣，并得到了一个更小更快的应用程序作为回报。

## 分享这篇文章