# 部署 Rails 应用程序

> 原文：<https://www.sitepoint.com/deploying-a-rails-application/>

部署一个 PHP web 应用程序需要通过 FTP 将文件上传到服务器，或者提交并推送到一个 [Git](http://git-scm.com) 库的主分支。这没什么复杂的。[根据 Rails 官方文档，部署 Rails 应用很容易](http://rubyonrails.org/deploy/)。尽管我很喜欢官方文件，但他们在这一点上大错特错了。部署 Rails 应用程序可能是一件非常头疼的事情，尤其是因为有太多的选项。

不过，最终归结为两件事:你可以部署到一个基于云的服务，在那里大部分硬东西会为你照看，或者为了更多的控制，你可以部署到一个虚拟专用服务器(VPS)。在本系列中，我们将研究这两个选项。

### 部署到云

我们将选择 [Heroku](http://www.heroku.com) 作为我们的云选项，因为它在 Rack and Rails 应用程序部署中非常受欢迎。当您第一次在 Heroku 的服务上创建应用程序时，您可以通过将您的更改推送到 Heroku 为您提供的 Git remote 来部署到 Heroku。

对于转移到 ruby 的 PHP 程序员来说，这应该感觉很熟悉，尤其是如果你已经使用了云平台，比如说 [phpfog](http://www.phpfog.com) 。

在 Heroku 上安装并运行 Rails 应用程序需要几个步骤:

1.  开发你的应用程序(doh！)
2.  如果你还没有注册 Heroku
3.  为你的项目初始化 Git
4.  创建 Heroku 应用程序
5.  为 Heroku 添加 Git remote(例如，您可能已经有了一个 Github remote repo)
6.  将您的应用程序推送到 Heroku
7.  迁移数据库(如果您正在使用)

#### 选择数据库

Heroku 不支持 Sqlite，所以如果你的 app 用了，Heroku 会投诉。Heroku 使用了优秀的 [Postgresql](http://www.postgresql.org) 。在应用程序的初始部署期间，您可以运行迁移来填充 Heroku Postgresql 数据库。如果您已经从 PHP 转向使用 Rails 进行开发，您将会习惯 MySQL。在这种情况下，Postgresql 没有什么可怕的。最近它越来越受 Rails 开发人员的欢迎，因此非常值得习惯。

如果您仍在考虑使用 Sqlite，[Heroku dev center 上的这篇文章](https://devcenter.heroku.com/articles/how-do-i-use-sqlite3-for-development)应该能帮您理清头绪。

#### 开始吧…

所以，你的应用已经到了可以推送到 Heroku 的地步。把它变成一个 Heroku app，只是一个上命令行/终端的案例。将目录更改到您的项目，并确保您已经提交了所有最新的更改。然后简单地做:

```
heroku create 
```

接下来会发生的是 Heroku 会告诉你你的应用程序已经创建好了。很有可能它会有一个奇怪的名字(比如无限悬崖-4759)，但是我们可以很快改变它。您还会被告知 Heroku 的 Git 远程 url 是什么。这样做的好处是，你可以将你的更改推送到 Github，然后，当你准备好部署时，你可以推送到你的 Heroku 分支。

#### 更改应用程序名称

假设你想让这个应用有一个真正的目的，而不仅仅是为了测试，你会想用一个合理的名字。回到命令行，执行以下操作:

```
heroku apps:rename new_name_here 
```

这样做的好处是你的 Git 遥控器会自动更新。如果您使用 Heroku 网站来重命名您的应用程序，您需要移除旧的 Heroku 遥控器并添加新的遥控器:

```
git remote rm heroku git remote add heroku git@heroku.com:newname.git 
```

如果您有一个想要用于应用程序的域名，请返回命令行并执行以下操作:

```
heroku domains:add www.mydomain.com 
```

接下来，我们将进行部署。

#### 部署应用程序

你需要做的就是:

```
 git push heroku master 
```

你会看到 Heroku 运行了一大堆流程，包括`rake assets:precompile`这真的很棒。当它结束时，您应该看到类似于:`http://yourapp.herokuapp.com deployed to Heroku`的内容。太好了！哦，等一下…

#### 数据库

您还可以部署您的迁移。回到命令行，执行以下操作:

```
heroku run rake db:migrate 
```

您可能会在 vendor/plugins 中看到一个关于插件的弃用警告。如果你知道你没有对插件做任何事情，你可以安全地忽略这个警告。

#### 这就是所有的乡亲…

真的。就这样，你的应用程序现在应该启动并运行了。Heroku 唯一的问题是:免费版是一个很棒的服务。但是，如果您需要添加更多的资源，这可能会非常昂贵。别说我没警告你…

### 部署到虚拟专用服务器(VPS)

另一个选择是给自己买一个 VPS，并在上面部署你的应用。我这里用的这个叫做 [Linode](http://www.linode.com) 。我首先要指出的是，如果设置服务器对你没有吸引力，那么选择 Heroku。

当你注册 Linode 时，你可以选择(Linux)服务器操作系统。他们有 Ubuntu，Fedora，OpenSUSE 等。所以你最喜欢的可能会得到支持。出了问题也有可能创造和推倒。事实上，在 Linode 上是被鼓励的。所以如果你是一个 Linux n00b，有很多帮助和支持。

安装服务器时，它是一个原始的、基本的服务器安装。这意味着，要启动并运行 Rails 应用程序，你需要先做一些安装工作。哦，如果你想知道，不，没有图形用户界面。一路都是命令行。

#### 生产堆栈

你可以选择。我最终选择了 [Nginx](http://nginx.org) 做网络服务器， [PostgreSQL](http://www.postgresql.org) 做数据库，而 [rbenv](https://github.com/sstephenson/rbenv) 管理 Ruby 版本。你可以很容易地选择 [Apache](http://www.apache.org) 、 [Phusion Passenger](http://www.modrails.com) 和 [MySQL](http://www.mysql.com) 。我做出这些选择是因为我已经修补了 Phusion Passenger 和 [RVM](https://rvm.io) ，并想尝试其他选项。

一旦你设置好你的 Linode，通过 SSH 登录。您的 Linode 仪表盘拥有您需要的所有信息:

```
ssh root@176.xxx.xxx.xxx 
```

您应该先运行一个发行版更新并安装任何需要的更新。然后安装生产堆栈组件。如果您使用的是 ubuntu，请安装软件属性选项，以便于向 apt 添加新的存储库:

```
apt-get -y install curl git-core python-software-properties 
```

然后您可以为 Nginx 添加稳定的分支:

```
add-apt-repository ppa:nginx/stable 
```

不管您的发行版是什么，您都需要安装:

1.  Nginx 或 Apache
2.  MySQL 或 PostgreSQL
3.  同时安装 node.js，这样就有了执行 Javascript 的方法。
4.  您还需要安装 Git 和 Curl。

如果使用安装脚本:
`curl -L https://raw.github.com/fesplugas/rbenv-installer/master/bin/rbenv-installer | bash`，通过 rbenv 安装 Ruby 会更容易

你会被告知复制一些行到你的。bashrc 文件，所以一定要这样做。你复制它们的地方也有点麻烦。这些行需要在你的。bashrc 文件:

```
# If not running interactively, don't do anything [ -z "$PS1" ] && return 
```

这是为了让 rbenv 能够与 Capistrano 部署一起工作，我们很快就会谈到这一点。你需要重新加载你的。现在的 bashrc 文件:`~/.bashrc`。此时，rbenv 命令将在命令行上可用，并可用于为 Ruby 安装一些重要的依赖项。例如，对于 Ubuntu 12.04，您可以:

```
rbenv bootstrap-ubuntu-12-04 
```

然后等待一段时间，让依赖项安装完毕。完成后，您就可以安装 Ruby 了:

```
rbenv install 1.9.3-p125 
```

然后将其设置为默认拼音:

```
rbenv global 1.9.3-p125 
```

最后，安装 bundler gem，以便我们能够访问可执行包:

```
gem install bundler --no-ri --no-rdoc 
```

使用 rbenv rehash 命令获取更改:

```
rbenv rehash 
```

### 准备好应用程序

假设您将通过 Github 管理您的源代码，那么从我们的服务器向它添加一个 ssh 连接是一个好主意。仍然登录时，执行:`ssh git@github.com`。这将确保当我们通过 Capistrano 部署时，不会出现任何“未知”错误。

> 这个想法是，在我们通过 Capistrano 部署之前，我们可以先检查所有东西是否都提交给了 Github。

现在可以正常开发推送至 github 您的 Linode 服务器已经准备好接收部署的应用程序了。不过首先，我们需要建立[独角兽](http://unicorn.bogomips.org)和[卡皮斯特拉诺](https://github.com/capistrano/capistrano)。

### 独角兽和卡皮斯特拉诺

Unicorn 是一个 HTTP 服务器，可以很好地与 Nginx 一起运行机架应用程序。Capistrano 是以半自动方式部署 Rails 应用程序的众多方法之一。

我们需要将它们都添加到我们的 gemfile 中:

```
gem 'unicorn' gem 'capistrano' 
```

回到命令行，在您的应用程序目录中运行`capify`命令。这将创建一个 **Capfile** 和一个 **config/deploy.rb** 文件，这两个文件都需要编辑。

从 capfile 开始，我们使用资产管道，因此我们将相应地取消注释:

```
load 'deploy' # Uncomment if you are using Rails' asset pipeline load 'deploy/assets' Dir['vendor/gems/*/recipes/*.rb','vendor/plugins/*/recipes/*.rb'].each { |plugin| load(plugin) } load 'config/deploy' # remove this line to skip loading any of the default tasks 
```

然后，我们可以粘贴我们的 Capistrano 食谱: