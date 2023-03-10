# 使用 SemaphoreCI 持续部署轨道

> 原文：<https://www.sitepoint.com/continous-deployment-of-rails-with-semaphoreci/>

![Semaphore](img/1a36f13214f1e9e8689dd1367722373a.png)

我们今天的目标是建立一个 Rails 项目，该项目使用 Semaphore CI 来测试项目，然后使用 Capistrano 自动部署到 Amazon EC2 服务器。我们将使用 Nginx、Puma 和 Postgres，以及 Ruby 2.3.0 和 Rails 4.2.6。

## 什么是 SemaphoreCI？

Semaphore 可以让你为任何 web 项目快速建立一个快速连续的交付过程。您可以测试您所做的每一项更改，并轻松地部署新版本以取悦您的用户。SemaphoreCI 允许您通过与其他服务集成，让您的团队呆在厕所里。它现在被全球数百家公司使用和称赞。你可以在这里阅读更多关于[的信息。](https://semaphoreci.com/product)

## 竞争情报的好处

持续集成的一些主要优势是:

*   它让每个人都了解健康状况。SemaphoreCI 让每个人都知道构建是否失败，哪个提交导致了构建失败，以及是谁提交的。
*   允许您持续、平稳地推出新功能。当新代码被合并到指定的分支中时，测试套件就会运行，如果所有测试都通过了，就会自动触发部署。

## 程序

确保你已经安装了 **RVM** 。我运行的是 **Ruby 2.3.0** 和 **Rails 4.2.6** 。

### 创建新的 Rails 应用程序

在终端中键入以下内容。我添加了 **-d postgresql** 标志，让生成器知道我们想要使用 **Postgres** 作为 DB:

```
$ rails new ci-test-and-deploy-to-ec2-capistrano -d postgresql 
```

光盘放入项目目录:

```
$ cd ci-test-and-deploy-to-ec2-capistrano 
```

创建数据库:

```
$ rake db:create 
```

为我们的项目生成一个**脚手架**，它将创建**模型**、**控制器**、**视图**和**路线**。我们将资源命名为 **Blog** 并提供字段名。这也将生成一个迁移来创建一个`blogs`数据库表:

```
$ rails generate scaffold blog author title body:text 
```

迁移数据库:

```
$ rake db:migrate 
```

将这一行添加到 **config/routes.rb** 中。这将设置应用程序的根目录。

```
root 'blogs#index' 
```

通过在终端中运行以下命令来启动您的应用服务器:

```
$ rails server 
```

访问 [http://localhost:3000](http://localhost:3000) 以验证我们的应用程序设置是否正常。您应该会看到这个屏幕:

![](img/ea7919dd560516ec632fcfc1dfa6443c.png)

完美！我们的基本应用程序设置运行良好。

### 对 BitBucket 上的项目进行版本控制

#### 为什么我选择 Bitbucket 而不是广泛采用的 GitHub？

定价。Bitbucket 给你无限的免费仓库。几乎所有与 GitHub 相同的强大功能，加上灵活的定价。我们将使用它，因为我想展示人们如何快速启动一个免费的受版本控制的私有项目，并且能够确保 SemaphoreCI 的持续免费交付(每月 100 个私有构建)。

然而，如果你想使用 Github，我们在这里所做的一切都可以在这个平台上轻松完成。

#### 让我们从 BitBucket 开始吧

在 [BitBucket 上注册。](https://bitbucket.org/)

创建并命名您的回购。这里，我们将使用我们在前面的步骤中创建的示例项目/应用程序，并将其推送到这个新创建的存储库中

您应该已经在终端窗口的项目目录中了。`cd`如果您还没有进入目录:

```
$ cd /path/to/my/repo 
```

在项目目录中初始化一个空的 git 存储库:

```
$ git init
Initialized empty Git repository in /Users/sarmad/projects/ci-test-and-deploy-to-ec2-capistrano/.git/ 
```

现在运行这些命令。不要忘记将`git@bitbucket.org:sarmadsabih/ci-test-and-deploy-to-ec2-capistrano.git`替换为存储库中显示的那个:

```
$ git remote add origin git@bitbucket.org:sarmadsabih/ci-test-and-deploy-to-ec2-capistrano.git 
```

添加并提交所有文件:

```
$ git add -A && git commit -m "first commit" 
```

最后推进到您的回购的分支`master`:

```
$ git push origin master 
```

刷新存储库页面，您应该会看到概述页面。您的回购现已在 Bitbucket 上线。

### 配置 Amazon EC2 服务器

我们现在需要为我们的应用程序设置一个 Amazon EC2 服务器。注册[亚马逊 AWS](https://aws.amazon.com/console/) 它有一个免费层，适用于注册后的一年。免费层为您提供每月 750 小时(整整一个月)的 t2.micro 实例，直到您的免费层到期。

登录 AWS 管理控制台，单击 EC2 进入 EC2 仪表板:

![aws-management-console](img/798cf35bef6765bcebf5483392dc335d.png)

您将被带到 EC2 仪表板。点击“启动实例”按钮:

![aws-ec2-dashboard](img/99a0b0094a295e80baac55cb5efb5eba.png)

选择 Ubuntu 14.04 LTS 64 位作为操作系统:

![aws-ec2-ami-selection](img/56a98cb8906dbbe0b11abda506d505ad.png)

选择符合空闲层条件的 **t2.micro** 实例。根据您的计算要求，您可以选择不同的实例。单击“下一步:配置实例详细信息”:

![aws-ec2-instance-type](img/8f3a89311f6bcbc60481ad8007ed1cc3.png)

默认设置在这个页面上会很好。只有当你知道自己在做什么，并且这是你的要求时，才改变价值观。单击“下一步:添加存储”:

![aws-ec2-instance-details](img/9b90f9ac1dfc0834ea8a97324c85d191.png)

保持原样，然后单击“下一步:标记实例”:

![aws-ec2-storage](img/0d2545aa3522803ce6c6a942542950e9.png)

命名实例并单击“下一步:配置安全组:

![aws-tag-instance](img/124e58e132fd8e4c2565b9abf2181a1e.png)

查看您选择的设置/选项，然后单击“启动”:

![aws-ec2-review-instance-launch](img/46e907ba6a1db06a18e71a4f556c5893.png)

**注意这一步！**从第一个下拉菜单中选择“创建新的密钥对”。在输入字段中输入密钥对的名称，然后下载密钥对。**警告！**在此屏幕之后，您不能再次下载密钥对。如果您丢失了这个键，您将永远无法 ssh 到您的 Amazon EC2 实例(不完全正确，但是这样做很痛苦)。所以，下载密钥对(。pem 文件)并妥善保存。

单击“启动实例”以最终启动实例:

![aws-ec2-create-new-keypair](img/8b36d91f722935653d77b37240724291.png)

您的实例已启动:

![aws-ec2-instance-launched](img/7aaca7e09c03d2edf81afaf6d1d9ccf7.png)

这是列出所有实例的页面。验证您的实例处于“正在运行”状态，并且所有状态检查都已通过:

![aws-ec2-instances](img/87b1386b8afbd20191f33ed4ea02b268.png)

选择您的实例，您将能够在页面底部区域看到该实例的公共 IP。复制公共 IP，我们很快就会用到它:

![aws-ec2-instances-public-ip](img/8593bd993e962f278d0f8a292db66c31.png)

`cd`到你所在的位置。pem 文件已找到。我的在我的**下载**目录下。如果您的 pem 文件位置不同，请修改以下命令:

```
$ cd ~/Downloads 
```

使用`pwd`命令获取目录的完整路径:

```
 $ pwd
  /Users/sarmad/Downloads 
```

您将使用下面的路径。不要忘记用你自己的替换路径和文件名。此外，将下面使用的公共 IP 替换为您的 EC2 实例的公共 IP，即您在前面的步骤中复制的 IP:

```
$ ssh -i "/Users/sarmad/Downloads/rails-ci-testing-deployment.pem" ubuntu@54.210.115.143
The authenticity of host '54.210.115.143 (54.210.115.143)' can't be established.
ECDSA key fingerprint is SHA256:pvhV+Nlz8erA0/crpI7QTNvYigQY2KYNoXDTcaifyBE.
Are you sure you want to continue connecting (yes/no)? 
```

键入“yes”并按回车键。您应该看到:

```
Warning: Permanently added '54.210.115.143' (ECDSA) to the list of known hosts. @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@         WARNING: UNPROTECTED PRIVATE KEY FILE!          @ @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
Permissions 0644 for '/Users/sarmad/Downloads/rails-ci-testing-deployment.pem' are too open. 
```

正如消息所暗示的，您的。pem 文件太开放，所以我们需要解决这个问题。运行以下命令:

```
$ chmod 400 /Users/sarmad/Downloads/rails-ci-testing-deployment.pem 
```

这将解决问题。现在，再次运行`ssh`命令:

```
$ ssh -i "/Users/sarmad/Downloads/rails-ci-testing-deployment.pem" ubuntu@54.210.115.143 
```

它应该会让您登录到 EC2 实例。首先更新现有包:

```
ec2$ sudo apt-get update && sudo apt-get -y upgrade 
```

添加一个新用户，该用户将用于由 Capistrano 进行部署。您可以随意命名用户:

```
ec2$ sudo useradd -d /home/sarmad -m sarmad 
```

为新用户设置密码

```
ec2$ sudo passwd sarmad
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully 
```

现在让我们授予新用户`sudo`权限:

```
ec2$ sudo visudo 
```

将这一行粘贴到打开的文件中:

```
sarmad ALL=(ALL:ALL) ALL 
```

点击 **CTRL+o** ，按回车键和 **CTRL+x** 保存并退出。

使用以下命令以新用户身份登录

```
ec2$ su - sarmad 
```

输入密码。这将使您作为`sarmad`(或者您给它起的名字)用户登录:

用`ssh-keygen`创建一个 SSH 密钥。对于默认值，在每次提示时按 Enter 键:

```
ec2$ cat .ssh/id_rsa.pub 
```

您需要将 EC2 公钥添加到您的 Bitbucket 帐户，以便 EC2 用户可以访问您的回购..点击[此](https://confluence.atlassian.com/bitbucket/set-up-ssh-for-git-728138079.html)和此链接，了解更多关于向 Bitbucket 帐户添加 SSH 密钥的信息。

#### 复制 EC2 私钥以备后用

现在复制 EC2 `sarmad`用户的私钥:

```
ec2$ cat id_rsa
-----BEGIN RSA PRIVATE KEY-----
some_long_random_string
-----------------------------
-----------------------------
-----------------------------
-----------------------------
-----END RSA PRIVATE KEY----- 
```

把这个抄在某个地方，因为我们以后会需要它。

### 将开发机器的公钥复制到 EC2

我们现在需要将本地开发机器的公钥复制到 EC2。Capistrano 将使用它作为`sarmad`用户(我们为部署创建的用户)SSH 到 EC2 实例。在 EC2 终端上运行以下命令:

```
ec2$ nano .ssh/authorized_keys 
```

将本地开发机器的公钥粘贴到该文件中。按 **CTRL+o** ，回车，然后按 **CTRL+x** 保存并退出。本地开发机器上的 Capistrano 现在可以作为这个用户 SSH 到部署服务器。

### 配置 EC2 环境

#### 安装 Ruby

虽然我们可以直接安装 Ruby，但它被广泛采用，并建议我们使用某种 Ruby 版本管理器。有两个受欢迎的选择。 [Rbenv](http://rbenv.org/) 和 [RVM](https://rvm.io/) 。我们将使用 RVM。使用以下命令安装 RVM:

```
ec2$ gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
ec2$ \curl -sSL https://get.rvm.io | bash -s stable 
```

根据此安装后消息获取 RVM:

```
* To start using RVM you need to run `source /home/sarmad/.rvm/scripts/rvm` in all your open shell windows, in rare cases you need to reopen all shell windows. 
```

奔跑

```
ec2$ source /home/sarmad/.rvm/scripts/rvm 
```

安装最新稳定的 MRI Ruby:

```
ec2$ rvm install ruby 
```

在这种情况下，安装的是 Ruby-2.3.0。也可以通过这个命令`rvm install 2.3.0`安装 Ruby 2.3.0。

我们需要安装邦德勒宝石

```
ec2$ gem install bundler --no-ri --no-rdoc 
```

创建项目目录

```
ec2$ mkdir rails-ci-testing-and-deployment-to-ec2-capistrano 
```

#### 安装 NGINX

[你可以在这里](https://www.nginx.com/)阅读更多关于 NGINX 的内容。

```
ec2$ sudo apt-get install nginx 
```

输入`sarmad`的密码，并按回车键确认。现在，配置默认站点。打开站点配置文件:

```
ec2$ sudo nano /etc/nginx/sites-available/default 
```

删除现有内容并粘贴以下内容:

```
upstream app {
  # Path to Puma SOCK file, as defined previously
  server unix:///home/sarmad/rails-ci-testing-and-deployment-to-ec2-capistrano/shared/tmp/sockets/puma.sock fail_timeout=0;
}

server {
  listen 80;
  server_name localhost;

  root /home/sarmad/rails-ci-testing-and-deployment-to-ec2-capistrano/current/public;

  try_files $uri/index.html $uri @app;

  location / {
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header Host $host;
    proxy_redirect off;
    proxy_http_version 1.1;
    proxy_set_header Connection '';
    proxy_pass http://app;
  }

  location ~ ^/(assets|fonts|system)/|favicon.ico|robots.txt {
    gzip_static on;
    expires max;
    add_header Cache-Control public;
  }

  error_page 500 502 503 504 /500.html;
  client_max_body_size 4G;
  keepalive_timeout 10;
} 
```

请慢慢阅读这个配置文件以理解配置。很简单，真的。

#### Install Git

```
ec2$ sudo apt-get install git 
```

接受默认值。

#### 安装 PostgreSQL

```
ec2$ sudo apt-get install postgresql postgresql-contrib libpq-dev 
```

再次接受默认值。您应该看到以下内容:

```
* Starting PostgreSQL 9.3 database server                                 [ OK ]
Setting up postgresql (9.3+154ubuntu1) ...
Setting up postgresql-contrib-9.3 (9.3.13-0ubuntu0.14.04) ...
Setting up postgresql-contrib (9.3+154ubuntu1) ...
Processing triggers for libc-bin (2.19-0ubuntu6.9) ... 
```

创建一个数据库用户。我给我的取名为`sarmad`，但是你可以给它起别的名字:

```
ec2$ sudo -u postgres createuser -s sarmad 
```

使用以下命令登录 PSQL 控制台:

```
ec2$ sudo -u postgres psql 
```

更改用户的密码:

```
postgres=# \password sarmad 
```

输入将用于连接数据库服务器的密码。确认密码，我们就准备好了。

退出 postgres 提示符:

```
postgres=# \q 
```

然后按回车键。现在让我们为我们的应用程序创建一个生产数据库:

```
ec2$ sudo -u postgres createdb -O sarmad rails-ci-testing-and-deployment-to-ec2-capistrano_production 
```

回到 Rails 项目，您的 ** config/database.yml* *应该如下所示:

```
default: &default
  adapter: postgresql
  encoding: unicode
  # For details on connection pooling, see rails configuration guide
  # http://guides.rubyonrails.org/configuring.html#database-pooling
  pool: 5

development:
  <<: *default
  database: rails-ci-testing-and-deployment-to-ec2-capistrano_development

test:
  <<: *default
  database: rails-ci-testing-and-deployment-to-ec2-capistrano_test

production:
  <<: *default
  database: rails-ci-testing-and-deployment-to-ec2-capistrano_production
  username: sarmad
  password: sarmad
  host: localhost
  port: 5432 
```

不要忘记替换任何不同于您为自己设置的信息，如数据库名称、用户名或密码。

您可能希望以更保密的方式存储数据库用户名和密码。但是由于我们使用的是私有存储库，并且本教程的范围仅仅是用 Rails 快速设置 CI，所以我们现在可以将这些信息放在这个文件中。

### 为生产创造一个秘密

在您的本地(而不是 EC2)项目目录中运行这个命令来生成一个密码:

```
$ rake secret
80a602e4c5c8c173dd3d00b1492c7f952030552bf747467578be8efdc545d2e8e491497081be4fd4e15627a2e811bf47e89950db6a5162da6932eb2edb204c3a 
```

复制秘密并将其放入 **config/secrets.yml** :

```
production:
  secret_key_base: 80a602e4c5c8c173dd3d00b1492c7f952030552bf747467578be8efdc545d2e8e491497081be4fd4e15627a2e811bf47e89950db6a5162da6932eb2edb204c3a 
```

这又是一个敏感的信息，你可能想保密地存储，但为了保持简单，这将是现在的工作。

### 配置 Capistrano

让我们在项目中配置 Capistrano。卡皮斯特拉诺支持彪马和 RVM。将这些宝石复制到您的宝石文件中:

```
 gem "therubyracer", platforms: :ruby, group: :production
gem "puma"
group :developemnt do
  gem "capistrano3-puma"
  gem "capistrano"
  gem "capistrano-bundler", require: false
  gem "capistrano-rvm"
  gem "capistrano-rails", require: false
end 
```

运行`bundle install`来安装新的 gems。

让我们通过为您的环境生成配置文件来设置 Capistrano，将**阶段**设置为**生产**。这将为**生产**环境部署建立文件。

```
$ cap install STAGES=production
mkdir -p config/deploy
create config/deploy.rb
create config/deploy/production.rb
mkdir -p lib/capistrano/tasks
create Capfile
Capified 
```

这些文件的创建结果是:

*   **config/deploy.rb**
*   **配置/部署/生产. rb**
*   **Capfile**

您的 **Capfile** 应该是这样的(复制以确保正确性):

```
# Load DSL and set up stages
require "capistrano/setup"

# Include default deployment tasks
require "capistrano/deploy"

# Include tasks from other gems included in your Gemfile
#
# For documentation on these, see for example:
#
#   https://github.com/capistrano/rvm
#   https://github.com/capistrano/bundler
#   https://github.com/capistrano/rails
#   https://github.com/capistrano/passenger
#
require "capistrano/rvm"
require "capistrano/bundler"
require "capistrano/rails/assets"
require "capistrano/rails/migrations"
require "capistrano/puma"

# Load custom tasks from `lib/capistrano/tasks` if you have any defined
Dir.glob("lib/capistrano/tasks/*.rake").each { |r| import r } 
```

您的 **config/deploy.rb** 文件应该如下所示:

```
# config valid only for current version of Capistrano
lock '3.4.0'

# Change the application name with your application name
set :application, 'rails-ci-testing-and-deployment-to-ec2-capistrano'

# Change the repository URL with your repository URL
set :repo_url, 'git@bitbucket.org:sarmadsabih/ci-test-and-deploy-to-ec2-capistrano.git'

# Default branch is :master if you need to use some other branch then uncomment the line below
# And change the branch name with the name of the branch you want to use
# set :branch, :some_other_branch_you_may_want_to_use

# Don't forget to change the username (i.e sarmad in this case) with your username
# And the project directory with the name of your project directory
set :deploy_to, '/home/sarmad/rails-ci-testing-and-deployment-to-ec2-capistrano'

set :scm, :git

# Default value for :format is :pretty
# set :format, :pretty

# Default value for :log_level is :debug
# set :log_level, :debug

# Default value for :pty is false
set :pty, true

set :rvm_type, :user

# Edit this if you are using a different Ruby version
set :rvm_ruby_version, 'ruby-2.3.0'

# Default value for linked_dirs is []
set :linked_dirs, fetch(:linked_dirs, []).push('log', 'tmp/pids', 'tmp/cache', 'tmp/sockets', 'vendor/bundle', 'public/system')

# Default value for default_env is {}
# set :default_env, { path: "/opt/ruby/bin:$PATH" }

# Default value for keep_releases is 5
set :keep_releases, 5

# Pume config
set :puma_rackup, -> { File.join(current_path, 'config.ru') }
set :puma_state, "#{shared_path}/tmp/pids/puma.state"
set :puma_pid, "#{shared_path}/tmp/pids/puma.pid"
set :puma_bind, "unix://#{shared_path}/tmp/sockets/puma.sock"    #accept array for multi-bind
set :puma_conf, "#{shared_path}/puma.rb"
set :puma_access_log, "#{shared_path}/log/puma_error.log"
set :puma_error_log, "#{shared_path}/log/puma_access.log"
set :puma_role, :app
set :puma_env, fetch(:rack_env, fetch(:rails_env, 'production'))
set :puma_threads, [0, 8]
set :puma_workers, 0
set :puma_worker_timeout, nil
set :puma_init_active_record, true
set :puma_preload_app, false 
```

好了，我们差不多搞定服务器了。回到您的本地机器，开始使用 Capistrano 进行部署。编辑**config/deploy/production . Rb**来设置服务器 IP。打开文件，将以下内容粘贴到文件中。更改 IP 地址以匹配您的 EC@ server 的 IP:

```
set :stage, :production

# Replace this EC2 server's public IP with your EC2 server's public IP
# Replace this user ('sarmad') with the one you created on your EC2 instance
server '54.210.115.143', user: 'sarmad', roles: %w{web app db} 
```

提交并推送至我们的位存储桶回购:

```
$ git add -A
$ git commit -m "ready for first deployment"
$ git push origin master 
```

现在让我们使用 Capistrano 开始部署:

```
$ cap production deploy 
```

第一次部署需要一些时间。坐着别动。这个过程将部署应用程序，迁移数据库，并启动应用服务器。

SSH 到您的 EC2 实例，并重启 NGINX，以便重新加载我们的设置:

```
ec2$ sudo service nginx restart 
```

转到 http://[您的 EC2 实例 DNS 名称或公共 IP]查看应用程序的启动和运行:

![ec2-production-home-page](img/d1f2d9fd35dc3fd895471113bc9b71b1.png)

### 旗语

[注册](https://semaphoreci.com/users/sign_up)如果你还没有在 SemaphoreCI 上注册，你会在注册后看到这个。点击“添加新项目”:

在以下屏幕上单击您的用户名:

![semaphore-add-project-select-account](img/b8017ea8c0f5db8f58c1cb2f55bf71f6.png)

选择位存储桶:

![sgit](img/2da5540c2f4f8baf9fba42bed0609f47.png)

您将从您的 Bitbucket 帐户中看到一个存储库列表。选择要添加到 SemaphoreCI 的回购。在我们的例子中，我们将选择我们为本教程创建的那个:

![semaphore-select-project-from-bitbucket](img/8725f901095e9e40a8de0f70d59ed814.png)

在我们的例子中选择主分支:

![Select branch](img/9c78cffbc450fc5d34a817a9ca200025.png)

这是您项目的设置页面。将此屏幕中选择的字段与您的项目设置相匹配。Ruby 2.3.0 和 PG 作为数据库:

![semaphore-project-settings](img/3b0384797c20600f38d874c90e8f6945.png)

设置完成后，点击“构建”。您可以在以下屏幕中看到您的构建进度:

![semaphore-build-in-progress](img/b24f10667184e9c16da59998867fd9cb.png)

作为通过测试的结果，构建被通过。如果有失败的测试，构建将会失败，您将会收到一封包含构建失败细节的电子邮件提醒。

![semaphore-build-passed](img/246060aaca1af05a0dcaba363371faf5.png)

我们现在有一个测试服务器设置，每月有 100 个免费的私有构建。现在，让我们设置部署:

![semaphore-project-home](img/7bc87009de29c63058b845183bf75ec3.png)

选择“Capistrano”:

![semaphore-setup-deployment](img/413dd377df905d04c9c542359cb622a0.png)

选择“自动”。每当主分支(或者您选择的任何分支)上有一个成功的构建时，它将触发一个自动部署。

![semaphore-deployment-server-automatic](img/053d53d93438a23d62ab1baa6ae3b96a.png)

选择要构建的分支(主):

![semaphore-deployment-branch](img/62ae84a1a0612b7071501cf98368b6b0.png)

部署命令应该如下所示。注意第二个部署命令中的关键字**生产**:

![semaphore-deployment-commands](img/5f32a47b4cb1e2dc8cf5873338f29d23.png)

将 EC2 实例`sarmad`(或者您创建的任何部署用户)用户的私钥粘贴到下面的文本区域。这将让我们的 SemaphoreCI 部署服务器通过 Capistrano:

![semaphore-ec2-private-key-for-deployment](img/d761cbabb032ca691cd943e1473ce434.png)

接受默认值并单击“创建服务器”:

![semaphore-deployment-server-name](img/0f995d202f1dec0fe6b36ba6f0c2b48b.png)

部署服务器被创建和设置。点击**部署**按钮，使用我们的设置启动部署:

![semaphore-first-deployment](img/27f8ccfd9cc65a4dbd5ab705dc0461ca.png)

答对了。部署成功。

![semaphore-capistrano-automatic-deployment](img/1478818e6c6f279ffcb1349817ac1289.png)

现在我们需要验证如果我们推送一个失败的构建(推送一个失败测试的提交)，它没有被部署。运行 rake 测试以验证测试是否通过当前代码库:

```
$ rake test
Run options: --seed 52969
# Running:
.......
Finished in 0.509991s, 13.7257 runs/s, 25.4906 assertions/s.
7 runs, 13 assertions, 0 failures, 0 errors, 0 skips 
```

通过在**test/controllers/blogs _ controller _ test . Rb**中进行修改来使测试失败。改变本测试中的`assert_response`线:

```
test "should get index" do
    get :index
    assert_response :success
    assert_not_nil assigns(:blogs)
end 
```

更换`assert_response :success`到`assert_response :error`使测试失败。通过运行`rake test`进行验证:

```
$ rake test
Run options: --seed 47910
# Running:
..F....
Finished in 0.378145s, 18.5114 runs/s, 31.7339 assertions/s.
  1) Failure:
BlogsControllerTest#test_should_get_index [/Users/sarmad/projects/rails-ci-testing-and-deployment-to-ec2-capistrano/test/controllers/blogs_controller_test.rb:10]:
Expected response to be a <error>, but was <200>
7 runs, 12 assertions, 1 failures, 0 errors, 0 skips 
```

它失败了。让我们将这一更改推送到我们的位存储库，并监控信号量 CI:

```
$ git add -A
$ git commit -m "failing test"
$ git push origin master 
```

这个构建正在进行中。我们需要这个版本失败。

![semaphore-build-in-progress-failing-test](img/cfabc8c4f150cd96f49d3d42e5fa44fc.png)

生成失败，SemaphoreCI 没有触发任何部署:

![semaphore-build-failed-test-failing](img/62da3ccf4bb1018fa8f74175998331cc.png)

这是失败构建的详细信息页面。我们可以看到哪些测试失败和错误，如果有的话。

![semaphore-build-failed-detail-test-failing](img/0daaf28b062dc62523384c961f8d148f.png)

现在让我们修复损坏的测试，并将其推送到 Bitbucket 存储库。通过将`assert_response :error`恢复到`assert_response :success`来恢复失败的测试。

通过运行`rake test`验证测试是否通过:

```
$ rake test
Run options: --seed 25838
# Running:
.......
Finished in 0.455917s, 15.3537 runs/s, 28.5140 assertions/s.
7 runs, 13 assertions, 0 failures, 0 errors, 0 skips 
```

提交并推动我们的回购:

```
git add -A
git commit -m "fix the broken test"
git push origin master 
```

再次监控 SemaphoreCI:

![semaphore-build-passed-and-automatic-deployment](img/cbae95db50b8bf1932bee9b4736485f2.png)

构建是成功的，并且在构建通过后立即启动了部署。

## 结论

现在，您已经有了一个部署在高度可伸缩的 Amazon EC2 服务器上的 Rails 应用程序。您的设置包括一个用 Capistrano 设置的自动部署脚本，以及一个测试和部署服务器。这是一套非常强大的 Rails 开发工具，对于大多数项目来说都是非常高效的。我把它们都放在一个地方，这样您就可以开始使用，并为将来的 Rails 应用程序部署打下基础。希望你觉得有用。

## 分享这篇文章