# 增强您的 Rails 开发工作流程

> 原文：<https://www.sitepoint.com/boosting-your-rails-development-workflow/>

![rails](img/36ba165de235da6747d51b7b251c95e5.png)
本文主要面向不熟悉 Rails 的人。大多数成熟的 Rails 开发人员已经有了自己钟爱的工作流。如果您是新手，或者需要提高 Rails 的日常工作效率，请继续阅读！

让我们探索一些有助于您更快更好地完成工作的宝石和脚本。

> 关于为 OS X 和 Windows 机器安装 Ruby 和 Rails 的一个快速提示:试试 [railsinstaller](http://railsinstaller.org) 。它将在几分钟内让您轻松启动并运行。

## 你必须安装的宝石

在开始使用 Rails 之前，您必须安装以下五个脚本/gem。你别无选择，对不起！:)

1.oh_my_zsh 脚本
2。笔记本脚本
3。厉害了 _ 打印宝石
4。宙斯宝石
5。守卫-肝脏装载宝石

### 1.哦 _my_zsh

在安装 **oh_my_zsh** 之前，你应该已经安装了 [zsh](http://www.zsh.org/) 。 **oh_my_zsh** 与众不同的是它的[主题](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes)很酷而且很容易定制，[插件](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugins)和[自动完成](https://github.com/robbyrussell/oh-my-zsh/wiki/Cheatsheet)助手将会节省你大量的时间。你还会注意到 **oh-my-zsh** 要求你定期安装更新，这是一件好事。

#### 安装

正如我提到的，您应该已经安装了 zsh shell。然后，运行这个命令使其成为默认 shell: `chsh -s /bin/zsh`

运行下面的代码下载并安装它:

```
curl -L https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh | sh
```

#### 使用

要更改主题，在文本编辑器中打开`~/.zshrc`并将`ZSH_THEME`的值设置为主题的名称。这是[所有可用主题](https://github.com/robbyrussell/oh-my-zsh/wiki/themes)的列表。你应该试开几辆，找到一辆能用的。此外，如果你做演示，你可能想找一个在投影仪上看起来不错的(提示:黑底白字不好……)

要添加/删除插件，打开`~/.zshrc`文件，从`plugins`数组中添加/删除插件名称。这是所有可用插件的列表。你可以在`~/.oh-my-zsh`下找到 **oh-my-zsh** 的完整源代码。

### 2.[笔记本电脑](https://github.com/thoughtbot/laptop)

该脚本仅适用于 OS X 机器。引用来自[笔记本电脑](https://github.com/thoughtbot/laptop#laptop) github 页面:

> Laptop 是一个为 Rails 开发设置 Mac OS X 笔记本的脚本。

要安装这个脚本，您必须安装 gcc，将 zsh 设置为您的默认 shell(我们已经完成了)，然后运行下面的代码行:

```
zsh <(curl -s https://raw.github.com/thoughtbot/laptop/master/mac)
```

这个[链接](https://github.com/thoughtbot/laptop#what-it-sets-up)列出了所有将要安装的东西。我们将重点介绍其中的几个:
–[Heroku tool belt](https://toolbelt.heroku.com/)，我们需要使用 Heroku 上的应用
–home brew the loved package manager。你可以通过[http://braumeister.org/](http://braumeister.org/)T8—[RVM](http://rvm.io)在线浏览软件包，如果你用过 [railsinstaller](http://railsinstaller.org) 的话，这个软件包已经安装好了

### 3.[牛逼 _ 打印](https://github.com/michaeldv/awesome_print)

当您第一次在 rails 控制台中运行任何代码时，您可能会想到的第一个想法是“怎么会有人能读懂这个呢？!"你是对的，这真的很难，尤其是如果结果很复杂或者你已经检索了大量的对象。来了 **awesome_print** 力挽狂澜。

#### 安装

要安装 **awesome_print** ,运行以下命令:

```
gem install awesome_print
```

要在 rails 上使用它，你必须把它添加到你的 Gemfile 中，然后`bundle`

```
group :development do
  gem 'awesome_print'
end
```

#### 使用

如果你想让 **awesome_print** 在 irb 或控制台中默认运行，在这里添加两行到你的`.irbrc`所示的[。否则，您可以在控制台中将该行加上`ap`](https://github.com/michaeldv/awesome_print#usage)

```
require "awesome_print"
ap object, options = {}
```

#### 例子

这是一个 **awesome_print** 输出的例子:

```
# {

               :id => 1,
          :user_id => 5,
      :assigned_to => 7,
             :name => "Hayes-DuBuque",
           :access => "Public",
          :website => "http://www.hayesdubuque.com",
  :toll_free_phone => "1-800-932-6571",
            :phone => "(111)549-5002",
              :fax => "(349)415-2266",
       :deleted_at => nil,
       :created_at => Sat, 06 Mar 2010 09:46:10 UTC +00:00,
       :updated_at => Sat, 06 Mar 2010 16:33:10 UTC +00:00,
            :email => "info@hayesdubuque.com",
  :background_info => nil
}
```

### 4.[宙斯](https://github.com/burke/zeus)

当你在做一个 Rails 项目时，需要重启你的服务器，运行各种命令，比如`console`、`rake`、`generate`、`rspec`和`test`。这可能需要一点时间，特别是当你的项目增长，类和测试的数量增加的时候。这就是为什么你需要 **zeus** ，它会预装你的 Rails 应用，这样你所有的任务都会运行得更快。

#### 安装

要安装**宙斯**运行这个命令，或者你可以把它添加到你的 gem 文件中，然后`bundle`

```
gem install zeus
```

#### 使用

您所要做的就是导航到您的 Rails 项目并运行`zeus start`

zeus 需要几秒钟启动，然后它会列出所有可用的命令。您需要打开一个新的 shell 窗口并运行其他命令，例如

```
zeus s # to start rails server
zeus c # to start rails console
zeus test # to run tests
zeus generate model  # go generate modle
```

### 5.[警卫-肝负荷](https://github.com/guard/guard-livereload)

**[Guard](https://github.com/guard/guard)** 是一个可以轻松处理文件系统修改事件的工具。 **guard-livereload** 是[一长串宝石](https://github.com/guard/guard/wiki/List-of-available-Guards)中的一个，它依赖于 **Guard** ，但是是为特定种类的文件结构和应用定制的。例如， [guard-rspec](https://github.com/guard/guard-rspec) 监控 rspec 文件，并在它们改变时启动 rspec 来运行规范。

guard-livereload 做了类似的工作，当 Rails 视图发生变化时重新加载它。这就像从你的浏览器开发工具中改变它们一样，所以你会看到你的视图随着你的改变而改变。

#### 安装

你必须先安装**守卫**宝石，然后通过运行这个
`gem install guard` `gem install guard-livereload`来安装**守卫肝**

或者您可以将它们添加到您的 gem 文件中:

```
group :development do
  gem 'guard'
  gem 'guard-livereload'
end
```

然后运行这个，这将创建包含 guard 配置的`Guardfile`:

```
bundle
guard init
guard init livereload
```

安装 **guard** 的最后一部分是给你的 gem 文件添加一些宝石。这是为了[高效的文件系统处理](https://github.com/guard/guard#efficient-filesystem-handling)，根据您的操作系统，您可能只需要添加其中一个。

```
group :development do
  gem 'rb-inotify', :require => false # for OS X
  gem 'rb-fsevent', :require => false # for Linux
  gem 'rb-fchange', :require => false # for Windows
end
```

对于**守卫-肝脏加载**，你需要安装**机架-肝脏加载**宝石来在浏览器中实时查看你的变化:
`gem install rack-livereload`

并将这一行添加到`config/environments/development.rb`

```
MyApp::Application.configure do
  config.middleware.insert_after(ActionDispatch::Static, Rack::LiveReload)
end
```

## 排除故障

1.better_errors 宝石
2。导轨 _ 面板宝石
3。六分仪宝石
4。安静 _ 资产宝石

### 1.[更好 _ 错误](https://github.com/charliesome/better_errors)

**better_errors** 用一个更好的页面替换了标准的 Rails 错误页面。它甚至在浏览器中添加了一个精彩的 REPL 直播功能！这是一个相对较新的东西，但是开发者们很快就掌握了它，因为[默认错误页面和 better_errors 之间的差异](https://github.com/charliesome/better_errors#features)是巨大的。

#### 安装

将宝石添加到您的宝石档案中。如果你想使用现场 REPL，你需要添加**绑定调用**宝石，然后`bundle`

```
group :development do
  gem "better_errors"
  gem "binding_of_caller"
end
```

#### 使用

任何时候你的 Rails 应用程序抛出一个错误，你都会看到一个更好的错误页面。在该页面中，您将找到完整的堆栈跟踪以及与请求相关的所有局部和全局变量。如果有异步请求，可以通过导航到“0.0.0.0:3000/__better_errors”来跟踪上一个错误。您甚至可以使用 [BetterErrors.editor](http://rubydoc.info/github/charliesome/better_errors/master/BetterErrors.editor=) 方法配置 gem 在您的编辑器中打开源代码。

### 2.[轨道 _ 面板](https://github.com/dejan/rails_panel)

这个 gem 需要一个特定的 Google Chrome 扩展。宝石是`meta_request`，镀铬延伸是`rails_panel`。

> RailsPanel 是一个针对 Rails 开发的 Chrome 扩展，它将结束您对 development.log 的跟踪。在浏览器中——在开发者工具面板中——拥有关于您的 Rails 应用请求的所有信息。提供对数据库/渲染/总时间、参数列表、渲染视图等的洞察。

#### 安装

将宝石添加到您的宝石文件中，然后`bundle`。此外，这里的[谷歌浏览器扩展](https://chrome.google.com/webstore/detail/railspanel/gjpfobpafnhjhbajcjgccbbdofdckggg)

```
group :development do
  gem 'meta_request', '0.2.1'
end
```

### 3.[六分仪](https://github.com/schneems/sextant)

这个宝石在`0.0.0.0:3000/rails/info/routes`下被并入了 rails 4.0。然而，如果你正在开发一个 rails 3.2+应用程序，你必须安装 gem。

#### 安装

将宝石添加到您的宝石文件中，然后`bundle`

```
group :development do
  gem 'sextant'
end
```

#### 使用

导航到`0.0.0.0:3000/rails/routes`，您将在浏览器中看到命令`rake routes`的输出。

### 4.[安静 _ 资产](https://github.com/evrone/quiet_assets)

当服务器记录所有资产请求时，跟踪服务器日志有点困难。大多数时候，您并不关心这些请求，所以这个 gem 隐藏了资产日志。这极大地整理了日志，让您专注于重要的请求。

#### 安装

将宝石添加到您的宝石文件中，然后`bundle`

```
group :development do
  gem 'quiet_assets'
end
```

## 前端和 JavaScript MVC 框架

这一节将概述封装了一些框架并通过 Rails 资产管道为它们服务的 gem。我不会涉及每个框架的细节或者如何使用它们或者哪个更好。我想把重点放在这些宝石如何帮助 Rails 中的东西真正快速地结合在一起。

1.twitter_bootstrap gem
2。主干-轨道

### 1. [twitter_bootstrap](https://github.com/seyhunak/twitter-bootstrap-rails)

这不是 twitter bootstrap 维护者的官方宝石，但它是一个受欢迎的宝石，它工作得很好。

> Bootstrap 是 Twitter 的一个工具包，旨在 kickstart 开发 web 应用程序和网站。它包括用于排版、表单、按钮、表格、网格、导航等的基本 CSS 和 HTML。

#### 安装

将宝石添加到您的宝石文件中，然后`bundle`

```
group :development do
  gem "twitter-bootstrap-rails"
end
```

要将引导程序安装到您的项目中，请运行
`rails generate bootstrap:install static`

#### 使用

添加 bootstrap 的布局:
`rails g bootstrap:layout application fixed`

您还可以通过运行以下命令向特定资源添加主题:

```
rails g scaffold Post title:string description:text
rake db:migrate
rails g bootstrap:themed Posts
```

### 2.[主干轨道](https://github.com/codebrew/backbone-rails)

这个 gem 帮助您在 Rails 项目中设置 backbone.js。它创建了 backbone.js 文件结构，并通过资产管道为库提供服务。

> Backbone.js 通过提供具有键值绑定和自定义事件的模型、具有丰富的可枚举函数 API 的集合、具有声明性事件处理的视图，为 web 应用程序提供了结构，并通过 RESTful JSON 接口将其连接到您现有的 API。

#### 安装

将宝石添加到您的宝石文件中，然后`bundle`

```
group :development do
  gem "rails-backbone"
end
```

然后运行引导程序:
`rails g backbone:install`

#### 使用

gem 提供了三个生成器来帮助开始使用 backbone.js

##### 模型生成器

`rails g backbone:model`

##### 路由器生成器

`rails g backbone:router`

##### 脚手架发电机

`rails g backbone:scaffold`

## 认证和管理

您的大多数应用程序将需要身份验证，其中一些可能需要管理。我们都知道这些任务是多么重要和敏感，所以最好使用经过良好测试的 gems 来处理它们。

1.设计宝石
2。active_admin gem
3。Rails 作曲家

### 1.[设计](https://github.com/plataformatec/devise)

**device**是 Rails 社区中一个非常流行的宝石，它真的不需要介绍。

#### 安装

将宝石添加到您的宝石文件中，然后`bundle`

```
group :development do
  gem "devise"
end
```

要在您的 Rails 项目中安装**设计**:
`rails generate devise:install`

#### 使用

使用适当的模型名称运行以下命令来生成一个设备模型。该模型将具有 devise 提供的所有身份验证功能。
`rails generate devise MODEL`
您应该在任何您希望仅由经过身份验证的用户访问的控制器中添加此行，或者将其添加到您的`ApplicationContrller`中，以要求在您的整个应用中进行身份验证:
`before_filter :authenticate_user!`

### 3. [active_admin](https://github.com/gregbell/active_admin)

active_admin 是最好的管理工具之一，拥有非常好的用户界面和[文档](http://activeadmin.info/documentation.html)。这是一个使用**活动管理**的演示网站

> Active Admin 是一个创建管理风格界面的框架。它抽象了常见的业务应用程序模式，使开发人员可以轻松实现漂亮优雅的界面。

#### 安装

将宝石添加到您的宝石文件中，然后`bundle`。如果您运行的是 Rails 3.1 或更高版本，您还需要添加一些其他的东西:

```
gem "activeadmin"
gem 'sass-rails'
gem 'meta_search', '>= 1.1.0.pre'
```

要在 Rails 项目中安装 **activeadmin** :

```
rails generate active_admin:install
rake db:migrate
```

完整的安装文档可以在[这里](http://activeadmin.info/documentation.html)找到。

#### 使用

导航至`0.0.0.0:3000/admin`并使用登录

 `User: admin@example.com
Password: password` 

要注册一个模型，运行:
`rails generate active_admin:resource [MyModelName]`

## [rails-composer](https://github.com/RailsApps/rails-composer)

Rails Composer 是一个很棒的脚本，只需要很少的配置就可以在几秒钟内启动您的项目。Rails Composer 肯定使用了我们上面提到的大多数 gem，所以如果你想要最快的方式，你可以使用脚本，如果你想要特定的 gem，你可以选择任何对你有帮助的东西。

要使用 Rails Composer 创建一个项目，像往常一样使用您的项目名运行`rails new`命令，并按如下方式传递`-m`参数

```
rails new myapp -m https://raw.github.com/RailsApps/rails-composer/master/composer.rb
```

在开始一个新的 rails 项目时，您将面临大约 20 个问题，几乎涵盖了您可以考虑的所有方面。涵盖了从 app 结构、web 服务器、模板引擎、测试、前端框架、认证，甚至 github 资源库的一切。你可以在这里找到选项的完整列表。

## 包扎

我们已经介绍了大约 20 种宝石和脚本来帮助您更有效地实现您的 Rails 目标。如果你正在使用一块神奇的宝石，它能神奇地帮助你以更好的方式完成事情，我很乐意听到它。

## 分享这篇文章