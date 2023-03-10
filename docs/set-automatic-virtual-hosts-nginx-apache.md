# 用 Nginx 和 Apache 设置自动虚拟主机

> 原文：<https://www.sitepoint.com/set-automatic-virtual-hosts-nginx-apache/>

当启动新的应用程序时，开发人员通常需要设置一个新的虚拟主机。这包括为 Apache 或新的 Nginx 站点条目设置新的配置文件。有时您需要手动执行此操作。其他时候，如果你仅仅定义了一个站点到文件夹的映射，它会自动[处理](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)。但是，如果我们也可以跳过这一步，只让任何以`.local.com`结尾的 URL 自动查找它的文件，那么我们所需要做的就是只改变我们的`etc/hosts`文件**一次** ***一次*** 不是很实用吗？哇哦。

![Mind blown](img/5b581f4f27cd03d18466f341b2d02212.png)

在本教程中，我们将设置 Nginx 和 Apache 在检测到给定的 URL 格式时自动查看某些文件夹。例如，URL `mynewsite.local.com`会自动在`DOC_ROOT/nynewsite/public`文件夹中查找`index.php`文件。然后你将能够定义适合通用脚本、标准 MVC 应用(`public/index.php`)和基于 Symfony/Silex 的应用(`web/app.php`)的其他模式——所有这些都取决于 URL 格式(例如`mynewsite.sf2local.com`可以在`web/`中查找`app.php`，这是那些框架的典型)。

本教程将以 Unix 为中心，但是如果你使用我们的 [Homestead 改进版](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) box，它也是 Windows 友好的。

在我们开始之前，确保您的`/etc/hosts`文件中有`something.local.com`,这样我们可以在进行过程中测试这个 URL。如果需要，根据您的喜好进行修改。我将重点关注在我们的文档根目录下的`something`文件夹中用于触发 Vhost 查找的`local.com`后缀，但是这实际上可以是在 URL 字符集内有效的任何东西。

## 为 Nginx 设置

![Nginx Logo](img/4eb349b11de01a960e3ad84cf7c2bcf8.png)

假设你已经运行了一个 Nginx 设置，无论是通过 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 还是任何其他方式，让我们在 Nginx 通常用来读取站点配置的文件夹中创建一个新条目:`sites-available`。在标准安装中，Nginx 将在配置中包含文件夹`sites-enabled`中的所有内容。将我们将要写入的文件放入`sites-available`将需要我们稍后激活它。

```
vim /etc/nginx/sites-available/wildcard.conf
```

我们首先定义一个`server`块，并告诉它监听端口 80:

```
server {
	listen 80;
}
```

我们写的其他东西都将放在服务器块中，在`listen`语句下面。

```
server_name ~^(www\.)?(?<sname>.+?).local.com$;
```

这是最重要的部分。它告诉 Nginx“服务器名称将以正则表达式的形式出现。拔出`sname`位，留着以后用”。

```
root /home/vagrant/Code/$sname;
```

这告诉 Nginx“这个 web 应用程序的根文件夹在这个位置”。因为它从上面的正则表达式追加了`sname`，所以路径变成了动态的，对应于 URL。如果你没有运行一个虚拟机，或者有一个不同设置的根文件夹，请随意更改——只要确保它以`$sname;`结尾。如果你的应用程序通常在`public`文件夹中有前端控制器，可以随意添加`public`，这样`root`语句看起来就像这样:

```
root /home/vagrant/Code/$sname/public;
```

我将在文件的最终版本中使用这种方法。

其余的配置可以保持不变。差不多就是这样，除非您还希望有动态错误日志，在这种情况下，文件中的错误日志条目应该如下所示:

```
access_log /var/log/nginx/$sname-access.log;
    error_log  /var/log/nginx/wildcard-error.log debug;
```

请注意，错误日志中不能有变量，只有访问日志。不知道为什么要这样实现。不幸的是，这意味着您的通配符错误日志将被合并。

下面是我们写的配置文件的完整代码。根据你的安装，一些其他的东西可能会有所不同(比如 php-fpm 行等等。)但这超出了本教程的范围。

```
```
server {
    listen 80;
    server_name ~^(www\.)?(?<sname>.+?).local.com$;
    root /home/vagrant/Code/$sname/public;

    index index.html index.htm index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    access_log /var/log/nginx/$sname-access.log;
    error_log  /var/log/nginx/wildcard-error.log debug;

    error_page 404 /index.php;

    sendfile off;

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass unix:/var/run/php5-fpm.sock;
        fastcgi_index index.php;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

*您的 Nginx 安装可能设置不同，因此您可能需要查找站点配置文件。也有可能你所有的 Nginx 配置都在一个文件中，在这种情况下，只需将上面的配置添加到其中——这也可能是你的`sites-available`文件夹中的一个名为`default`的文件。这个配置在哪里并不重要，只要它被加载——查看`etc/nginx/nginx.conf`内部的配置自动包含位置。*

最后，我们需要通过在`sites-enabled`文件夹中创建一个符号链接来启用新配置:

```
sudo ln -s /etc/nginx/sites-available/myapp /etc/nginx/sites-enabled/myapp
sudo service nginx restart
```

如果您现在在浏览器中访问`something.local.com`，来自(在我的例子中)`vagrant/Code/something/public`的文件将被加载。

## 为 Apache 设置

![Apache Logo](img/2aad79f0bb4949022c8c77705d06573f.png)

在这一节中，我将假设您有一个正在运行的 Apache 实例，它照常为您的 PHP 站点提供服务。这可以通过 [PuPHPet](https://www.sitepoint.com/build-virtual-machines-easily-puphpet/) 或与你的友好邻居*AMP stack，无关紧要-只要它能工作。

首先，我们需要确保`mod_vhost_alias`被启用。这是阿帕奇国防部用来给我们所需要的功能。有两种方法可以做到这一点。在更精简的安装中，命令`a2enmod`将在终端中可用，它所需要的只是`sudo a2enmod vhost_alias`。在其他情况下，同样由于 Linux 的诅咒，你需要查看主配置文件，或者是`/etc/apache2/apache2.conf`或者是`/etc/apache2/httpd.conf`之类的。如果这是您的情况，尝试找到包含`mod_vhost_alias`的行。如果没有，添加下面一行:

```
LoadModule vhost_alias_module modules/mod_vhost_alias.so
```

注意，末尾的路径需要与 Apache 模块在系统中的位置相匹配。这也因 Linux 的诅咒而不同，但通常在`/etc/apache2/mods-available`中。

就像 Nginx 一样，Apache 通常从`/etc/apache2/sites-available`和扩展`/etc/apache2/sites-enabled`加载它的 Vhost 配置。它通常还带有一个模块，方便启用和禁用网站，就像这样:`sudo a2ensite mysite.conf`。实际上，这与上面关于创建符号链接来启用新配置的 Nginx 部分是一样的。它按字母顺序加载这些文件，所以我们将在`sites-enabled`中创建一个新的配置文件(或者在`sites-available`中创建并随后启用站点)，名为`000-default.conf`。

我们放入的第一条语句是:

```
UseCanonicalName Off
```

这是必需的，这样我们就可以动态地为 Apache 接收的 URL 分配别名。然后，我们定义虚拟主机块:

```
<VirtualHost *:80>
	ServerName vhosts.fqdn
	ServerAlias *.local.com
	VirtualDocumentRoot /var/www/%1+
</VirtualHost>
```

`%1`的意思是“取用点分隔的域名的第一部分”。对于其他选项，如果您希望进行不同的配置，请参见[文档](https://httpd.apache.org/docs/2.2/mod/mod_vhost_alias.html)。

如果我们现在将文件夹`something`放入`/var/www`中，并在`index.php`中放入一些 Hello World 的内容，应该就可以了。但是在这种情况下，它将以文本形式输出 PHP 文件的内容。我们需要像往常一样配置 VirtualHost 的其余参数。我将在下面粘贴我的 PuPHPet 版本——您的配置可能会有所不同。

```
UseCanonicalName Off
<VirtualHost *:80>
  ServerName vhosts.fqdn
  ServerAlias *.local.com

  VirtualDocumentRoot /var/www/%1

  <Directory ~ "/var/www/.*">
    Options Indexes FollowSymlinks MultiViews
    AllowOverride All
    Require all granted

    <FilesMatch "\.php$">
      Require all granted
      SetHandler proxy:fcgi://127.0.0.1:9000

    </FilesMatch>

  </Directory>
</VirtualHost>
```

现在，如果你用`sudo service apache2 restart`或者任何在你的发行版上有效的命令重新加载 Apache 配置，你应该能够在你的浏览器中访问`something.local.com`,如果你把一些 PHP 文件放到`var/www/something/public`中，你会再次看到它的工作。

## 可选项:dnsmasq

可选地，如果你在 Linux 或 OS X 上，你可以安装 [dnsmasq](https://www.thekelleys.org.uk/dnsmasq/doc.html) 。

Dnsmasq 是一个工具，它可以将一系列 URL 转发给一个给定的 IP。这就像 regex 对`/etc/hosts`的支持。使用这个工具，你可以将所有以`.local.com`结尾的 URL 设置为**或者你选择的任何其他 URL，以重定向到你的虚拟机的 IP 或者你的本地主机。这意味着你再也不用编辑你的`hosts`文件了——无论何时你开始一个新项目，你的 URL 和 Vhosts 都已经准备好了，你可以马上开始工作，极大地减少了 devops。**

在 OS X 与[家酿](https://computers.tutsplus.com/tutorials/homebrew-demystified-os-xs-ultimate-package-manager--mac-44884)包经理:

```
brew install dnsmasq
```

在您的 Linux 发行版上，下载[tarball](https://www.thekelleys.org.uk/dnsmasq/)。

如果你想进一步自动化，你可以添加 dnsmasq 作为一个[流浪者插件](https://github.com/mattes/vagrant-dnsmasq)。

要设置 dnsmasq 将 URL 模式转发到您的 IP，并设置您的操作系统将所有 URL 调用转发到 dnsmasq，以便它可以实际做到这一点，请参见[这份出色的指南](https://passingcuriosity.com/2013/dnsmasq-dev-osx/)。

## 结论

在本教程中，我们学习了如何自动化我们经常处理的 devops 部分——设置新的 Vhosts 及其域。每个项目只需要几分钟，特别是像 [Homestead Improved](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/) 这样的包，但仍然需要几分钟——并且能够快速切换到另一个模拟项目来测试您刚刚想到或需要尝试的一个脚本对一个人的工作流来说是无价的。

这个教程对你有帮助吗？您是否希望获得特定主题的更多信息？让我们知道！

## 分享这篇文章