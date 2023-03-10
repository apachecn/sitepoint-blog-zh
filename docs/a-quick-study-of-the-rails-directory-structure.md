# Rails 目录结构的快速研究

> 原文：<https://www.sitepoint.com/a-quick-study-of-the-rails-directory-structure/>

[![](img/2f4365bfb34dad25ec9b3d515bf66f43.png)](https://www.sitepoint.com/wp-content/uploads/2016/02/1456508788Fotolia_20304375_Subscription_Monthly_M.jpg)

Ruby on Rails 是一个用 Ruby 编写的成熟的 web 框架。它是最著名的 web 框架之一，让开发变得如此简单。今天我想解释一下 Rails 生成的默认文件和文件夹。让我们开始吧。

要创建一个新的 Rails 应用程序，我们只需运行以下命令:

```
rails new sample_app 
```

上面的命令生成了我们今天讨论的目录结构。如果你使用的是旧版本的 Rails，它可能会改变 T2。本文基于最新的 Rails 版本，在撰写本文时，这个版本是`4.2.4`。通过在控制台中键入以下命令，您可以找到系统中安装的版本

```
rails version
=> Rails 4.2.4 
```

目录结构，

```
.
|-- app
|   |-- assets
|   |   |-- images
|   |   |-- javascripts
|   |   |   `-- application.js
|   |   `-- stylesheets
|   |       `-- application.css
|   |-- controllers
|   |   |-- application_controller.rb
|   |   `-- concerns
|   |-- helpers
|   |   `-- application_helper.rb
|   |-- mailers
|   |-- models
|   |   `-- concerns
|   `-- views
|       `-- layouts
|           `-- application.html.erb
|-- bin
|   |-- bundle
|   |-- rails
|   |-- rake
|   |-- setup
|   `-- spring
|-- config
|   |-- application.rb
|   |-- boot.rb
|   |-- database.yml
|   |-- environment.rb
|   |-- environments
|   |   |-- development.rb
|   |   |-- production.rb
|   |   `-- test.rb
|   |-- initializers
|   |   |-- assets.rb
|   |   |-- backtrace_silencers.rb
|   |   |-- cookies_serializer.rb
|   |   |-- filter_parameter_logging.rb
|   |   |-- inflections.rb
|   |   |-- mime_types.rb
|   |   |-- session_store.rb
|   |   `-- wrap_parameters.rb
|   |-- locales
|   |   `-- en.yml
|   |-- routes.rb
|   `-- secrets.yml
|-- config.ru
|-- db
|   `-- seeds.rb
|-- Gemfile
|-- Gemfile.lock
|-- lib
|   |-- assets
|   `-- tasks
|-- log
|-- public
|   |-- 404.html
|   |-- 422.html
|   |-- 500.html
|   |-- favicon.ico
|   `-- robots.txt
|-- Rakefile
|-- README.rdoc
|-- test
|   |-- controllers
|   |-- fixtures
|   |-- helpers
|   |-- integration
|   |-- mailers
|   |-- models
|   `-- test_helper.rb
|-- tmp
|   `-- cache
|       `-- assets
`-- vendor
`-- assets
    |-- javascripts
    `-- stylesheets 
```

现在，我们将逐一浏览每个目录。

## 应用

这是整个应用程序的核心目录，大多数特定于应用程序的代码都将存放在这个目录中。如您所知，Rails 是一个 MVC 框架，这意味着应用程序根据其在模型、视图或控制器中的用途被分成几个部分。这三个部分放在这个目录中。

### 应用/资产

这包含应用程序前端所需的静态文件，这些文件根据其类型分组到文件夹中。这些文件夹中的 javascript 文件和样式表(CSS)应该是特定于应用程序的，因为外部库文件会进入另一个目录(`vendor`)，我们稍后会谈到这个目录。

### 应用程序/资产/图像

应用程序所需的所有图像都应该放在这个目录中。这里的图像可以通过像`image_tag("img_name.png")`这样漂亮的助手在视图中获得，因此您不必指定图像的相对或绝对路径。

### 应用程序/资产/JavaScript

javascript 文件放在这里。为每个控制器创建 JS 文件有一个约定。例如，对于`books_controller.rb`，该控制器视图的 JS 函数将是`books.js`。

#### app/assets/JavaScript/application . js

预先创建的`application.js`是整个应用程序的 javascript 需求的清单。Rails 使用[资产管道](https://guides.rubyonrails.org/asset_pipeline.html)来编译和提供资产。这意味着`application.js`是您引用特定于应用程序的 JS 文件的文件，这些文件在发送到视图之前被统一和缩小。

尽量不要在这个文件中创建 javascript 函数。

### 应用程序/资产/样式表

类似于`/javascripts`，CSS 文件放在这里。命名约定也与 javascript 资源相同。

#### app/assets/样式表/application.css

这个文件是应用程序中样式表的清单。与`application.js`类似，引用的文件作为单个样式表提供给视图。

## 应用程序/控制器

这是所有控制器文件存放的地方。控制器负责编排模型和视图。该文件的命名约定是复数模型名称+ "_controller "。比如`Book`型号的控制器是`books_controller.rb`，叫做“蛇案”。另外，控制器类将是 CamelCase，也就是`BooksController`。

生成控制器的脚本是:

```
rails generate controller controller_name action1 action2 
```

### app/controller/application _ controller . Rb

这是所有其他控制器继承的主控制器。`ApplicationController`上的方法也适用于其他控制器。这个控制器继承自`ActionController::Base`模块，该模块有一组在控制器中使用的方法。

### 应用程序/控制器/问题

`Concerns`是可以跨控制器使用的模块。这有助于通过在目录中实现可重用的功能来减少代码。命名约定是`module_name.rb`。

## 应用程序/助手

这是视图的所有助手函数所在的地方。已经有一些预先创建的助手可用，比如我们上面提到的(`image_tag`)用于引用视图中的图像。您可以在特定于控制器的助手文件中创建自己的函数，当您使用 Rails 生成器创建控制器时，将会自动创建该文件。命名约定是`controller_name_helper.rb`。

### app/helper/application _ helper . Rb

这是根助手。类似于`application_controller.rb`，默认情况下，这里编写的函数在所有的帮助器和视图中都是可用的。

## 应用程序/邮件程序

`mailers`目录包含应用程序的邮件(如“e mail”)特定功能。邮件程序类似于控制器，它们在`app/views/mailer_name/`中有自己的视图文件。要生成邮件程序，您可以运行以下命令

```
rails generate mailer MailerName 
```

首次生成邮件程序时，会自动为您创建`application_mailer.rb`。这将从`ActionMailer::Base`继承，并为您的邮件视图设置默认的`from`地址和`layout`。随后的邮寄者将继承`ApplicationMailer`。

命名约定类似于控制器:`modelname_mailer.rb`。你可以在这里阅读更多关于邮寄者的信息。

## 应用程序/型号

所有的模型文件都存在于`app/models`目录中。模型充当保存数据的数据库表的对象关系映射器。命名约定简单来说就是`modelname.rb`。按照惯例，模型名称是表示数据库中模型的基础表的单数形式。因此，`Book`模型将被映射到数据库中的`books`表的顶部。点击，你可以在[了解更多关于 Rails 车型的信息。](http://guides.rubyonrails.org/active_model_basics.html)

### 应用程序/模型/问题

模型关注点类似于控制器关注点，包含可能在多个模型文件中使用的方法。这非常有助于组织代码。

## 应用程序/视图

MVC 架构的第三部分是视图。与视图相关的文件放在这个目录中。这些文件是 HTML 和 Ruby(称为嵌入式 Ruby 或 Erb)的组合，并根据它们对应的控制器进行组织。每个控制器动作都有一个视图文件。例如，`BooksController#index`动作的相应视图如下

```
app/views/books/index.html.erb 
```

这是 Rails 的另一个约定，可以打破。换句话说，您可以手动显式呈现任何视图。

### 应用程序/视图/布局

此文件夹保存了所有视图文件的布局，这些文件是由它们继承的。在此处创建的文件可用于所有视图文件。

您可以创建应用程序各部分范围内的多个布局。例如，如果我们想为管理视图和用户视图创建一个单独的布局，我们可以通过创建一个以控制器名命名的布局字段来实现。对于所有的`AdminController`视图，创建一个名为`admin.html.erb`的布局文件，它将作为管理视图的布局，以及任何从`AdminController`继承的控制器。

您还可以在这里创建可能跨控制器使用的局部视图。

#### app/views/layouts/application . html . erb

这将是自动创建的默认文件，它将作为`ApplicationController`和从`ApplicationController`继承的任何其他控制器中的动作的布局。

## 箱子

这个目录包含 Rails 应用程序的 Binstubs。Binstubs 不过是运行应用程序范围内的 gem 可执行文件的包装器。这个可以用来代替`bundle exec <command>`。默认可用的 Binstubs 有`bundle`、`rails`、`rake`、`setup`和`spring`。这些 binstubs 中的任何一个都可以通过以下方式执行:

```
bin/<executable> 
```

## 配置

顾名思义，它包含了所有应用程序的配置文件。该目录中的文件可以改变数据库连接和应用程序行为。

### 配置/应用程序. rb

这包含应用程序的主要配置，例如要使用的时区、语言和许多其他设置。完整列表可在[这里](http://guides.rubyonrails.org/configuring.html)找到。另外，请注意这里指定的配置适用于所有环境(开发、测试和生产)。特定于环境的配置将进入其他文件，我们将在下面看到。

### config/boot.rb

`boot.rb`正如你可能想象的那样，“启动”Rails 应用程序。Rails 应用程序将 gem 依赖关系保存在项目根目录下的一个名为`Gemfile`的文件中。`Gemfile`包含应用程序运行所需的所有依赖项。`boot.rb`验证确实存在一个`Gemfile`，并将这个文件的路径存储在一个名为`BUNDLE_GEMFILE`的环境变量中，以备后用。`boot.rb`还需要`'bundler/setup'`，它将使用捆绑器获取并构建`Gemfile`中提到的宝石。

### config/database.yml

这个文件包含应用程序需要的所有数据库配置。这里可以针对不同的环境设置不同的配置。

### 配置/环境. rb

这个文件需要`application.rb`来初始化 Rails 应用程序。

### config/routes.rb

这是定义应用程序所有路由的地方。声明路由有不同的语义，在这个文件中可以找到这样的例子。

### config/secrets.yml

这是 Rails 4.1 中新添加的，它为您提供了一个存储应用程序秘密的地方。这里定义的秘密可以通过使用`Rails.application.secrets.<secret_name>`在整个应用程序中使用。你可以从[的发行说明](http://edgeguides.rubyonrails.org/4_1_release_notes.html#config-secrets-yml)中了解更多关于`secrets.yml`的信息。

### 配置/环境

正如我上面提到的，这个文件夹包含开发、测试和生产环境的特定于环境的配置文件。`application.rb`中的配置在所有环境中都可用，而您可以通过将设置添加到该目录下名为 files 的环境中，为不同的环境分离出不同的配置。可用的默认环境文件有:`development.rb`、`production.rb`、`test.rb`，但是如果需要，您可以添加其他文件。

### 配置/初始化器

该目录包含 Rails 初始化过程中需要运行的文件列表。您在此创建的任何`*.rb`文件将在初始化期间自动运行。例如，您在此处声明的常数将在整个应用程序中可用。初始化文件从`config/environment.rb`到`Rails.application.initialize!`的调用中触发。

有一些核心初始化器，我将在这里介绍，但是你可以添加任何你喜欢的 Ruby 文件。事实上，许多 Rails gems 需要一个初始化器来完成 Rails 应用程序的 gem 设置。

#### config/initializer/assets . Rb

这包含资产管道的配置。它将只有一个已经定义的默认配置`Rails.application.config.assets.version`，这是您的资产包的版本。您还可以指定配置，例如添加要预编译的附加资产路径或其他项。

#### config/initializer/back trace _ silencers . Rb

您可以在此添加可能适用于您的应用程序的 backtrace_silencers 和过滤器。回溯过滤器只不过是在发生错误时帮助细化堆栈跟踪的过滤器。沉默者，另一方面，让你沉默指定宝石的所有堆叠轨迹。

#### config/initializer/cookie _ serializer . Rb

这里没有进行大量的配置。它包含应用程序 cookie 序列化的规范。你可以从[这里](http://api.rubyonrails.org/classes/ActionDispatch/Cookies.html)阅读更多关于 Rails 中 cookie 处理的内容。

#### config/initializer/filter _ parameter _ logging . Rb

您可以在此添加可能包含敏感信息且不想在日志中显示的参数。默认情况下，“密码”参数将被过滤。

#### config/initializer/inflections . Rb

您可以在这里添加/覆盖任何语言的词形变化(单数和复数)。你可以在这里了解更多关于变形 API [的信息。](http://api.rubyonrails.org/classes/ActiveSupport/Inflector.html)

#### config/initializer/mime _ type . Rb

在此为您的应用程序添加 MIME(多用途 Internet 邮件扩展)配置，以处理您可能要使用的不同类型的文件。Mime API 中可用选项的列表可以在[这里](http://api.rubyonrails.org/classes/Mime/Type.html)找到。

#### config/initializer/session _ store . Rb

此文件包含在应用程序中用于存储会话的基础会话存储。默认值是`:cookie_store`，这意味着会话存储在浏览器 cookies 中，但是您可以将其更改为四个选项之一。关于会话商店的选项和更多信息可在[此处](http://guides.rubyonrails.org/action_controller_overview.html#session)获得。

#### config/initializer/wrap _ parameters . Rb

顾名思义，它包含包装参数的设置。默认情况下，所有的参数都包装在一个嵌套的散列中，这样它就可以在没有根的情况下使用。您可以覆盖此设置或在此添加您的自定义设置。

### 配置/区域设置

这是每种语言的 YAML 文件列表，其中包含了翻译应用程序各个部分的键和值。你可以从[这里](http://guides.rubyonrails.org/i18n.html)了解国际化(i18n)和设置。

## 

所有与数据库相关的文件都放在这个文件夹里。可以在这里找到配置、模式和迁移文件，以及任何种子文件。

### db/seeds.rb

该文件用于用所需记录预填充或“播种”数据库。您可以使用普通的 ActiveRecord 方法来插入记录。

## Gemfile

`Gemfile`是所有应用程序的 gem 依赖项被声明的地方。这个文件是强制性的，因为它包含了 Rails 核心 gem 和其他 gem。您可以在 [Bundler 网站](http://bundler.io)了解所有关于 Bundler 和 Gemfiles 的信息。

## Gemfile.lock

`Gemfile.lock`保存 gem 依赖树，包括应用程序的所有版本。该文件由上述`Gemfile`上的`bundle install`生成。实际上，它将你的应用依赖锁定在特定的版本上。

## 解放运动

目录是所有特定应用程序库的所在位置。特定于应用程序的库是从应用程序中提取的可重用的通用代码。可以把它看作是一个特定应用的宝石。

### 图书馆/资产

这个文件包含所有的库资产，也就是说那些不是特定于应用程序的项目(脚本、样式表、图像)。

### 库/任务

应用程序的 Rake 任务和其他任务可以放在这个目录中。这里提到的耙任务是应用程序的`Rakefile`所需要的，我们将在下面看到。

## 原木

这包含了所有的日志文件。Rails 自动为每个环境创建文件。

## 公众的

public 目录包含 web 应用程序的一些公共文件。默认情况下，HTTP 错误(如 404、422 和 500)的 HTML 模板与一个`favicon`和一个`robots.txt`文件一起生成。这个目录下的文件可以直接在`https://appname.com/filename`找到。

## makefile 先生

就在上面，我提到了在`lib/tasks`中创建的 Rake 文件可以通过 Rake 模块在整个应用程序中使用。这是可能的，因为 Rakefile 需要`application.rb`并调用`Rails.application.load_tasks`。对`load_tasks`的调用从`lib/tasks`文件夹中加载所有的`*.rake`文件。

## 试验

文件夹名说明了一切。这保存了应用程序的所有测试文件。为每个组件的测试文件创建一个子目录。

## 终端监督程式（Terminal Monitor Program 的缩写）

这是应用程序保存文件(如缓存)的临时目录。您不需要担心这个目录，因为它完全由 Rails 自己管理。但是如果你想控制这个目录，有几个命令可用，可以在这里找到

## 供应商/资产

这是供应商文件的去处，比如 javascript 库和 CSS 文件等等。此处添加的文件将自动成为资产管道的一部分。

## 结论

至此，我们得出了本文的结论。理解 Rails 应用程序的目录和文件结构可以带您探索 Rails。请花点时间浏览我提到的定制链接，因为它们将帮助您迈向成熟的 Rails 开发人员。

## 分享这篇文章