# 氮气。木卫一和赫洛库:完美的一对

> 原文：<https://www.sitepoint.com/nitrous-io-heroku-perfect-pair/>

![256-spedometer](img/b2289af6c4d1eeb9ce98655db4d69d70.png)

本文是 [Nitrous 的后续文章。IO:云中的 Rails 开发](https://www.sitepoint.com/nitrous-io-rails-development-cloud/)早前发表。如果你还没有读过那篇文章，我建议你读一读。*我会等*。

虽然这不是先决条件，但本教程假设你有一个关于 T2 一氧化二氮的账户。IO 并为 Ruby 设置了一个盒子。

#### 从氮气箱开始

如果您尚未在 [Nitrous.io](http://nitrous.io) 上创建盒子，选择`Boxes`菜单并创建一个新盒子。

Nitrous Box 必须连接到您的 [Github](http://www.github.com) 帐户。[氮气帮助中心的这些说明](http://help.nitrous.io/github-add-key/)是完成这项任务的最佳指南。

#### 登录 Heroku

假设您已经拥有一个 Heroku 账户。如果没有[现在就创建一个](https://id.heroku.com/signup)。
Heroku 提供了一个非常详细的 API(称为 Toolbelt)，当你创建一个盒子时，它是 Nitrous 实例的一部分。

```
$ heroku login
```

您需要提供您的登录电子邮件和密码。对此，我们应该表示感谢:

```
Authentication successful.
```

如果这是第一次使用这个 Nitrous Box 部署到 Heroku，您需要将您的 SSH 密钥添加到 Heroku。*如果您不确定这一步过去是否已经完成，只需再次运行该命令*也无妨。

```
$ heroku keys:add
```

#### 创建您的应用

##### 创建 Rails 应用程序

接下来，您可以简单地创建一个 Rails 应用程序。我们将指定一个 PostgreSQL 数据库(因为现在所有“酷孩子”都在用这个数据库)。

```
$ rails new APPNAME --database=postgresql
$ cd APPNAME
```

##### 进入设置

Git 中通常的(应该非常熟悉的)初始提交是这样的:

```
$ git init
$ git config --global user.name "Cody Wrighter"
$ git config --global user.email "C.Wrighter@ParaHacker.com"
$ git add .
$ git commit -m "Initial Commit on Nitrous Box"
```

##### 在 Heroku 上创建新的应用程序

使用 Heroku Toolbelt，您可以通过以下简单命令在 Heroku 上创建应用程序:

```
$ heroku create
```

它会用 Heroku 上的应用程序名称来响应。我建议您复制这个特殊的(通常是诗意的)应用程序名称，以便在后面的步骤中使用。

因为 [Bundler](http://bundler.io/) 也包含在 Nitrous 实例中，所以在 Gemfile 中添加以下几行:

```
ruby '2.0.0'  # to maintain parity between 'development' and 'production'

gem 'pg'  #this may already exist in the Gemfile
gem 'rails_12factor', group: :production
```

你应该使用 RVM 在 Nitrous 上安装 Ruby 2.0.0。RVM 也包含在每个氮气箱中，所以这就像`rvm install 2.0.0`后面跟着`rvm use 2.0.0`一样简单

下一步是在 Heroku 上建立 PostgreSQL 数据库。

使用 Heroku 工具带，

```
$ heroku addons:add heroku-postgresql:dev
```

将添加一个 PostgreSQL 数据库(免费级别)并返回一个 HEROKU_POSTGRES_COLOR_URL。记下返回的实际**颜色**。

然后

```
$ heroku pg:promote HEROKU_POSTGRESQL_URL
```

建立数据库(您需要登录 Heroku 仪表板以获取 PostreSQL 详细信息)。

通常，创建 Rails 应用程序时，下一步是编辑`config/database.yml`文件。但是使用 Heroku，您的生产数据库细节会自动应用到 Heroku 服务器*上，而不管您在 database.yml 文件*中放置了什么。

*要使用 Heroku PostgreSQL 数据库进行开发，请遵循这里的[说明。如果您喜欢使用本地 SQLite 数据库进行开发，这些说明在本文](http://help.nitrous.io/postgres/)的结尾[。](#sqlite3)*

现在是时候让 Bundler 为我们施展魔法，把所有的碎片拼在一起了。

```
$ bundle install
```

#### 设置资产管道

在 config/application.rb 中添加一行

```
config.assets.initialize_on_precompile = false
```

#### 在 Git 中提交

```
$ git add .
$ git commit -m "Heroku and Postgresql setup"
```

#### 将应用程序部署到 Heroku

部署到 Heroku 是整个过程中最简单的部分。我真的很感激，因为这是我在持续开发周期中最常做的部分。

```
$ git push heroku master
```

### 在 Heroku 上设置服务器

如果你想留在自由王国，在 Heroku 上使用一个 dyno。

```
$ heroku ps:scale web=1
$ heroku ps
```

#### 访问服务器上的应用程序

在这一点上，我喜欢执行一个快速的“冒烟测试”，以确保我们没有错过任何必要的步骤。当您对应用程序进行故障诊断时，这可以省去很多麻烦，因为您可以确保它在基础级别上工作。在这里，您将使用 Heroku 为您的应用程序创建的[有时富有诗意，有时意味深长]的名称。你可以从你的剪贴板上粘贴这个名字(如果你遵循了我之前的建议)

```
$ curl http://<HEROKU_APP_NAME>.herokuapp.com/
```

或者您可以导航到实例(基于它之前返回的名称):
我刚刚在浏览器中打开了另一个当前连接到 Nitrous 的标签。

您应该会得到 Rails 默认的`index.html`页面。我们尚未创建任何模型、控制器或路线，因此“关于您的应用环境”链接将产生服务器错误。

这是可以预料的。

使用 Heroku ToolBelt，使用`heroku logs`查看应用程序日志。最后一行的“status=200”是我们希望看到的。

现在我们需要迁移数据库。

```
$ heroku run rake db:migrate
```

#### 对我们进展的快速评估

您已经完全在浏览器中创建和部署了一个 Rails 应用程序**!**

利用氮气。IO 作为基于云的开发平台，Heroku 作为基于云的 SAAS 主机，你可以完全脱离和不加选择地使用你的客户端硬件和/或软件。

#### 设置开发服务器

为了将这个空的、良性的 Rails 应用程序转化为你的下一个赚大钱的颠覆性 Web 产品，我们需要做一些繁重的测试——编码——重构敏捷开发。现在让我们来设置开发环境。

在 gem 文件中添加`gem 'unicorn', group: :development`并运行`bundle install`来本地安装它。

为了测试(以及开发期间的每个迭代),运行以下命令

```
$ bundle exec unicorn -p 3000 -E development
```

使用 Nitrous 菜单**预览- >端口 3000** 来测试您的*开发*环境

##### 最后一次提交

```
$ git add .
$ git commit -m "use unicorn for local development"
$ git push heroku
```

如果你喜欢生产用的 unicorn，这里有关于[设置](http://devcenter.heroku.com/articles/rails-unicorn)的细节。

#### 竣工清理

当然，`rm`这个`public/index.html`文件很重要。

我建议你也在你的 Github 账户中维护这个项目。作为一个公共存储库，你可以从开源社区以贡献和请求的形式获得帮助。使用私有存储库，您可以与团队中的其他开发人员协作，并维护中央源代码控制。

Nitrous Box 必须连接到您的 [Github](http://www.github.com) 帐户。[在](http://help.nitrous.io/github-add-key/)[亚硝酸帮助中心](http://help.nitrous.io)的这篇伟大的文章解释了如何设置 Github 私钥。

首先在 Github 上创建存储库。然后发出这两个命令:

```
$ git remote add github git@github.com:<github_username>/nitrous_heroku.git
$ git push -u github master
```

接下来，编写一些测试。然后你将创建一些模型、控制器和视图，并开始实际开发应用程序。

使用以下命令运行 unicorn 服务器

```
$ bundle exec unicorn -p 3000 -E development
```

使用 Nitrous 菜单**预览- >端口 3000** 来测试您的*开发*环境

* * *

### 更新开发环境

如果您喜欢在开发中使用 sqlite(我就是这样做的),那么添加这些步骤。

将“开发”(和“测试”)的 config/database.yml 修改如下:

```
development:
  adapter: sqlite3
  database: db/development.sqlite3
  pool: 5
  timeout: 5000

test:
  adapter: sqlite3
  database: db/test.sqlite3
  pool: 5
  timeout: 5000
```

将 sqlite3 添加到 gem 文件

```
group :development, :test do   #sqlite3 gem is not supported on Heroku
  gem 'sqlite3'
end
```

拜访 Bundler

```
$ bundle install
```

然后迁移数据库

```
$ export RAILS_ENV=development
$ rake db:migrate
```

打电话给 unicorn 服务器进行测试

```
$ bundle exec unicorn -p 3000 -E development
```

然后使用 Nitrous 菜单**预览- >端口 3000** 在浏览器中打开它

您现在拥有完整的开发和生产环境，以及基于 git 的部署，并且您不需要在本地计算机上安装任何东西。

我很高兴迎来一个新的基于浏览器的工作流程。

## 分享这篇文章