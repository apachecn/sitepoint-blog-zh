# 如何使用 Capifony 部署 Symfony 应用程序

> 原文：<https://www.sitepoint.com/deploy-symfony-apps-capifony/>

假设您有一个 Symfony 应用程序。在某种程度上，您希望将它部署到您的服务器上，并向全世界展示。当然，你可以手动完成这一切，但现在你也可以选择使用类似于 [Capifony](http://capifony.org/) 的工具。

如果你过去开发过 Ruby 应用程序，你可能会对 [Capistrano](http://capistranorb.com/) 很熟悉。Capistrano 是一个将 Ruby 应用程序部署到服务器的工具。Capifony 是在 Capistrano 的基础上创建的，基本上是部署方案的集合。

在本文中，我们将使用 Capifony 在服务器上部署一个 Symfony 应用程序。

## Capifony 是如何工作的？

在我们开始之前，了解 Capifony 的工作原理很重要。通过运行 deploy 命令，Capifony 运行某些执行不同任务的命令。例如，它将下载 composer，安装依赖项并清除缓存。

目录结构非常重要。Capifony 需要两个目录和一个符号链接。它需要的第一个目录叫做`releases`。每次部署时，都会在该目录中创建一个新目录。Capifony 获取您的 git 存储库，并在这个新创建的目录上运行所有命令。

第二个目录名为`shared`。您可以想象一些目录在不同版本之间是共享的。例如，如果您允许人们上传图像，您会希望确保这些文件在不同版本之间共享。这些目录和文件通常存储在`shared`目录中。

在这两个目录旁边，我们有一个名为`current`的符号链接。这个符号链接指向最新的成功发布。因此，当您部署一个新版本时，将在`releases`目录中创建一个新目录。如果这个目录上的所有任务都成功了，那么`current`符号链接将指向这个新版本。
您应该指示您的 web 服务器从该符号链接中读取，以便它始终使用正确的最新版本。

## 安装 Capifony

让我们跳过理论部分，开始部署吧。为此，我们需要安装 Capifony。在继续之前，请确保您的系统上安装了 Ruby。你可以通过运行`gem install capifony`来安装 Capifony gem。

在您的应用程序目录中，运行命令`capifony .`。这个命令将在根目录下创建一个名为`Capfile`的文件，并在配置目录下创建一个`deploy.rb`。在本文中，您将修改`deploy.rb`文件，所以请确保在您最喜欢的编辑器中打开它。

现在您必须决定您的部署策略。您可以选择让您的生产服务器访问您的 SCM(源代码控制管理),或者您的本地计算机从 SCM 获取您的存储库并将其复制到您的生产服务器。

在本文中，我们将着眼于第一个策略。如果对第二种策略感兴趣，可以看看 Capifony 官方[网站](http://capifony.org/)的说明。

## 配置您的项目

我将使用这个项目并将它部署到一个生产服务器上。我在我的机器上签出了应用程序，所以是时候运行`capifony .`了。

```
$ capifony .
[add] writing './Capfile'
[add] writing './app/config/deploy.rb'
[done] symfony 2 project capifonied!
```

当您打开`deploy.rb`脚本时，您将看到以下内容。

```
set :application, "set your application name here"
set :domain,      "#{application}.com"
set :deploy_to,   "/var/www/#{domain}"
set :app_path,    "app"

set :repository,  "#{domain}:/var/repos/#{application}.git"
set :scm,         :git
# Or: `accurev`, `bzr`, `cvs`, `darcs`, `subversion`, `mercurial`, `perforce`, or `none`

set :model_manager, "doctrine"
# Or: `propel`

role :web,        domain                         # Your HTTP server, Apache/etc
role :app,        domain, :primary => true       # This may be the same as your `Web` server

set  :keep_releases,  3

# Be more verbose by uncommenting the following line
# logger.level = Logger::MAX_LEVEL
```

是时候改变这个配置文件了。我们从 4 个最重要的参数开始。首先，我们定义应用程序的名称、要部署到的域、目录以及应用程序路径。如果你正在使用默认的 Symfony 设置，应用程序路径将已经正确配置。到目前为止，我的配置是这样的。

```
set :application, "Jumph"
set :domain,      "peternijssen.nl"
set :deploy_to,   "/srv/www/jumph"
set :app_path,    "app"
```

让我们配置我们的存储库。由于我们使用的是 git 存储库，我们应该将 SCM 设置为 git，并将存储库指向我们的 Github 存储库。

```
set :repository,  "git@github.com:jumph-io/Jumph.git"
set :scm,         :git
```

接下来，我们定义我们的模型管理器。在我的例子中，我使用的是原则，但是如果你使用的是推进，你应该把配置值改为“推进”。

我们可以跳过角色。他们只是在重复使用你的域名。

最后一个设置是`keep_releases`设置。通过这个设置，您可以定义想要保留多少个版本，从而允许您回滚到以前的版本。

到目前为止，我们已经将所有默认的配置变量更改为正确的值。然而，Symfony 需要部署更多的配置。在我的例子中，我同时使用 Assetic 和 Composer。这意味着我必须将以下设置添加到文件中。

```
set :dump_assetic_assets, true
set :use_composer, true
```

现在我们需要配置共享文件。例如，您的`parameters.yml`应该在每个版本之间共享。其次，明智的做法是在不同版本之间分享你的上传，你的日志和你的供应商。如果您没有在每个版本之间共享您的供应商，这意味着您的部署每次都安装所有供应商。要设置这些共享路径，我们只需添加以下配置。

```
set :shared_files,      ["app/config/parameters.yml"]
set :shared_children,     [app_path + "/logs", web_path + "/uploads", "vendor", app_path + "/sessions"]
```

注意，在我的例子中，我还将会话目录移到了缓存目录之外。这样我就可以在不同版本之间共享这个目录，没有人会在新的部署中被注销。请注意，您需要更改 Symfony 中的配置，以反映这一变化。

```
session:
    save_path: "%kernel.root_dir%/sessions/"
```

### 配置您的服务器

到目前为止，我们的 Symfony 应用程序已经准备就绪。现在是时候为我们的服务器配置一切了。我们在与上面相同的配置文件中这样做。

部署时，Capifony 以 root 身份运行。如果您希望以自己的用户身份运行它，可以在配置中添加以下几行。

```
set :use_sudo,      false
set :user, "peter"
```

确保您的 web 服务器用户能够写入某些目录也很重要。这可以通过添加以下设置来实现。

```
set :writable_dirs,       ["app/cache", "app/logs", "app/sessions"]
set :webserver_user,      "www-data"
set :permission_method,   :acl
set :use_set_permissions, true
```

注意:你可能需要在你的服务器上安装某些软件包。有关权限的更多信息，请查看[这个](http://capifony.org/cookbook/set-permissions.html)页面。

现在我们可以告诉 Capifony 在您的服务器上准备目录。我们可以通过运行`cap deploy:setup`来做到这一点。当然，一定要确保您可以通过 SSH 访问服务器，并且目录对您的用户是可写的。

*注意:在我的例子中，由于一个[已知问题](http://capifony.org/#known-issues)，我不得不将`default_run_options[:pty] = true`添加到我的配置中。*

运行该命令后，您会注意到它在您的服务器上创建了`releases`和`shared`目录。

现在，您应该能够通过在命令行上运行`cap deploy`来进行部署。如果遇到任何问题，您可以将下面一行添加到您的配置文件中，以获得有关错误的更多信息。

```
logger.level = Logger::MAX_LEVEL
```

在我的例子中，由于一个无效的公钥，我无法访问 git 存储库。因为我的本地计算机可以访问存储库，所以我只需将我的 SSH 代理转发到服务器。这可以通过在部署脚本中添加下面一行来完成。

```
ssh_options[:forward_agent] = true
```

因为这是您的第一次部署，Capifony 会要求您提供`parameters.yml`文件的凭证。您只需填写一次，因为我们将文件配置为跨版本共享。

### 添加附加命令

如果您尝试部署我前面提到的存储库，您会注意到当 Assetic 尝试转储它的文件时，它会失败。这是因为我通过 [Bower](http://bower.io/) 管理我的 JavaScipt 和 CSS 依赖项。所以在 Assetic 转储文件之前，我应该首先运行`bower install`。

Capifony 默认不支持 bower，所以我们必须扩展 Capifony 执行的任务列表。我们通过将任务添加到配置文件来添加一个额外的任务。

```
before 'symfony:assetic:dump', 'bower:install'

namespace :bower do
    desc 'Run bower install'
    task :install do
      capifony_pretty_print "--> Installing bower components"
      invoke_command "sh -c 'cd #{latest_release} && bower install'"
      capifony_puts_ok
    end
end
```

这项任务很容易理解。首先，我们定义任务应该何时运行。在这种情况下，我们希望在 Assetic 转储其文件之前运行它。接下来，我们定义它应该运行哪个任务。

我们需要做的最后一件事是定义这个新任务。我们通过在名称空间内创建一个任务并记下要运行哪个命令来实现这一点。在这个任务中，我们首先确保我们在正确的目录中，然后运行`bower install`。

此外，我在命令前后添加了一些输出。这样，它会在运行时显示在`cap deploy`命令中。它给了你一些额外的反馈，如下图所示。

```
$ cap deploy
--> Updating code base with checkout strategy
--> Creating cache directory................................✔
--> Creating symlinks for shared directories................✔
--> Creating symlinks for shared files......................✔
--> Normalizing asset timestamps............................✔
--> Downloading Composer....................................✔
--> Installing Composer dependencies........................✔
--> Installing bower components.............................✔
--> Dumping all assets to the filesystem....................✔
--> Warming up cache........................................✔
--> Clear controllers.......................................✔
--> Setting permissions.....................................✔
--> Successfully deployed!
```

### 附加命令

一开始，我们决定至少发布 3 个版本。如果在新版本中出现问题，您可以通过运行命令`cap deploy:rollback`回滚。

此外，您还可以通过运行`cap deploy:web:disable`或`cap deploy:web:enable`来激活或停用 Symfony 维护页面。

Capifony 包含更多可能会派上用场的命令。要获得完整的列表，您可以运行`cap -vT`。

### 完整配置

作为参考，这是我们通过本文创建的完整配置文件。

```
set :application, "Jumph"
set :domain, "peternijssen.nl"
set :deploy_to, "/srv/www/jumph"
set :app_path, "app"

set :repository, "git@github.com:jumph-io/Jumph.git"
set :scm, :git

set :model_manager, "doctrine"

role :web, domain
role :app, domain, :primary => true

set :use_sudo, false
set :user, "peter"

set  :keep_releases, 3

set :dump_assetic_assets, true
set :use_composer, true

set :shared_files, ["app/config/parameters.yml"]
set :shared_children, [app_path + "/logs", web_path + "/uploads", "vendor", app_path + "/sessions"]

set :writable_dirs, ["app/cache", "app/logs", "app/sessions"]
set :webserver_user, "www-data"
set :permission_method, :acl
set :use_set_permissions, true

ssh_options[:forward_agent] = true
default_run_options[:pty] = true

before 'symfony:assetic:dump', 'bower:install'

namespace :bower do
    desc 'Run bower install'
    task :install do
      capifony_pretty_print "--> Installing bower components"
      invoke_command "sh -c 'cd #{latest_release} && bower install'"
      capifony_puts_ok
    end
end
```

## 结论

在部署 Symfony 应用程序方面，Capifony 让您的生活更加轻松。虽然我们已经看到了 Capifony 提供的许多选项，但您可能希望深入了解。你可以查看他们的[网站](http://capifony.org/)了解更多信息。您是否使用 Capifony 来部署您的 Symfony 应用程序？如果你遇到任何困难或有任何问题，请告诉我们！

## 分享这篇文章