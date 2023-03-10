# 在 5 分钟内部署带有 Phusion 乘客和 Nginx 的轨道

> 原文：<https://www.sitepoint.com/deploying-rails-with-phusion-passenger-and-nginx-in-5-minutes/>

当我第一次使用 RoR 时，我常常想知道开发人员是如何如此轻松地部署他们的应用程序的。如果你想用 Nginx 部署一个 Rails 应用程序，而又不想让整个过程看起来单调乏味，这里有一个指南会派上用场。我可以在 5 分钟内完成，你呢？

## Phusion 客车/ mod_rails

Phusion Passenger 是 Apache 和 Nginx 服务器的健壮模块，在部署使用 Rails 框架开发的 web 应用程序时，它比任何其他模块都更受欢迎。但如果你运行的是微软的 Windows 平台，你可能不得不寻找其他的替代品，因为 Phusion Passenger 与除了 Windows 之外的任何东西都兼容。对于本教程，我将使用 Ubuntu，但是你可以使用任何类似 unix 的东西。

## 在 nginx 和 Apache 之间选择

Apache 和 Nginx 是广泛采用的 web 服务器。尽管 Apache 是最著名的基于进程的 web 服务器，但如果您需要一个快速、响应迅速的服务器，Nginx 是最合适的。当服务器负载过重时，它会创造奇迹。

在这篇文章中，我将依赖 Nginx 服务器，但是如果你的堆栈中有 Apache，连接的方法与 Nginx 没有太大的不同。

## 使用 Phusion 乘客和 Nginx 部署 Rails 应用程序

首先，从 nginx.org 下载 Nginx 的最新稳定版本。稍后您将需要它的源代码来安装 Nginx 模块。我将使用 nginx-1.0.15，这是在写本教程时的最新稳定版本。

```
cd /tmp
wget http://nginx.org/download/nginx-1.0.15.tar.gz
tar -xvzf nginx-1.0.15.tar.gz
```

假设您安装了 Ruby、Rails 和 RubyGems，输入下面的代码来安装 Phusion passenger gem。

`gem install passenger`

现在，您必须配置您的 Nginx 来处理乘客。在您喜欢的 shell 中运行以下命令。

```
passenger-install-nginx-module
```

交互式安装程序将引导您完成安装，几分钟后您将拥有一个功能完整的 Nginx 服务器。我发现安装程序使用起来相当有趣。如果出现任何问题，您会收到通知，安装程序会显示可能的原因以及解决方法。你还能期待什么？

出现提示时，选择自定义 Nginx 安装方法(选项 2)。从长远来看，这将是你的优势，如果你想在 Nginx 的配置脚本中添加一些额外的参数，这是一个完美的选择。例如，如果您的应用程序需要 SSL 支持，而您的服务器中有 OpenSSL 源代码，那么您可以通过向配置文件传递一个参数来轻松地将它们编译在一起。

出现提示时，指定我们之前下载的 nginx 源代码的位置。在我们的例子中，它是/tmp/nginx-1.0.15。接下来的几个步骤不言自明。

如果一切顺利，您应该会看到类似“Nginx with Passenger support 已成功安装”这样的内容。安装程序可能会编辑您的 Nginx 配置文件，以便添加一些代码片段，或者您可能需要手动完成。不管怎样，你都会收到通知的。

我被要求自己配置它，这是它提示我做的。(下面给出的代码可能因用户而异)。

```
Please edit your Nginx configuration file (probably /opt/nginx/conf/nginx.conf),
http {
...
passenger_root /usr/lib/ruby/gems/1.8/gems/passenger-3.0.11;
passenger_ruby /usr/lib/ruby1.8;
...
}
server {
listen 80;
server_name www.yourhost.com;
root /somewhere/public; #
passenger_enabled on;
}
```

将上述内容复制到 nginx.config 中，并用应用程序的绝对路径替换“somewhere”(如上所述)。

以下是(我的)最终配置的样子；

```
worker_processes 1;
events {
worker_connections 1024;
}
http {
passenger_root /usr/lib/ruby/gems/1.8/gems/passenger-3.0.11;
passenger_ruby /usr/bin/ruby1.8;
include mime.types;
default_type application/octet-stream;
sendfile on;
keepalive_timeout 65;
server {
listen 80;
server_name myapplication.com;
root /home/root/application/public;
passenger_use_global_queue on;
passenger_enabled on;
# redirect server error pages to the static page /50x.html
#
error_page 500 502 503 504 /50x.html;
location = /50x.html {
root html;
}
}
}
```

设置您的数据库环境，并使用以下命令重启 nginx 服务器

```
sudo /etc/init.d/nginx restart
```

 `瞧！就这样，恭喜你。您已经成功部署了您的 rails 应用程序。`

 `通过在你的浏览器中输入你的应用程序 URL，希望你会发现它是有效的。如果你看不到它运行或者你进入一个错误页面，检查 Nginx 的 error.log(可以在 nginx.log 文件中找到)或者你的应用程序日志文件以获得更多信息。这可能是因为你错过了某些宝石或某些依赖不满意。` 

## `分享这篇文章`