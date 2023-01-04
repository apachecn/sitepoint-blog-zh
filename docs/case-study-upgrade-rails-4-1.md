# 案例研究:升级到 Rails 4.1

> 原文：<https://www.sitepoint.com/case-study-upgrade-rails-4-1/>

本文不是关于 Rails 4.1 的内容。这是关于我如何使用 3.2.18 到 4.1 来获得一个(尽管简单的)生产应用程序。如果你想要一篇“Rails 4/4.1 的新特性”的文章，互联网上到处都是，包括我们自己的 Michael Olah 写的一篇很棒的文章。

今天，我将列举我将一个 Rails 3.2.18 应用程序升级到 Rails 4.1 的步骤，尽可能使用任何新的 Rails 约定。我希望我的经历能给你的“我应该马上升级”或者“我会下地狱”的决定注入一点现实。

## 当前状态

正如我提到的，我正在升级的应用程序目前在 Ruby 2.0 和 Rails 3.2.18 上。这是一个相对普通的应用程序，使用 Devise 进行身份验证，使用 RSpec 和 Guard 进行测试。数据库是 PostgreSQL，它被部署到 Heroku。

我们开始吧，好吗？

## Gemfile

### 添加/更改

首先要做的是决定 Ruby 的版本。我想，既然我要去 Rails 4.1，我也可以去 Ruby 2.1。围绕 Ruby 2.1 出现了一些问题，其中一些已经在 2.1.2 中解决了。然而，[在 2.1.2 中仍然有一些不好的事情](http://www.omniref.com/blog/blog/2014/03/27/ruby-garbage-collection-still-not-ready-for-production/)，所以知道你正在进入什么，K？

另外，如果你像我一样使用 RVM 和/或 Pow，一定要更改任何`.ruby-version`和`.powrc`文件。当你指定了一个新版本后忘记更改`.powrc`时，Pow 就超时了，这消耗了我大约 30 分钟的地球时间。

我改了台词

```
rails '3.2.18'
ruby '2.0.0'
```

到

```
rails '4.1.0'
ruby '2.1.2'
```

现在是新的宝石。围绕 4.1 的大部分争论集中在 [Spring](https://github.com/rails/spring) 上，这是一个闪亮的应用预加载程序，旨在从我们的集体爬行中消除 Rails 启动时间。与任何与 Rails 相关的东西一样，将 Spring 添加到默认的 Rails 约定中会引起一些社区恐慌。首先，它不能在一些一流的实现上工作，比如 JRuby 或 Rubinius。它也不能在任何 Windows 平台上运行。当我第一次读到这一点时，我同意反对者的观点。为什么要添加对这么多用户来说没有功能的东西呢？

无论如何，我想我会尝试一下，看看它是否值得喧嚣。如果没有，我会写帖子谴责这是纯粹的邪恶。或者什么的。

将`gem 'spring' group: :development`添加到 gem 文件。

Rails 4.0 增加了[强参数](https://github.com/rails/strong_parameters)，这是一种将应用程序可以传递给模型的属性列入白名单的方法。我一直在使用类似的方法，根据需要简单地调整控制器中的参数。这意味着我不需要*让*使用强参数方法来启动和运行，但是随着时间的推移，我可以朝着这个方向努力。
然而，由于我现在没有为强参数修复我所有的模型/控制器，我不得不使用一些胶带作为 **protected_attributes** gem 的形式。

```
gem 'protected_attributes`
```

## 搬迁

我的 Gemfile 包括几个 gem(**activeuuid**和 **uuidtools** )，用于向我的应用程序添加 uuid 支持和实用程序。Rails 4.0 改进了 UUID 支持，所以我不再需要这些了。如果你在你的应用中需要一个 uuid 值，你现在可以使用`SecureRandom.uuid`。我还必须从任何使用 uuid 的模型中删除`include ActiveUUID::UUID`，现在 **activeuuid** 已经不存在了。

Gemfile 仍然在使用一个被 Rails 4.0 移除的`:assets`组，所以把这个`:assets`去掉。

最后，我长期以来一直是 mail catcher gem 的粉丝，但是 Rails 4.1 在核心产品中增加了 T2 动作邮件预览版。因此，我将删除**邮件捕捉器**，并用新的东西来扮演角色。我知道 **mailcatcher** 做的比 ActionMailer 预览版多一点，但是 gems 越少越好(除非它不是)。

好了，所有的 Gemfile 更改都完成了。时间到了，一切都会好的。

## 环境

对应用程序环境文件有一些必要的更改。首先，如果您在尝试启动服务器/控制台时得到一个关于删除`config.active_record.whitelist_attributes`的异常，那么您需要返回到 **Gemfile** 部分并添加 **protected_attributes** 。还有，让我重复自己的话，你真可耻。

在我成功的`bundle`之后，我看到了一些关于`config.eager_load`的警告。

```
config.eager_load is set to nil. Please update your config/environments/*.rb files accordingly:
  * development - set it to false
  * test - set it to false (unless you use a tool that preloads your test environment)
  * production - set it to true
```

因此，相应地更新这些文件。注意:如果您像我一样定义了一个“暂存”环境，请确保对该文件进行适当的更改。

在阅读 Rails 升级指南时，它提到了一个[新的 cookie 序列化器](http://guides.rubyonrails.org/upgrading_ruby_on_rails.html#cookies-serializer)。根据指南，我补充道

```
config.action_dispatch.cookies_serializer = :hybrid
```

到 **config/application.rb** 。这是另一个阅读指南的时刻。盲目地改变你的 cookie 序列化器会让你的 cookie 崩溃，所以要知道你为什么这么做。

## 路线

Rails 路由的关键字`match`现在已经过时了。如果在您的 **config/routes.rb** 文件中有`match`条路线，您将会看到类似这样的东西:

```
/Users/dood/.rvm/gems/ruby-2.1.2@app/gems/actionpack-4.1.0/lib/action_dispatch/routing/mapper.rb:196:in `normalize_conditions!': You should not use the `match` method in your router without specifying an HTTP method.
If you want to expose your action to both GET and POST, add `via: [:get, :post]` option.
If you want to expose your action to GET, use `get` in the router:
Instead of: match "controller#action"
Do: get "controller#action" (RuntimeError)
```

我有(令人震惊的)大量的`match`路线，现在都是`get` s。

## 秘密

我认为应用程序秘密的新方法应该有自己的章节。主题的[导轨部分值得一读。](http://guides.rubyonrails.org/upgrading_ruby_on_rails.html#config-secrets-yml)

### config/secrets.yml

首先，用以下内容创建一个`config/secrets.yml`文件:

```
development:
  secret_key_base: <output of rake secret>

test:
  secret_key_base: <output of rake secret>

production:
  secret_key_base: <output of rake secret>
```

有了我的新秘密文件，是时候删除`config/initializers/secret_token.rb`了。如果我做对了，我的 cookie 将会升级到新的更安全的 cookie。再次，确保你明白这里发生了什么，因为没有回头路。在升级之前，您可能需要确保您的所有用户都有新的 cookies，升级指南会告诉您如何根据需要支持这两者。

### 应用程序设置

我的应用程序目前使用 [**设置逻辑**](https://github.com/binarylogic/settingslogic) gem 来保存我的应用程序设置，我非常喜欢在几个应用程序上使用这些设置。有人信誓旦旦的说 [**费加罗**](https://github.com/laserlemon/figaro) ，我也能看出为什么。在这两种情况下， **settingslogic** 添加的内容刚好足够让我想保留它。因此，我的秘密文件将只保存`secret_key_base`

## 混合物

此时，我似乎已经有了一个准备运行的 Rails 4.1 应用程序。老实说，我原以为这更像是一笔交易。

我确实删除了**供应商/插件**目录。我收到一个警告，说插件似乎几代都不被支持。现在它已经消失了，还有另一个小小的 Rails 烦恼。

因为我将 **spring** 添加到了 Gemfile 中，所以我需要创建 binstubs 以便让 spring 高兴。

```
spring binstub --all
```

它吐出了:

```
* bin/rake: generated with spring
  * bin/rails: spring inserted
```

## 部署

我要被部署到 Heroku，所以应该很容易，对吧？然而，当 Rails 4.0 推出时，我试图在 Heroku 上部署一个 Rails 4 应用程序，而资产管道却有其他想法。下面的图片是我试图让它工作时的感受:

![Some combination of switches here will successfully deploy your assets to Heroku. Good luck.](img/10b582aa4a541abfc24907b5a3bac9a1.png)

这里的一些开关组合将成功地将您的资产部署到 Heroku。祝你好运。

谢天谢地，时间已经做了时间会做的事情，事情有了很大的改善。[这篇文章](https://devcenter.heroku.com/articles/rails-4-asset-pipeline)涵盖了我所做的改变。基本上，我将 **rails_12factor** gem 添加到我的包中，然后确保它在我的**config/environments/staging . Rb**文件中。

```
config.serve_static_assets = true
```

我认为两者都做是矫枉过正，但它的工作，所以我慢慢后退。如果你的资产(包括 **application.css** 和 **application.js** )是 404ing，我这里说的应该可以搞定。

## 印象

升级后的 Rails 还是 Rails。如果你还记得，我提到过我对整个 Spring 的事情有点焦虑，所以让我们启动一个 rails 控制台，看看我是否注意到启动时间有什么变化。

*(大约 5 秒后)*

不，看起来是一样的。我会关闭控制台并打开另一个。

*(*

*(嘴巴半开)*

那不可能是对的。我从未见过 Rails 控制台启动如此之快。

(再做一次，同样的结果。)

(感觉更轻，但有点脏，因为所有关于春天不适合每个人的信念都离开了身体和心灵。)

哇哦。春天带来了巨大的不同。我迫不及待地想看看它能为 RSpec 做些什么。

*(点火`guard` )*
*(运行几次规格)*

嗯。似乎对 RSpec 没什么作用。我已经重新启动了守卫，但规格有相同的启动时间。

*(向谷歌求教，发现答案)*

好了，有一个[**spring _ commands _ rspec**](https://github.com/jonleighton/spring-commands-rspec)添加了必要的 binstub。我`bundle`在那个宝石里，然后`spring binstub --rspec`添加 binstub，接着`spring stop`确定 Spring 停下来闻闻新的 binstub。不幸的是，我们还没有完成。正如我在文章开头提到的，我使用 Guard。出于某种原因，生成的`rspec` binstub 没有被 Guard(或者任何东西，看起来)拾取，所以你必须修改你的`Guardfile`。我变了

```
guard :rspec do
```

到

```
guard :rspec, cmd: 'bin/rspec' do
```

并重启`guard`。卡巴拉米。我的天赋现在在每次保存前开始。

[//www.youtube.com/embed/28GLa9T2CtI](//www.youtube.com/embed/28GLa9T2CtI)

我得到了春天。对不起 JRuby(和其他)用户。如果这能让你感觉好点的话，我的几个更大的 Rails 应用程序是 JRuby，所以我能感受到你的痛苦。

## 现在呢？

嗯，我的应用程序升级到了 Rails 4.1，并将于本周在 staging 开始它的生命。太好了。在很大程度上，升级是不痛不痒的，收益也是实实在在的。就像我喜欢一样。

### 后续步骤

接下来的步骤包括查看我的应用程序的代码库还有哪些新的有趣的部分。比如:

*   **枚举**:我看不出这里有多大价值。只有当你的数据类型是整数时，枚举才有意义，比如一个标志。否则，当您查看数据库中的数据时，您必须记住什么数字映射到什么值。我可能就是不明白。
*   消息验证者:我们的设计邮件发送者通过忘记密码和注册确认来完成这些工作。我想知道 Plataformatec 的孩子们会不会用这个？
*   [**关于**](https://github.com/basecamp/concerning) :这是一种“低礼之道，以隔小忧”很有意思，但不适合这个应用。也许我的一些庞然大物。
*   **变体**:这个应用程序没有响应，超出了 Foundation 提供的范围，我们在实现方面做得很差。我们需要在这方面做些工作，所以我敢打赌，我们很快就会在这里寻找。
*   [**新的测试方法**](http://brandonhilkert.com/blog/rails-4-1-travel-to-test-helper/) :一些新的测试方法看起来很简洁，可能会让我们倾倒更多的宝石。宝石倾销，FTW！

你已经升级到 Rails 4.1 了吗？印象？想法？痛苦？分享一下你的经验！

## 分享这篇文章