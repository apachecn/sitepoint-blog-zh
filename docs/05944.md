# 将您的想法升级到 Rails 4

> 原文：<https://www.sitepoint.com/upgrading-mind-rails-4/>

![rails4](img/2c999b09e688f84010a1209d9cebacc6.png)

如果你曾经花时间开发过 Rails，你肯定有过这样的经历:你在一个特定的版本中做项目工作，升级到最新最好的版本是不可能的。这种情况发生在我从事大量(当时)始于 Rails 3 时代的绿地项目时。当 Rails 4 发布时，团队决定不升级，因为升级没有提供价值。

我最近接触了一个 Rails 4.1 项目，并想分享一下我提升思维以使用 Rails 4 约定的经验。

## 设置

从 Rails 4 开始和以前一样简单。让我们看看当一个新项目生成时会发生什么。

```
$ rails new hello_rails4
```

### Gemfile

生成的 Gemfile 比您习惯的旧版本 Rails 要干净得多。团队和其他让它感觉杂乱的东西都不见了。

### 春天

一个有趣的补充是这一行:

```
# Spring speeds up development by keeping your application running in the background. Read more: https://github.com/jonleighton/spring
gem 'spring', group: :development
```

春天的宝石真的可以加速发展。例如，当运行 rake 任务时，它只需加载 Rails 环境一次，而以前这需要花费很长时间。我一直用[宙斯宝石](https://github.com/burke/zeus)做这个，但是弹簧宝石是内置的，感觉要轻得多。

要设置它，您需要做的就是运行这个命令

```
$ bundle exec spring binstub --all
```

并从*运行您的命令。/bin* 目录。如果你像我一样厌倦了详细说明”。/bin "对于这些命令，你可以安装 [direnv](https://github.com/zimbatm/direnv) (如果你在 Mac 上，通过自制软件)并将这一行添加到*。项目中的 envrc* 文件。

```
export PATH=$PWD/bin:$PATH
```

现在，你可以尝试运行`rake`。第一次你必须像往常一样等待，但随后的电话是即时的。

如果您在测试中使用 rspec 和 cucumber，您还需要将 *spring-commands-rspec* 和 *spring-commands-cucumber* 添加到您的 gem 文件中。

```
gem "spring-commands-rspec"
gem "spring-commands-cucumber"
```

要使它生效，您需要重新构建您的 binstubs。

```
$ bundle exec spring binstub --all
```

当您这样做时，您将体验到与运行其他 Rails 任务相同的运行测试的加速。

## 涡轮链

如果你喜欢好的争议，Turbolinks 是自资产管道以来最有争议的特性之一。就像资产管道一样，关闭它没什么大不了的，所以争议(再次)是没有根据的。

如果你决定使用 turbolinks，你可能会遇到一些问题。我知道我做了，我花了一段时间才弄明白。

### jQuery DOM 已加载

当使用 turbolinks 时，当页面改变时，您并不总是得到 jQuery“DOM loaded”事件。请记住，页面只加载一次，后续请求只刷新页面内容。

大多数应用程序使用这个事件来初始化它们的 javascript 处理程序。你会看到一些东西，比如初始化一个 fancybox。

`coffeescript
jQuery -> $('.fancybox').fancybox()` 

要解决这个问题，您需要绑定到两个事件。原始的 DOM loaded 事件和一个新的`page:load`事件。如果以这种方式初始化，大多数 javascript 应该可以正常工作。

`coffeescript
$(document).on 'ready page:load', -> $('.fancybox').fancybox()` 

### 问题

当你遇到 Rails 4.1 的问题时，通常是因为第三方库和 gems 没有更新，每次显示页面时都假定是一个干净的石板。

我在使用 [recaptcha gem](https://github.com/ambethia/recaptcha/) 时遇到了一个问题，当页面刷新时，验证码可以正常加载，但当点击页面的快速链接时，验证码根本不显示。通常，在这种情况下，你会发现一个[问题](https://github.com/ambethia/recaptcha/issues/83),库的作者正在讨论如何将它与 turbolinks 一起使用。在这种特殊情况下，表单助手必须启用 ajax:

`ruby
recaptcha_tags ajax: true` 

### 关掉它

当你遇到一个无法简单解决的问题时，你可以选择关闭单个页面的超链接。简单地给链接添加一个`data-no-turbolink`属性。该页面现在将通过标准请求加载。

要完全禁用 turbolinks，您必须:

*   从你的 gem 文件中删除`gem 'turbolinks'`
*   删除布局中的 javascript 和样式表标签
*   从 *application.js* 中移除`//= require turbolinks`

## Rails 4 配置和 Heroku

在 Rails 3 中，有很多方法可以执行应用程序配置。从简单但破碎的

```
if Rails.env.production
  Braintree::Configuration.environment = :production
  Braintree::Configuration.merchant_id = "merchant_id"
  Braintree::Configuration.public_key = "public_key"
  Braintree::Configuration.private_key = "private_key"
else
  Braintree::Configuration.environment = :sandbox
  Braintree::Configuration.merchant_id = "sandbox_merchant_id"
  Braintree::Configuration.public_key = "sandbox_public_key"
  Braintree::Configuration.private_key = "sandbox_private_key"
end
```

就像 Ryan Bates 在他的 railscast [#85 YAML 配置](http://railscasts.com/episodes/85-yaml-configuration-revised)中描述的那样。

这些似乎都不合适，尤其是当 Heroku 是您的部署平台时。Heroku 使用环境变量是一个很好的设计决策，但是我们是 Rails 开发人员，我们很懒。“好”的那种懒惰，所以我们决定使用[费加罗宝石](https://github.com/laserlemon/figaro)。

我不打算深入研究 gem 是如何工作的(关于它的文档很棒)。可以说，figaro gem 提供了两个世界的精华:

*   简单地将配置放在您控制的文件中，而不在源代码控制中检查它们。我们很懒，所以不再摆弄加载环境变量是一件好事。
*   在 Heroku 上部署和拥有真实环境变量的能力。

我本来可以继续在 Rails 4 中使用 figaro，但自从我看到 figaro gem 作者的这篇文章后，我就想使用 Rails 4.1 中的内置功能—*secrets . yml*。

在我看来，不必安装另一个 gem 来处理框架从一开始就应该包含的东西总是一个胜利。公平地说，内置功能并不是最丰富的特性。如果您正在手动配置应用服务器，复制 *secrets.yml* 文件可能是一个选项，但是如果您想要部署到 Heroku，您会遇到一个问题，因为 Heroku 使用环境变量。

希望环境变量问题将在不久的将来得到解决。同时，我将为 Rails 4.1 中的 *secrets.yml* 特性添加这个轻量级扩展:

```
gem 'heroku_secrets', github: 'alexpeattie/heroku_secrets'
```

要使用它，创建一个 *config/secrets.yml* 文件。注意，你可以使用[锚和引用语法](http://rails-bestpractices.com/posts/37-dry-your-database-yml)来稍微润色一下。

```
default: &default
  recaptcha_public_key: ""
  recaptcha_private_key: ""

development:
  <<: *default
  secret_key_base: ''

test:
  <<: *default
  secret_key_base: ''

production:
  <<: *default
  secret_key_base: ''
  google_analytics_code: ''

  smtp_username: 'admin@example.com'
  smtp_password: 'xxx'
  smtp_domain: 'example.com'
  smtp_address: 'smtp.example.com'
```

您可以让 gem 通过 rake 任务将您的生产配置复制到 Heroku，并自动选择变量:

```
$ rake heroku:secrets[heroku_app_name] RAILS_ENV=production
```

就是这样！现在您已经配置好了您的应用程序，可以使用 Rails 为您进行配置了

```
Braintree::Configuration.environment = Rails.application.secrets.braintree_environment
Braintree::Configuration.merchant_id = Rails.application.secrets.braintree_merchant_id
Braintree::Configuration.public_key = Rails.application.secrets.braintree_public_key
Braintree::Configuration.private_key = Rails.application.secrets.private_key
```

不要忘记将这个文件添加到您的*中。gitignore* 文件，因为 Rails 默认情况下还不这么做。

## 结论

Rails 4 比它的前身更容易升级。大多数变化都是以新特性的形式出现的，这些新特性(如果使用得当)会增强您作为开发人员的生活。如果某些东西不适合你的特殊情况，选择退出比以往任何时候都容易。

这篇文章总结了我在试图将我的思维升级到最新最伟大的时候所经历的事情。当您阅读文档时，您总是可以更深入地挖掘，但是，希望本文能给您一个好的开始。

## 分享这篇文章