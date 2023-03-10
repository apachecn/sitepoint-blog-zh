# 如何使用 Mina 将应用程序自动部署到阿里巴巴 ECS

> 原文：<https://www.sitepoint.com/how-to-automate-app-deployment-to-alibaba-ecs-with-mina/>

*本文是与[阿里云](https://www.alibabacloud.com/)合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

对于如何充分利用阿里云服务，你有什么建议吗？如果是这样，请在我们的 [SitePoint 社区](https://www.sitepoint.com/community/)告诉我们。

[https://www.youtube.com/embed/bVaHxabFW84](https://www.youtube.com/embed/bVaHxabFW84)

[**Mina**](https://github.com/mina-deploy/mina) 是一个部署自动化工具，也是来自 Rails 世界的 *deploy Bash 脚本生成器*，在开发公司[注意到](https://infinum.co/the-capsized-eight/faster-web-application-deployments-using-mina-instead-of-capistrano)相对于 Capistrano 的优势后，它成为了焦点。与 Capistrano 相反，Mina 只使用一个到部署服务器的 SSH 连接，并在那里执行一批 bash 命令。这使得它比 Capistrano 快得多，Capistrano 为每个命令打开一个单独的 SSH 会话。

在本文中，我们将介绍如何设置 Mina 来部署一个基本的 Django 应用程序——Django 世界的一个非正统工具集，它倾向于更多地使用 Docker 或 Fabric。鉴于 Mina 的简单性和灵活性，我们认为值得探索它在 Python web 应用程序部署中的用途。

[**【Django】**](https://www.djangoproject.com/)，一个为有期限的完美主义者设计的*网络框架，已经存在一段时间了。它最初是一个面向内容管理的 web 框架，由劳伦斯日报世界的 web 开发人员为其新闻门户网站内部创建。它于 2005 年出版，从此起飞，剩下的就是历史了。它成为最重要的、被广泛采用的 web 框架之一，与 Ruby on Rails 竞争。Instagram、Disqus、华盛顿时报、Mozilla、Bitbucket 和其他网站都在使用它。它仍然欣欣向荣。*

Django 文档建议将带有 mod-wsgi 的 Apache 作为首选，这可能是一个流行的选择。但是因为我们对性能很着迷，所以在本教程中，我们决定用 NGINX 和 uWSGI 栈将 Django 应用程序部署到阿里巴巴的 ECS 云实例。

NGINX 是一个以效率著称的基于事件的 web 服务器，它包括缓存选项，所以它通常是一个理想的解决方案。 [**uWSGI**](https://github.com/unbit/uwsgi) 是一个*应用服务器容器*——Python 的标准 web 接口 [WSGI](https://wsgi.readthedocs.io/en/latest/what.html) 的实现。它与 NGINX 配合得非常好。

## 入门指南

在我们开始之前，你需要注册一个[阿里云](https://int.alibabacloud.com/m/1000016598/)账户。开始使用阿里云并决定它是否是你的正确选择真的很容易，因为它的[免费试用为新用户提供 300 美元的信用](https://int.alibabacloud.com/m/1000016596/)。一旦你注册并获得积分，你就可以[探索许多主题的入门资源](https://int.alibabacloud.com/m/1000016596/)。

帐户设置好后，我们要做的第一件事是在阿里云后端控制台中创建一个 ECS 实例。

![](img/9771dacbe3fcfd28ae56c967eafd7a9f.png)

这个过程很简单。我们将选择 Ubuntu 16.04 LTS 作为我们的操作系统/ OS 映像。在创建时，我们希望确保我们的实例被分配到适当的安全组。在阿里巴巴的术语中，这些是针对不同端口的防火墙规则。这通常在默认情况下是有效的，但是如果以后对我们的实例的 web 访问出现任何问题，请确保检查这个选项。

可以通过左侧的弹性计算服务子菜单访问“安全组”页面。

创建实例后要做的下一件事是设置 SSH 密钥访问。

也许最直接的方法是在创建时用密码设置实例。然后，我们可以从我们的启动系统执行标准的`ssh-copy-id`——大概是一个本地设备。

从我们的本地设备执行的`ssh-copy-id root@xxx.xxx.xxx.xxx`(我们将把 xxx…序列替换为我们的阿里巴巴 ECS 实例公共 IP 地址)将提示我们输入密码，在输入密码后，我们的基于密钥的访问将被设置。

当我们通过 ssh 登录到我们的实例时，我们将做`apt-get update`来确保我们的 apt 源代码是最新的，然后我们安装 git、curl、wget: `apt-get install git curl wget -y`

## 安装服务器环境

Ubuntu 16.04 LTS 默认的 Python 版本是古老的 2.7。为了运行 Django 的最新版本，我们需要 Python 3+。解决这个问题的一个不太痛苦的方法是安装 Python 版本管理器 [pyenv](https://github.com/pyenv/pyenv) 。

它允许我们改变全局使用的 Python 版本，或者每个项目。在我们安装 pyenv 之前，根据 [pyenv wiki](https://github.com/pyenv/pyenv/wiki/common-build-problems) ，我们将安装先决条件:

`apt-get install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev liblzma-dev zlib1g-dev libffi-dev`

然后我们可以安装 pyenv:

`curl -L https://github.com/pyenv/pyenv-installer/raw/master/bin/pyenv-installer | bash`

完成后，pyenv 安装程序将提示我们在~/中添加几行。bash_profile，我们将这样做:

![](img/79318faf103334d79c1139e9c66a4e57.png)

现在，我们通过在终端中执行`source ~/.bash_profile`来更新工作会话中的路径。

如果我们做得正确，我们现在应该能够安装 Python 版本 3.7.0:

![](img/b5fb930cad9b0043edc27a0d55b1fc4b.png)

在服务器端做`pyenv versions`现在应该给我们显示两项:`system`和`3.7.0`，假设我们成功安装了 3.7.0 版本。

将使我们的 3.7.0 版本成为我们系统上的全局 python 版本。如果你有 pyenv 的问题，[这是访问](https://github.com/pyenv/pyenv/wiki/common-build-problems)的网址。

### 服务器堆栈

Ubuntu 镜像的默认设置是 Apache 服务器，它是预先安装的。如果正在运行，我们应该用`service apache2 stop`将其停止，然后用`apt-get install nginx -y`安装 nginx。这应该安装并启动 NGINX 服务器，当我们访问我们服务器的公共 IP 地址时应该可以看到。

我们还将安装 uWSGI: `pip install uwsgi` (Python pip 大概是在我们安装 pyenv 的时候安装的)。

我们还将确保安装了 Django:`pip install django`。我们可以在这里使用 [`virtualenv`](https://www.sitepoint.com/virtual-environments-python-made-easy/) 来确保我们的应用程序有一个包含的隔离环境，但是为了保持这个教程简单，我们将跳过它。

然而，在更复杂的情况下，这可能是一个明智的选择。

本指南假定我们已经将我们的域 A 记录指向我们的服务器 IP 地址，因此在本指南的其余部分中，假定`myxydomain.com`指向我们的 ECS 服务器的公共 IP。

现在我们将为我们的网站创建 NGINX 虚拟主机。文件可以在这里找到[。我们将讨论几件事情:](https://gist.github.com/tyaakow/1cd0a30095000c11301d3359b13a3537)

`server unix:///tmp/minaguide.sock;`

在这里，我们使用 NGINX 连接到 uWSGI 将在`/tmp`目录中创建的 Unix 套接字，对于更复杂的目录树中可能出现的权限复杂性，建议使用`/tmp`。这就是`/tmp`的作用。

`include /root/project/minaguide/uwsgi/uwsgi_params;`

这个(`/root/project/minaguide`)是我们的 django 项目在服务器上的一个目录，在这个目录中，我们将有一个带有`uwsgi_params`文件的`uwsgi`子目录，其中将保存一些 uWSGI 变量。我们稍后将回到这一点，并设置我们的 uWSGI 应用程序。

我们现在有了部署所需的基础服务器环境。

## 设置 Mina

我们正在机器*上设置 Mina，我们将从该机器*进行部署。假设设备也是 Linux / Ubuntu(对于 Mac 用户来说，情况应该不会有太大的不同——对于 Windows 用户来说也是如此，只要他们使用 [Windows 子系统 for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10) )，我们将希望确保我们已经安装了 Ruby 和 Ruby gems——`apt-get install ruby -y`应该会成功。

当我们这样做了，我们将有`gem`命令可用，所以我们将做`gem install mina`。

![](img/f951f49999e1518035c0f716fd96545b.png)

现在——也是在我们的本地机器上——我们将创建一个专用于我们项目的目录，并在那里执行`mina init`。

![](img/cb982d213b14f751370d3db5d70697e3.png)

这将在我们的项目目录中创建一个`config/deploy.rb`文件，我们将对其进行编辑以配置 Mina:

```
require 'mina/rails'
require 'mina/git'

# we fill our base variables here. In this case, using 
# ENV['domain'], we dont really need to enter the domain into the file, as
# we will be passing it when we call mina in command line.

# the script presumes we have set up ssh-key access to server, and that it won't ask us 
# for password (for 'root' user)
set :application_name, 'minaguide'
set :domain, ENV['domain'] 
set :deploy_to, '/root/project/minaguide'
set :repository, 'https://github.com/tyaakow/django-uwsgi-starter.git'
set :branch, 'master'
set :user, 'root'

# we have left this empty, but this can be used for choosing pyenv python version, like we 
# did manually
task :remote_environment do

end

# here we put any custom commands you need to run at setup
# directory structure from :deploy_to will be created
task :setup do
  # we are pulling rc.local from a gist, in order to make daemonize uWSGI 
  # and make it boot with the system
  comment "pulling rc.local to /etc/ ..."
  command %{wget -P /etc/ https://gist.githubusercontent.com/tyaakow/62a5f91dbf59b32e088ec15c8375ca76/raw/c0c864317888419cbb9f2c4311149649cea2be1a/rc.local}
  comment "making it executable..."
  command %{sudo chmod 755 /etc/rc.local}
  comment "removing existing virtualhosts from /etc/nginx/sites-enabled/ ..."
  command %{rm /etc/nginx/sites-enabled/*}
  comment "fetching the virtualhost file from github gist..."
  command %{wget -O /etc/nginx/sites-enabled/#{ENV['domain']} https://gist.githubusercontent.com/tyaakow/1cd0a30095000c11301d3359b13a3537/raw/197eb92160588c2e61818a90e28782a344e5fbae/myxydomain.conf }
  comment "replacing the myxydomain.com with the actual domain specified in deploy.rb..."
  command %{sed -i 's/myxydomain.com/#{ENV['domain']}/g' /etc/nginx/sites-enabled/#{ENV['domain']}}
end

desc "Deploys the current version to the server."
task :deploy do
  deploy do
    invoke :'git:clone'

    on :launch do
      command %{sed -i 's/myxydomain.com/#{ENV['domain']}/g' /root/project/minaguide/current/minaguide/settings.py}
      command %{uwsgi --emperor /root/project/minaguide/current/uwsgi/vassals --daemonize /var/log/uwsgi-emperor.log}
      command %{service nginx restart}
    end
  end

end 
```

我们可以在这个文件中看到我们正在使用 Ruby 字符串插值(`#{}`语法)。

我们还使用`sed`以编程方式替换文件中的文本(`sed -i 's/myxydomain.com/#{ENV['domain']}/g' /root/project/minaguide/current/minaguide/settings.py`)——我们使用需要在命令行传递给 Mina 的`ENV['domain']`。这是为了说明用 Mina 构建一些(可能更复杂)场景的技术。

`task :setup do`是我们可以用来定义任意数量任务的语法。

正如在[的 Mina 文档](https://github.com/mina-deploy/mina/blob/2608e50049cf21b1425c8bb7c3e5dd0e964b725f/docs/writing_your_own_tasks.md#fetch)中所指定的，我们可以使用在文件开头用`set`赋值的符号语法变量——用`fetch(:deploy_to)`语法。

文件可以在这里找到[。](https://gist.github.com/tyaakow/82dbe0994c398ac4d3772a1a7745a7b9)

在本教程中，我们将 Django 应用程序从我们为此目的创建的 GitHub 公共回购中取出，但是我们也可以使用本地回购，使用:

```
task :deploy do
  run(:local) do 
```

…我们将在那里运行`rsync`或`scp`——或者我们可以从一个需要 ssh-key 访问的私有回购中获取它。

我们可以更进一步，创建任务来安装我们的整个环境，但是我们会很快超出本指南的范围。

我们在 repo 中将`uwsgi`目录添加到我们的标准 Django 应用程序中——由`django-admin startproject minaguide`创建:

![](img/bf1858fe1815287e56a46876e2190611.png)

…其中 uwsgi_params 来自[该页面](https://github.com/nginx/nginx/blob/master/conf/uwsgi_params)，uwsgi.ini 包含基本的 uwsgi 设置。

在我们的 deploy.rb 脚本中，我们从 GitHub gist 的[中提取 NGINX 的虚拟主机文件，并将其放入`/etc/nginx/sites-enabled/`:](https://gist.github.com/tyaakow/1cd0a30095000c11301d3359b13a3537)

```
# the upstream component nginx needs to connect to
upstream django {
    server unix:///tmp/minaguide.sock; #this file will be created by uWSGI
}

# configuration of the server
server {
    # the port your site will be served on
    listen      80;
    # the domain name it will serve for
    server_name myxydomain.com; # will be substituted, with sed, with actual domain name provided 
    # to mina at command line
    charset     utf-8;

    # max upload size
    client_max_body_size 75M;   # adjust to taste

    # Django media
    location /media  {
        alias /root/project/minaguide/current/media;  # your Django project's media files - amend as required
    }

    location /static {
        alias /root/project/minaguide/current/static; # your Django project's static files - amend as required
    }

    # Finally, send all non-media requests to the Django server.
    location / {
        uwsgi_pass  django;
        include     /root/project/minaguide/current/uwsgi/uwsgi_params; # the uwsgi_params file 
        # from https://github.com/nginx/nginx/blob/master/conf/uwsgi_params
    }
} 
```

关于用 uWSGI 和 NGINX 部署 Django 的更多细节可以在这里找到。

## 部署

现在，当我们想要部署时，我们将从我们的本地/开发系统`mina setup domain=myfabulousdomain.com`调用——在那里我们将用我们实际服务器的 URL 替换`myfabulousdomain.com`,我们将在那里部署。这将被米娜用作前面提到的`ENV['domain']`。

这将在我们的服务器上执行`setup`任务。

通过执行`mina deploy domain=myfabulousdomain.com`，我们将执行`deploy`任务/程序块。当我们访问我们的域时，我们应该看到以下内容:

![](img/cde624970715472be66ad4008e887299.png)

如果在部署期间，我们遇到了套接字文件的问题(权限和所有权问题，或其他问题)，并且我们没有时间花在修复它上——最快的解决方案可能是从将 nginx 连接到 uWSGI 套接字切换到通过 http 将其连接到 uWSGI——uw SGI 有可用的`--http`和`--http-socket`选项(我们将需要更改 NGINX 虚拟主机文件中的`upstream django / server`连接细节——以连接到类似`http:://127.0.0.1:8080`的东西——并适当地更改我们的 uw SGI 命令。

更多关于`--http`和`--http-socket`选项的信息[在此](https://uwsgi-docs.readthedocs.io/en/latest/ThingsToKnow.html)和[在此](https://uwsgi-docs.readthedocs.io/en/latest/HTTP.html)。uWSGI 是一个相当大的话题。

## 结论

在本文中，我们使用 mina，这是一个非常简单、非常灵活、非常快速的应用部署解决方案。对于更复杂的设置，需要更全面的解决方案——我们可能需要自动化更广泛的系统和堆栈配置细节——我们可能希望研究阿里巴巴的[容器服务](https://int.alibabacloud.com/m/1000017257/)—旨在为简化不同的 DevOps 场景提供解决方案。[容器注册中心](https://int.alibabacloud.com/m/1000017258/)允许更复杂的场景。

如果我们的产品/服务需要多个 ECS 实例，我们还会考虑阿里巴巴的[服务器负载平衡器](https://int.alibabacloud.com/m/1000016608/)，它允许我们动态扩展我们的基础设施以跟上流量。这里要考虑的另一个阿里巴巴的产品或功能是[自动扩展](https://int.alibabacloud.com/m/1000017259/)，它可以根据预定的时间表或基于对正在运行的实例的 CPU 和内存使用情况的监控来自动部署新的实例。

[Function Compute](https://int.alibabacloud.com/m/1000017260/) 相比之下，我们可以专注于我们的业务逻辑，同时将整个基础设施和环境问题委托给云。该服务是事件驱动的，可以由其他服务触发，我们所做的就是上传我们的代码(NodeJS、Python、Java)。我们为实际执行的代码付费。

然后是[全系列](https://int.alibabacloud.com/m/1000016598/)安全产品/服务、 [CDN](https://int.alibabacloud.com/m/1000016610/) 和监控解决方案

## 分享这篇文章