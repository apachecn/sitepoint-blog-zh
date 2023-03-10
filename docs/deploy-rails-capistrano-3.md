# 使用 Capistrano 3 部署 Rails

> 原文：<https://www.sitepoint.com/deploy-rails-capistrano-3/>

![CapistranoLogo](img/d68a3eaafe0087331fef876caec8218e.png)

Capistrano 是用 Ruby 编写的远程服务器自动化和部署工具。这是一个很棒的工具，它扩展了 rake (ruby make) DSL，可以用来部署任何 web 应用程序。

在本文中，我将把一个 Rails 应用程序部署到 EC2 Ubuntu 实例中。您可以将您的应用程序部署到任何其他的 PaaS 平台，比如 Digital Ocean，使用 ssh 访问。

我使用 Ubuntu 实例进行部署，并且假设您已经有了一个可以远程部署的应用程序。

#### 步骤 1:服务器设置

首先，您需要从 Amazon AWS 控制台创建一个 EC2 实例，ssh 到该实例中，并安装必要的包(数据库、web 服务器等。)来运行您的 web 应用程序。你必须更新 EC2 和 Google App Engine 上的防火墙设置，否则你的应用程序将无法被外界访问。另一件事是将您的域指向您使用域名注册商提供的 DNS 工具创建的实例

我不打算提供一步一步的指示来设置您的服务器。您可以使用这个服务器设置脚本来设置您的服务器，以便部署 Rails 应用程序。

```
$ ssh -i <path-to-pem-file> ubuntu@<ec2-instance-address>
$ wget "https://gist.githubusercontent.com/42races/8d2c597d10dc9a0bddb0/raw/cbc4373b27dc09d2fc88a660ef1ad1b4c246c0e4/server_setup.sh"
$ chmod +x server_setup.sh
$ ./server_setup.sh
```

这个脚本将安装必要的包，创建一个管理我们的 Rails 应用程序的`deploy`用户，下载并安装 RVM 和 Ruby，并将您的 ssh 公钥添加到实例的授权密钥中。

我在这个应用程序中使用 nginx，配置如下。将您的配置放在一个文件(例如:foobar.conf)中，并将其复制到**/etc/nginx/sites-available**目录中。我将使用“foobar”作为我的应用程序名，使用“foobar.com”作为我的应用程序域名地址。

```
upstream foobar {
  # uncomment the following line if multiple application servers are used.
  # this will force nginx to send requests from the same client to the same
  # application server.
  # ip_hash;
  server unix:///home/deploy/apps/foobar/shared/tmp/sockets/puma.sock fail_timeout=0;
}

server {
  listen 80 default deferred;
  server_name foobar.com;
  root /home/deploy/apps/foobar/current/public;

  location ^~ /assets/ {
    # gzip_static on;
    expires max;
    add_header Cache-Control public;
  }

  try_files $uri/index.html $uri @foobar;
  location @foobar {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_pass http://foobar;
  }

  error_page 500 502 503 504 /500.html;
  client_max_body_size 4G;
  keepalive_timeout 10;
}
```

#### 步骤 2:安装 Capistrano

转到您的 Rails 应用程序，在 gem 文件中添加下面一行

```
group :developemnt do
  gem 'capistrano', '~> 3.1.0'
end
```

运行应用程序目录中的`bundle`命令来安装 gem。

```
$ bundle
```

按如下方式检查已安装的 capistrano 的版本

```
$ bundle exec cap -V

Capistrano Version: 3.1.0 (Rake Version: 10.2.2) ###
```

Capistrano 附带了一组默认任务，您可以使用下面的命令列出这些任务。

```
$ bundle exec cap -vT
```

#### 步骤 3:初始化 Capistrano

运行以下命令来初始化 capistrano。

```
$ bundle exec cap install
```

上面的命令在您的应用程序目录中创建几个文件。

```
Capfile
config/deploy.rb
config/deploy/production.rb
config/deploy/staging.rb
lib/capistrano/tasks          # directory
```

如您所见，它为生产和暂存环境生成了不同的配置文件。从版本 3 开始，capistrano 默认为多阶段。有一个名为 **config/deploy.rb** 的全局配置文件，您可以在其中放置所有环境的配置命令。

#### 第 4 步:编辑 Capfile

在您最喜欢的编辑器中打开 **Capfile** ,添加/取消注释以下行

```
require 'capistrano/setup'
require 'capistrano/deploy'
```

Capistrano 已经被重新设计为使用插件。在我们的例子中，我们可以使用 bundler、rvm 和 rails 插件。插件被打包成 gem，所以我们可以将它们添加到 **Gemfile** 中，并在 **Capfile** 中需要它。这是官方[插件](http://capistranorb.com/documentation/plugins)的列表。

我们需要一个应用服务器来运行我们的应用程序，我将使用 [puma](http://puma.io) 。

将此添加到**gem 文件**:

```
group :development do
  gem 'capistrano', '~> 3.1.0'
  # cap tasks to manage puma application server
  gem 'capistrano-puma', require: false
  gem 'capistrano-rails',   '~> 1.1', require: false
  gem 'capistrano-bundler', '~> 1.1', require: false
  gem 'capistrano-rvm',   '~> 0.1', require: false
end
```

并且`bundle`安装这些宝石。在 **Capfile** 中，添加以下几行:

```
require 'capistrano/rvm'
require 'capistrano/bundler'
require 'capistrano/puma'
require 'capistrano/rails/assets' # for asset handling add
require 'capistrano/rails/migrations' # for running migrations
```

#### 步骤 5:全局配置

现在，在您最喜欢的编辑器中打开 **config/deploy.rb** 来设置各种选项，如应用程序名称:

```
set :application, 'foobar'                       # application name
set :repo_url, 'git@example.com:me/foobar.git'   # your repo url
set :deploy_to, '/home/deploy/apps/foobar'
```

设置您正在使用的版本控制:

```
set :scm, :git
```

要部署的分支:

```
set :branch, 'master'
```

您想要保留的版本数量

```
set :keep_releases, 5
```

可以提示用户回答其中的一些问题。例如，如果您想要动态设置 git 分支名称，请执行以下操作:

```
ask :branch, proc { `git rev-parse --abbrev-ref HEAD`.chomp }
```

格式化输出的一些其他选项有:

```
set :format, :pretty
set :log_level, :debug
set :pty, true
```

明智的做法是设置链接目录，以避免在每次部署时覆盖它们:

```
set :linked_dirs, %w{bin log tmp/pids tmp/cache tmp/sockets vendor/bundle public/system}
```

#### 步骤 6:环境配置

打开与所选部署环境对应的文件。因此，如果我们要部署到生产环境，请打开**config/deploy/production . Rb**文件并设置配置选项。如果环境文件不可用，只需创建该文件。

```
set :stage, :production

role :app, %w{deploy@<vps_ip_address>}
role :web, %w{deploy@<vps_ip_address>}
role :db,  %w{deploy@<vps_ip_address>}
```

#### 步骤 8: Puma 设置

有些插件需要自己的配置，Puma 就是其中之一。将以下几行添加到 **config/deploy.rb**

```
set :puma_rackup, -> { File.join(current_path, 'config.ru') }
set :puma_state, "#{shared_path}/tmp/pids/puma.state"
set :puma_pid, "#{shared_path}/tmp/pids/puma.pid"
set :puma_bind, "unix://#{shared_path}/tmp/sockets/puma.sock"
set :puma_conf, "#{shared_path}/puma.rb"
set :puma_access_log, "#{shared_path}/log/puma_error.log"
set :puma_error_log, "#{shared_path}/log/puma_access.log"
set :puma_role, :app
set :puma_env, fetch(:rack_env, fetch(:rails_env, 'production'))
set :puma_threads, [0, 16]
set :puma_workers, 0
set :puma_init_active_record, true
set :puma_preload_app, true
```

#### 步骤 8:部署

要部署您的应用程序，只需运行`deploy`任务:

```
$ bundle exec cap production deploy
```

请注意，您必须在每个`cap`命令中指定环境。

一旦部署完成，在你的浏览器中访问“foobar.com”(或者你叫它什么)。

#### 升级到 Capistrano 3

升级 Rails 应用程序以使用 capistrano 3 非常容易。第一步是备份旧的 capistrano 配置文件:

```
Capfile
config/deploy.rb
```

如果您的应用程序有多个阶段，也要备份它们。

现在从 **Gemfile** 中移除旧的 capistrano gem 以及任何相关插件。此时，只需遵循本文中前面提到的步骤。简单。

我希望这篇文章能帮助你进入 Capistrano 3。

## 分享这篇文章