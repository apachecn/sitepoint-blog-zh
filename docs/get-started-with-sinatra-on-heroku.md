# 从 Heroku 上的 Sinatra 开始

> 原文：<https://www.sitepoint.com/get-started-with-sinatra-on-heroku/>

这篇文章将向你简要介绍如何开始使用 Ruby 和部署到 Heroku 的 Sinatra web 框架。掌握基本的 Ruby 知识很重要，包括 Ruby 1.9.2、Rubygems 和 Bundler 的安装版本。

**其他先决条件如下:**

*   基本的 Git 知识
*   你的应用需要在 Ruby (MRI) 1.9.2 上运行
*   你的应用应该使用 Bundler
*   Heroku 帐户

## 设置您的本地工作站

要访问 Heroku 命令行客户端、Git 分布式版本控制和 Foreman，请在您的本地工作站上安装 [Heroku Toolbelt](https://toolbelt.heroku.com/) 。

通过在命令外壳中键入`heroku`命令，使用您的电子邮件 ID 和密码登录。

```
$ heroku login
Enter your Heroku credentials.
Email: adam@example.com
Password:
Could not find an existing public key.
Would you like to generate one? [Yn]
Generating new SSH public key.
Uploading ssh public key /Users/adam/.ssh/id_rsa.pub
```

当它显示“找不到现有的公钥”时，键入`y`创建一个新的`ssh`密钥。此键允许您将代码推送到 Heroku。使用`heroku`命令，可以在 Heroku 中添加[公钥](https://devcenter.heroku.com/articles/keys#adding-keys-to-heroku)。

## 入门指南

如果你有一个现有的 Sinatra 应用程序，请随意使用。否则，这里有一个“在 Heroku 上学习 Ruby”源文件供您使用:

### web.rb

```
require 'sinatra'
get '/' do
  "Learning Ruby on Heroku"
end
```

## 用 Gemfile 声明依赖关系

一个`Gemfile`的存在告诉 Heroku 这是一个 Ruby 应用。

#### Gemfile

```
source "https://rubygems.org"
gem 'sinatra', '1.1.0'
```

运行`bundle install`在您的本地工作站上设置 bundle。

## 用概要文件声明流程类型

Foreman 在你的应用根目录下使用一个名为‘Procfile’的文本文件来声明需要运行哪些进程。

由于所有 Sinatra 应用程序都是机架兼容的，我们可以在目录的根目录下创建一个 **config.ru** 文件，并告诉 Foreman 使用它。

创建文件`config.ru`:

```
require 'my_app'
run MyApp.new
```

运行它以确保应用程序启动:

```
rackup config.ru
```

你可能想参考我们的四集辛纳屈教程来了解更多的信息。

现在，可以将它添加到 Procfile 中:

```
web: bundle exec rackup config.ru -p $PORT
```

已经声明了一个流程类型`web`，以及执行它所需的命令。“web”这个名称在这里很有意义，因为它表示这个流程类型将连接到 Heroku 的 HTTP 路由堆栈，并在实现时处理 web 流量。

现在，您可以使用 Foreman(安装在工具带内)在本地开始使用您的应用程序了:

```
$ foreman start
16:39:04 web.1 | started with pid 30728
18:49:43 web.1 | [2013-03-12 18:49:43] INFO WEBrick 1.3.1
18:49:43 web.1 | [2013-03-12 18:49:43] INFO ruby 1.9.2p290 (2011-07-09 revision 32553) [x86_64-linux]
18:49:43 web.1 | [2013-03-12 18:49:43] INFO WEBrick::HTTPServer#start: pid=30728 port=5000
```

该应用程序将显示在端口 5000 上。确保您检查它是否与网络浏览器一起正常工作，然后按`Ctrl-C`退出。

## 将应用程序放入 Git

我们现在有了应用程序的三个主要元素:一个 **Procfile** 中的流程类型，一个 **Gemfile** 中的依赖项，以及一个 **web.rb** 中的源代码。让我们将您的应用程序存储到 Git 中:

```
$ git init
$ git add .
$ git commit -m "init"
```

## 将应用程序部署到 Heroku

在 Heroku 上创建应用程序:

```
$ heroku create
Creating blazing-galaxy-997... done, stack is cedar
http://blazing-galaxy-997.herokuapp.com/ | git@heroku.com:blazing-galaxy-997.git
Git remote heroku added
```

注意:你的 Heroku 应用名称将与我的不同(“炽焰-银河-997”)。

#### 部署代码

为了将我们的代码推送到 Heroku，我们使用`git push`命令:

```
$ git push heroku master
Counting objects: 6, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 660 bytes, done.
Total 6 (delta 0), reused 0 (delta 0)
-----> Heroku receiving push
-----> Ruby app detected
-----> Installing dependencies using Bundler version 1.1
Checking for unresolved dependencies.
Unresolved dependencies detected.
Running: bundle install --without development:test --path vendor/bundle --deployment
Fetching source index for https://rubygems.org/
Installing rack (1.2.2)
Installing tilt (1.3)
Installing sinatra (1.1.0)
Using bundler (1.1)
Your bundle is complete! It was installed into ./vendor/bundle
-----> Discovering process types
Procfile declares types -> web
Default types for Ruby -> console, rake
-----> Compiled slug size is 6.3MB
-----> Launching... done, v4
http://blazing-galaxy-997.herokuapp.com deployed to Heroku
To git@heroku.com:blazing-galaxy-997.git
* [new branch] master -> master
```

注意，Heroku 告诉我们它在我们的 Procfile 中找到了`web`进程。一切都在正常运转。

## 控制您的应用

现在您已经将代码部署到 Heroku。下一步是执行一个流程类型，通过指示 Heroku 在“dyno”中运行相关命令——Heroku 上的基本工作流程。

您可以通过为一个流程类型添加更多的 dynos 来扩展您的应用程序。这里有一个确保您有一个 dyno 运行`web`流程类型的例子:

```
$ heroku ps:scale web=1
```

要检查应用程序的动态操作状态，使用`heroku ps`命令显示应用程序正在运行的动态操作。

```
$ heroku ps
=== web: `bundle exec ruby web.rb -p $PORT`
web.1: up for 9m
```

显示 app 的 1 dyno 正在运行。使用命令`heroku open`，您可以访问您的应用程序。

```
$ heroku open
Opening blazing-galaxy-997... done
```

## 按比例放大

Heroku 应用程序的第一个 web dyno 是免费的。然而，如果你没有超过免费的 dyno，Heroku 保留在一段时间不使用后终止 dyno 的权利。您可以通过添加第二个 dyno 来避免这种情况:

```
$ heroku ps:scale web=2
```

对于每个应用程序，您可以免费使用最多 750 个动态小时。确保你不会用完每月的免费津贴。要缩回到一个 dyno:

```
$ heroku ps:scale web=1
```

## 查看日志

Heroku 使用命令`heroku logs`方便查看日志文件:

```
$ heroku logs
2013-03-13T04:10:49+00:00 heroku[web.1]: Starting process with command `bundle exec ruby web.rb -p 25410`
2013-03-13T04:10:50+00:00 app[web.1]: [2013-03-13 04:10:50] INFO WEBrick 1.3.1
2013-03-13T04:10:50+00:00 app[web.1]: [2013-03-13 04:10:50] INFO ruby 1.9.2 (2011-07-09) [x86_64-linux]
2013-03-13T04:10:50+00:00 app[web.1]: [2013-03-13 04:10:50] INFO WEBrick::HTTPServer#start: pid=2 port=25410
```

## 安慰

您可以使用`heroku run`命令在一次性的 dyno 中运行命令，比如只需要执行一次的脚本。使用它，您可以启动一个链接到本地终端的交互式 Ruby shell ( `bundle exec irb`)，以便在应用程序的环境中进行测试:

```
$ heroku run console
Running `console` attached to terminal... up, ps.1
irb(main):001:0>
```

默认情况下只加载 Ruby 标准库。从它你可以`require`你的一些应用程序文件。也可以在命令行上完成:

```
$ heroku run console -r ./web
```

## 耙子

与控制台类似，您可以在一次性 dyno 中运行`rake`:

```
$ heroku run rake db:migrate
```

## 如何使用 SQL 数据库

对于我们的示例应用程序，您不需要使用 SQL 数据库，但是大多数“真正的”应用程序需要某种持久性。要在您的应用程序中添加免费的开发 PostgreSQL 实例:

```
$ heroku addons:add heroku-postgresql:dev
```

您需要将 Postgres gem 添加到您的应用程序中，方法是在您的`Gemfile`中添加一行，如下所示:

```
gem 'pg'
```

和重新部署您的应用程序。

要在做出更改后重新部署应用程序:

```
git commit -am "Added pg gem"
git push heroku master
```

这篇文章实际上只是触及了将 Sinatra 应用程序部署到 Heroku 的皮毛。接下来的步骤可能是:

*   让应用程序将状态保存到 Postgres
*   添加一些 javascript/css
*   探索看似无穷无尽的 Heroku 附加组件,看看你可以利用哪些服务来改进你的应用。

祝你好运！

## 分享这篇文章