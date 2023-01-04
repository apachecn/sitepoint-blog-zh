# 使用 PuPHPet 轻松构建虚拟机——第 2 部分

> 原文：<https://www.sitepoint.com/build-virtual-machines-easily-puphpet-part-2/>

在本系列的第一部分中，我们考虑了如何以一种简单、可重复、甚至高效的方式创建和管理开发环境的问题。我相信这是一个我们都会不时碰到的问题。解决方案是一个相对较新的工具，叫做 [PuPHPet](http://www.puphpet.com) 。

如果你错过了第一部分，Puppet 基于五个关键领域创建了一个流浪者+木偶的配置:

1.  部署目标(内存、ip 地址、端口转发)
2.  服务器基础
3.  PHP(模块、库、配置设置)
4.  数据库(PostgreSQL、MySQL)
5.  网络服务器(Apache 2，Nginx)

我们看了如何配置大多数选项，以及如何使用生成的配置，以及一些基本的浮动命令。但我们就到此为止了。

所以在这个系列的第二部分，我们将更进一步。具体来说，我们将查看使用的两个核心文件:`common.yaml`和`Vagrantfile`。我们将对它们进行一些更改，然后调配虚拟机以反映配置更改。

这个系列不会让你成为[傀儡](http://puppetlabs.com/puppet/what-is-puppet)或[流浪者](http://www.vagrantup.com/)的主人，但是在它结束时，你将有足够的知识来对 pupppet 生成的配置进行更改，这样你就可以根据需要的变化来调整你的虚拟机。

**注:**请注意，木偶和流浪者发生了一些重要的变化，影响到了 pupppet。请特别注意 PuPHPet 网站顶部的两个通知——特别是如果你在这个系列的[第一部分](https://www.sitepoint.com/build-virtual-machines-easily-puphpet/)之后遇到了任何困难。如果通知不在了，问题也就不在了。

## 设置更改

为了我们今天的目的，有一个你需要知道的关键命令，[流浪者条款](http://docs.vagrantup.com/v2/provisioning/basic_usage.html)。引用手册:

> 该命令是快速测试任何供应器的好方法，对于 shell 脚本、Chef cookbooks 或 Puppet 模块的增量开发尤其有用。您可以在您的机器上对供应脚本进行简单的修改，运行一个浮动供应，并检查期望的结果。冲洗并重复。

我们将用它来推出我们的环境变化。

## 更改共享文件夹配置

您可能首先要做的事情之一是调整共享文件夹的配置。假设是本系列第一部分中的原始配置，下面是相关部分的样子。

```
config.vm.synced_folder "/Users/matthewsetter/Documents/workspace/my-great-app", "/var/www/my-great-app", id: "vagrant-root", :nfs => false
```

您可以看到，我已经在虚拟机的`/var/www/`下，在我的工作区目录中建立了一个同名的共享文件夹。最近，我为一个客户的项目设置了一个新的流浪虚拟机。我发现我的 Zend Framework 2 应用程序无法写入缓存目录。查看项目的目录结构，在虚拟机中，所有的目录都是由用户游民拥有的；但是 web 服务器用户(和组)是 www-data。所以它不识字。我看到了两种选择:

1.  将权限更改为 777
2.  更改所有者和组

对我来说，第二个是更明智的选择。但是怎么做呢？事实证明，这很简单。在上面的配置结束时，我们需要将所有者和组指定为`www-data`。我已经在下面的更新配置中这样做了。

```
 config.vm.synced_folder "/Users/matthewsetter/Documents/workspace/my-great-app", "/var/www/my-great-app", id: "vagrant-root", :nfs => false, owner: "www-data", group: "www-data"
```

要推出更改，请更新您的 vagger 配置中的文件 Vagrantfile，保存该文件，然后从命令行运行命令:`vagrant provision`。如果一切正常，您将看到类似于第一次调用`vagrant up`时的输出，但是要短得多，因为只进行了一些更改——而不是调配整个虚拟机。完成后，使用`vagrant ssh`登录虚拟机，然后查看项目目录的权限。它们应该类似于下面的输出:

```
 $ ls -lahrt /var/www/my-great-app/
    total 3.1M
    drwxrwxrwx 1 www-data www-data  102 Oct 15 05:00 data/
    -rw-rw-rw- 1 www-data www-data 856K Oct 15 05:00 composer.phar
    -rw-rw-rw- 1 www-data www-data  607 Oct 15 05:00 README.md
    -rw-rw-rw- 1 www-data www-data  259 Oct 15 05:00 .gitmodules
    -rw-rw-rw- 1 www-data www-data  338 Oct 15 05:00 .gitignore
    -rw-rw-rw- 1 www-data www-data 1.8K Oct 15 05:00 init_autoloader.php
    drwxrwxrwx 1 www-data www-data  340 Oct 15 05:00 db/
-rw-rw-rw- 1 www-data www-data 2.2M Oct 15 05:00 zftool.phar
```

## 改变 PHP 的配置

到目前为止，一切顺利。让我们看看 PHP 配置并做一些更改。下面是第一部分中 PuPHPet 生成的原始配置。你会在`vagrant/puppet/hieradata/common.yaml`里找到。它显示 php 版本是 5.5，启用了 composer，PHP 模块，ini 设置和默认时区。

```
 php:
    version: '55'
    composer: '1'
    modules:
        php:
            - cli
            - intl
            - cgi
            - curl
            - mcrypt
            - memcache
            - memcached
            - pspell
            - tidy
            - sqlite
        pear: {  }
        pecl:
            - pecl_http
    ini:
        display_errors: On
        error_reporting: 'E_ALL & ~E_STRICT'
        apc.enabled: '1'
    timezone: Europe/Berlin
```

让我们做一些改变。让我们改变:

*   时区为“欧洲/伦敦”
*   会话保存路径
*   一些其他运行时选项:

具体来说，从`ini:`开始更新如下内容:

```
 ini:
        display_errors: On
        error_reporting: 'E_ALL & ~E_STRICT'
        apc.enabled: '1'
        session.save_path: /tmp
        allow_url_fopen: '1'
        allow_url_include: '1'
        error_log: syslog
        file_uploads: '1'
    timezone: Europe/London
```

你可能会从 PHP 手册[中认出 ini 指令，所以我在这里不再赘述。但是您可以看到，在这里配置它们就像在 PHP ini 文件中一样容易。实际上，让我们冒险做一个像样的改变。](http://www.php.net/manual/en/ini.list.php)

这样做之后，下面是新的 PHP 设置:

*   session.save_path => /tmp => /tmp
*   allow_url_fopen => On => On
*   allow_url_include => On => On
*   file_uploads => On => On
*   error_log => syslog => syslog
*   默认时区= >欧洲/伦敦
*   date.timezone = >欧洲/伦敦= >欧洲/伦敦

*注意:*如果你想选择不同的时区，[这里有 PHP 支持的列表](http://www.php.net/manual/en/timezones.php)。

现在我相信你会同意，仅仅通过改变一个文本文件就能改变一个 PHP 配置，是非常强大的。想象一下，为了达到这个目标，你过去可能做了多少工作。

## 更改 XDebug 配置

如果你使用 PHP 已经有一段时间了，你可能一直在使用 Derek Rethan 的 XDebug 包。在第一部分中，我们采用了 PuPHPet 中提供的基本设置。让我们对其进行一些适度的更改，并再次调配虚拟机来支持它们。下面是 common.yaml 的原始配置。

```
xdebug:
    install: '1'
    settings:
        xdebug.default_enable: '1'
        xdebug.remote_autostart: '0'
        xdebug.remote_connect_back: '1'
        xdebug.remote_enable: '1'
        xdebug.remote_handler: dbgp
        xdebug.remote_port: '9000'
```

添加以下选项，记住要注意缩进，否则会有问题。

```
 xdebug.auto_trace = 1
    xdebug.idekey = "SITEPOINTPHP"
    xdebug.max_nesting_level
    xdebug.profiler_enable = 1
    xdebug.profiler_output_dir = "/tmp/xdebug.profiler"
```

这些配置选项优于任何其他配置选项没有特别的原因。他们是随机挑选出来玩 XDebug 的。因此，如果您愿意，可以随意选择其他选项。

像以前一样，调用`vagrant provision`，稍等片刻，等待供应过程完成。然后，我们可以通过再次查看正在运行的 PHP 配置来检查该过程是否成功:

```
php -i | grep -i xdebug
```

*   xdebug.auto_trace => On => On
*   xdebug . idekey = > SITEPOINTPHP = > SITEPOINTPHP
*   xdebug . max _ nesting _ level = > 100 = > 100
*   xdebug.profiler_enable => On => On
*   xdebug . profiler _ output _ dir = >/tmp/xdebug . profiler = >/tmp/xdebug . profiler

## 改变 MySQL 的配置

好，最后一个。让我们对 MySQL 做些改变，找点乐子。我们将更改用户帐户名和密码，安装并启用`phpmyadmin`。最后，我们将对用户的权限进行一些限制。不错的任务列表。没有吗？

想一想，如果全部手工完成，需要多长时间。不，说真的。也许您已经有了相应的配置脚本；也许你全是用手做的；但无论哪种方式都需要时间。

*注:*老实说，我不是 MySQL 管理员；所以有些工具我不知道。如果是，请在评论里提一些。

和以前一样，下面是最初生成的配置，这样您就知道它是什么样子了。

```
mysql:
    root_password: password
    phpmyadmin: 0
    databases:
        njmOqAFAsG3i:
            grant:
                - ALL
            name: testdb
            host: localhost
            user: testdb_couser
            password: testdb_password
            sql_file: ''
```

下面是更新后的版本，展示了我上面提到的修改后的样子。您会看到用户名和密码已经更改，我在 phpMyAdmin 旁边设置了 1 来启用和安装它。

```
mysql:
    root_password: password
    phpmyadmin: 1
    databases:
        njmOqAFAsG3i:
            grant:
                - INSERT
                - SELECT
                - 'SHOW DATABASES'
                - UPDATE
                - USAGE
                - INDEX
                - DELETE
                - CREATE
                - ALTER
            name: testdb
            host: localhost
            user: testdb_username
            password: testdb_P@$sw0rd
            sql_file: ''
```

让我们再次调配虚拟机，看看发生了什么变化。运行`vagrant provision`后，我可以用新的用户名和密码登录，phpMyAdmin 在`http://192.168.56.101/phpmyadmin/`可用。

## 结论

你觉得怎么样？PuPHPet、vagger 和 Puppet 真的让管理开发环境变得简单了吗？如此多的事情可能已经被手工完成，或者用手写的 bash 脚本不再需要。

在您跟进的过程中，您的资源调配是否和我的一样顺利？你遇到过什么需要帮助的问题吗？如果是这样，请随时直接给我发电子邮件。

否则，我希望你已经被流浪狗、木偶和木偶咬过了。我还希望您已经看到，通过仅仅 2 个版本控制的文本文件和一些命令，就可以动态地调整您的环境，以满足您不断变化的需求是多么容易。

请在评论中分享你的想法。

## 分享这篇文章