# 简化:免费的无痛部署

> 原文：<https://www.sitepoint.com/capified-painless-deployment-for-free/>

您已经构建了一个非常棒的应用程序。你需要把它拿出来。让我们面对它，部署应该是容易的，可重复的，如果它不工作，你应该能够回滚到最后一个已知的良好状态。

如果你正在使用手动 Git 工作流(其他 SCMs 可用)或者，天哪禁止 FTP，那么我可以听到你尖叫“鼓吹它！”。我一直在那里，看着所有这些文件通过 FileZilla 上传，迁移数据库，重启服务器。如果你曾经自动化过你的部署，哪怕只有一次，你都会被宠坏。其他都是痛苦。

你很可能听说过 [Capistrano](https://github.com/capistrano/capistrano) 。这是一个用 Ruby 编写的固执己见的部署工具。Capistrano 通过优雅的 DSL 提供用户定义的任务，使您的最新 web 应用程序的部署变得轻而易举。

今天，我们将浏览一些基础知识，检查可能的 web 服务器设置，并查看一些自定义部署任务的设置。

## 观点很重要

记得我说过卡皮斯特拉诺固执己见吗？这些意见来自 Capistrano 对您的应用程序所做的一些假设:

*   您将使用源代码管理
*   您将在应用程序的顶层有一个“config”目录
*   您将使用 SSH 来访问我们要部署到的机器

## 您的服务器设置

我们需要谈谈你服务器的设置。像 Heroku T1 和 T2 engine yard T3 这样的服务可以消除设置 MySQL、Apache 等的所有痛苦。然而，如果我们环顾网络，有太多的便宜的副总裁将满足你的需求。当然，我们将不得不做一些初始工作来获得服务器设置，但这是一次性的交易，我们可以[自动化它](http://rubygems.org/gems/chef)一点点知道如何做。

我的 VPS 使用用户作为托管帐户。如果我有一个名为“capdemo”的应用程序，我也将有一个名为“capdemo”的用户，一个主目录作为他们的一块托管馅饼。

我也用一个 [Apache](http://httpd.apache.org/) 服务器，主要是因为我对它非常熟悉。 [NGINX](http://nginx.org/) 是一个得到好评的替代品。目前，我坚持使用 Apache。这两个服务器都符合本文的下一个假设，即:我们将使用[乘客](http://www.modrails.com/)。

乘客给了我们一个主流的部署流程。没有特殊的服务器配置或端口管理。一个新的版本只是上传文件和重启应用服务器(Mongrel，Unicorn 等)的一个例子。).

只是一个关于重启这些服务器的简短说明。Passenger 在您的应用程序目录中查找一个名为`tmp/restart.txt`的文件，告诉它何时重启应用服务器。手动重启将是`touch tmp/restart.txt`。

## 用卡皮斯特拉诺烹饪

这些天来，我真的很饿了谈 devops。当我构建 Capistrano 脚本时，我正在开发一个**食谱**,告诉 Capistrano 我将如何准备我的 web 应用程序(可能是半生熟？).额外的 [Sriracha](http://theoatmeal.com/comics/sriracha) 里没有溅水取暖。这份食谱完全符合要求。如果它无法完成部署，Capistrano 会让您知道并清理碗碟。

Capistrano 非常适合 Rails 应用程序，但几乎可以用于任何应用程序。这个应用程序甚至不需要基于 Ruby。然而，在本例中，我们将使用一个 Rails 应用程序来烹饪。

像往常一样，将`gem capistrano`添加到您的 Gemfile 并运行`bundle install`。现在，我们可以通过运行`capify .`来“资本化”我们的项目。这将在我们应用程序的 *config* 目录中创建一个 Capfile 和一个部署脚本。

我们将在`config/deploy.rb`文件中创建部署方案。查看`deploy.rb`文件，我们看到 Capistrano 已经很好地让我们开始了。

```
set :application, "set your application name here"
set :repository,  "set your repository location here"

set :scm, :subversion

# Or: `accurev`, `bzr`, `cvs`, `darcs`, `git`, `mercurial`, `perforce`, `subversion` or `none`

role :web, "your web-server here"                          # Your HTTP server, Apache/etc
role :app, "your app-server here"                          # This may be the same as your `Web` server
role :db,  "your primary db-server here", :primary => true # This is where Rails migrations will run
role :db,  "your slave db-server here"

# if you're still using the script/reaper helper you will need

# these http://github.com/rails/irs_process_scripts

# If you are using Passenger mod_rails uncomment this:

# namespace :deploy do

# task :start do ; end

# task :stop do ; end

# task :restart, :roles => :app, :except => { :no_release => true } do

# run "#{try_sudo} touch #{File.join(current_path,'tmp','restart.txt')}"

# end

# end
```

您生成的`deploy.rb`文件应该看起来如上所示(在撰写本文时，Capustrano 的最新稳定版本是 2.9.0)。这为我们的食谱提供了一点先机。

所以让我们换成更自制的东西。首先，我们需要设置一些 SSH 配置、关于应用程序的信息、它将被部署到哪里的细节以及一些 SCM 细节。

```
ssh_options[:forward_agent] = true

require 'json'
set :user_details, JSON.parse(IO.read('dna.json'))

set :domain, "capdemo.bangline.co.uk"

set :application, domain
set :user, user_details['name']
set :password, user_details['password']
set :deploy_to, "/home/#{user}"
set :use_sudo, false

set :repository, "git@github.com:bangline/capdemo.git"
set :scm, :git
set :branch, "master"

set :deploy_via, :remote_cache

role :app, domain
role :web, domain
role :db, domain, :primary => true

# If you are using Passenger mod_rails uncomment this:

# namespace :deploy do

# task :start do ; end

# task :stop do ; end

# task :restart, :roles => :app, :except => { :no_release => true } do

# run "#{try_sudo} touch #{File.join(current_path,'tmp','restart.txt')}"

# end

# end
```

`ssh_options[:forward_agent]`确保我们使用本地机器上的密钥，而不是服务器上的密钥。我这样做是因为我通常不在服务器上放置密钥来访问 GitHub，但这样做是完全可行的(如果你是，请删除这一行)。

然后我解析一个名为`dna.json`的文件来获取用户凭证。我不仅可以从公共 git repo 中省略这个敏感文件，还可以用它来提高菜谱的可重用性。例如，我们也可以在 dna 文件中设置所有的配置管理细节。dna.json 文件的内容如下所示:

```
{
  "name":"chuck_norris",
  "password":"dont_need_one_as_the_server_knows_and_fears_me"
}
```

接下来的几行解释得很清楚。我们设置应用程序名称、服务器上的用户凭证、git 配置，最后是应用程序的部署位置。

我应该在这里指出，我是**而不是**使用 SSH 访问的密钥。如果设置了用户密码，那么 Capistrano 将在整个部署过程中使用该密码。这对于这个场景来说没问题，但是如果我们跨多个服务器部署，Capistrano 会假设所有服务器的密码都是相同的。换句话说，对于多服务器部署，使用 SSH 密钥。这不是最安全的方法，但是非常灵活。只要确保你的密码看起来像一只猫在你的键盘上打盹。

我还将`:deploy_via`设置为`:remote_cache`。这会在服务器本身上创建一个 git repo，防止在每次部署时对应用程序进行完整克隆。

定义描述了我们的应用程序的层次。`:app`层是我们在开发中最常用的层，`:web`层是所有请求的目的地，`:db`层是我们希望运行迁移的地方。这种配置风格看起来很傻，因为我们只使用了一个盒子(关键字`server`解决了这个问题)，但是如果我们需要扩展和分离数据库等等，那么这种风格更容易维护。

在这一点上可以做一些检查。如果您在终端中运行`cap -T`命令，您将看到 Capistrano 已经知道了哪些任务。此时，我们要设置应用程序目录并检查权限是否正确。

```
cap deploy:setup
cap deploy:check
```

## Capistrano 布局

在继续下一步之前，我们可以检查一下服务器的布局。如果我们 SSH 到框中并检查应用程序路径，我们应该会看到:

*   目前的
*   放
*   共享的

当前只是一个指向 releases 文件夹中最新版本的符号链接。有了这个常量，我们就可以将 apache vhost 配置文件设置为

```
<VirtualHost *:80>

# Admin email, Server Name (domain name) and any aliases
  ServerName capdemo.bangline.co.uk

# Index file and Document Root (where the public files are located)
  DocumentRoot /home/capdemo/current/public
  <Directory /home/capdemo/current/public>
        Options -MultiViews
        AllowOverride All
  </Directory>

</VirtualHost>
```

releases 目录保存了我们使用 Capistrano 进行的所有发布。当前的符号链接指向这里最新的目录。版本目录将保存所有以前的版本。我们可以使用`set :keep_releases, 5`来限制发布的数量，或者使用 Capistrano 任务`cap deploy:cleanup`。

共享目录在部署中是持久的，所以将用户上传的资产或 sqlite 数据库之类的项目放在这个目录中。

## 写食谱

到目前为止，我们所做的就是检查第一次部署的所有准备工作。`cap deploy:check`的输出应该告诉我们一切看起来都很好。如果没有，您可能需要检查用户的权限是否正确。记得我告诉过你清空`deploy.rb`文件吗？嗯，事实是，使用 Capistrano 与乘客是如此容易，这几乎是意料之中的。我们在`deploy.rb`中留下了一些注释掉的代码，现在我们需要它。

```
namespace :deploy do
  task :start do ; end

task :stop do ; end
task :restart, :roles => :app, :except => { :no_release => true } do
    run "#{try_sudo} touch #{File.join(current_path,'tmp','restart.txt')}"
  end
end
```

这些是我们添加的第一批部署任务。我们覆盖了默认的 start`,`stop`and`restart`tasks to be specific for our setup. The`restart`task can be called using`cap deploy:restart`and you can see it touches the`tmp/restart . txt `文件。更重要的是对任务的描述。

我们有一个名称空间，`deploy`和一些子任务。当我们调用`cap deploy:restart`时，唯一执行的任务是`restart`。在终端中调用`cap deploy`将运行我们在名称空间下看到的所有任务和一些我们看不到的任务。在`cap deploy`任务下有一组存储的/默认的任务。其中的细节已经很好地[在这里说明](https://github.com/mpasternacki/capistrano-documentation-support-files/raw/master/default-execution-path/Capistrano%20Execution%20Path.pdf)。我们感兴趣的比特，按照它们被调用的顺序，是:

*   deploy:update _ code——从 git 存储库中提取代码
*   部署:符号链接–将最新版本链接到当前版本
*   部署:重启——我们已经覆盖了它，只接触了 restart.txt 文件

另一个考虑是我们正在开发一个 Rails 3.1 应用程序。我们想要使用 [bundler](http://gembundler.com/) 来管理我们的宝石依赖(我们为什么想要别的东西？)幸运的是，bundler 有一个针对 capistrano 的部署任务。

```
require "bundler/capistrano"
```

只需将这一行添加到我们的`deploy.rb`中，就可以在部署时捆绑我们所有的 gem。它也以一种聪明的方式完成这项任务。所有的宝石都打包到`shared/bundle`目录中。这是相当于冻结我们的宝石的现代。有了这些，我们几乎可以部署了。

因为这将是我们的第一次部署，我们需要从命令行执行一些额外的任务。首先，我们需要迁移数据库。在不侵犯任何版权的情况下，“这是一项任务”。因此，让我们使用`cap deploy:update_code`在服务器上获取一些代码，然后运行`cap deploy:migrate`。此时，我们在服务器上有了代码，一个具有最新模式的数据库，并且我们的 gem 依赖项已经完成。

## 加入额外的配料

正如我之前提到的，我们将部署一个 Rails 3.1 应用程序。随着资产管道而来的是预编译我们的资产的能力。这为我们创建自己的部署任务提供了一个完美的借口。

```
namespace :assets do
  task :compile, :roles => :web, :except => { :no_release => true } do
    run "cd #{current_path}; rm -rf public/assets/*"
    run "cd #{current_path}; bundle exec rake assets:precompile RAILS_ENV=production"
  end
end

before "deploy:restart", "assets:compile"
```

这个任务只是简单地删除任何现有的资产，并运行 rake 命令来编译它们。更有趣的部分是我放在底部的钩子。由于 DSL Capistrano 的强大功能，您可能已经知道它的功能了，但是基本上它在重启应用服务器之前，会挂钩到`deploy`任务并运行我们的`assets:compile`任务。此外，通过将它拆分成自己的名称空间，我们可以从命令行孤立地运行它`cap assets:compile`。

在部署之前，我们还需要做最后一件事。还记得我们说过共享目录是保存 sqlite 数据库的好地方吗？我们的`database.yml`文件中的当前配置仍然在使用`db/production.sqlite3`。对此最简单的解决方法是将其改为`/home/capdemo/shared/production.sqlite3`。我们将它提交给我们的 GitHub repo，并运行`cap deploy:update_code`和`cap deploy:migrate`。现在我们有了跨部署的持久数据库，我们可以实际部署应用程序了，只需:

```
cap deploy
```

如果您跟踪部署的输出，您将看到编译任务在重启任务之前被执行。诚然，输出看起来不正确，但是如果你花一点时间阅读它，你会看到它正在做我们所期望的。

```
* executing `deploy:restart'
triggering before callbacks for `deploy:restart'
* executing `assets:compile'
```

编写自己的任务是了解 Capistrano 内部情况的好方法。关于任务需要记住的几件事是，它们是用普通的 Ruby 编写的，所以你可以使用所有常用的习惯用法。另一个是 Capistrano 给了你一套很好的配置变量，比如`current_path`、`shared_path`。一个名为[的家伙 Eric Davis](http://theadmin.org/articles/capistrano-variables/) 编制了一份完整的名单。

为了巩固这些要点，我们将研究如何再构建一个自定义任务。我喜欢查看我的部署历史，因此利用我们对共享目录和 capistrano 部署过程的了解，我们可以构建一个部署日志文件。

```
task :log_deploy do
  date = DateTime.now
  run "cd #{shared_path}; touch REVISION_HISTORY"
  run "cd #{shared_path};( echo '#{date.strftime("%m/%d/%Y - %I:%M%p")} : Vesrion #{latest_revision[0..6]} was deployed.' ; cat REVISION_HISTORY) > rev<em>tmp &amp;&amp; mv rev_tmp REVISION_HISTORY"
end

task :history do
  run "tail #{shared_path}/REVISION_HISTORY" do | ch, stream, out |
    puts out
  end
end
after "deploy", "deploy:log_deploy"
```

从现在开始，部署就一帆风顺了。我们只是使用`cap deploy`或`cap deploy:migrations`，这取决于我们是否需要为一个版本更新 db 模式。如果我们想查看部署历史，我们只需调用`cap deploy:history`来获得我们所有部署的日期、时间和版本的输出。

## 品味我们的部署

希望现在您有兴趣使用 Capistrano 进行自己的部署。我试图使这个演练尽可能详细。一开始我不太愿意使用卡皮斯特拉诺，因为我对自己的壳技能不是很有信心，而且我害怕失去人类的控制。但是我太傻了。

我不仅免去了手动部署的痛苦，还获得了 Capistrano 提供的所有额外好处，比如回滚功能、自动禁用页面等等。Capistrano 仍然有大量的标准特性，其细节可以在文档中找到[。也有大量的食谱供你借鉴和发展。](https://github.com/capistrano/capistrano/wiki/)

是的，与使用 Heroku 这样的工具相比，我们确实需要投入更多的时间来开发我们的部署。然而，写了一些食谱后，它会成为第二天性，也许，一个便宜的 VPS 会开始看起来更有吸引力。GitHub 上有[源](https://github.com/bangline/capdemo)。

## 分享这篇文章