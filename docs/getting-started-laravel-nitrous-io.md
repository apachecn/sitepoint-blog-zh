# Laravel on Nitrous.io 入门

> 原文：<https://www.sitepoint.com/getting-started-laravel-nitrous-io/>

2 月 12 日，一氧化二氮。IO 是一个云开发环境，可以让你在一瞬间建立虚拟机器，并通过 Web IDE 在任何平台上使用它们，它最终增加了 PHP 支持——这是用户一直渴望的。

你可以在[公告](http://blog.nitrous.io/2014/02/12/announcing-php-support.html)中了解更多信息，但我认为最好是我们通过示例进行演示，并让 Laravel 应用程序启动并运行。

注意:本教程假设您精通基本的 Unix 终端。

### 入门指南

如果你还没有，在 Nitrous.io 上创建一个帐户，或者点击我的参考链接[这里](https://www.nitrous.io/join/6O0J8Bn631M)创建一个帐户(完全公开，这给了我*nitrous*——用于使更强大的虚拟机上线的货币)。在整理了您的个人资料之后，我鼓励您进入公共密钥，如果您通常将它们用于 SSH 连接或类似用途，那么添加一个您自己的公共 SSH 密钥。GitHub 上有详细的指南[。](https://help.github.com/articles/generating-ssh-keys)

### 创建一个盒子

让我们进入盒子并点击“新盒子”。选择 PHP，一个离你最近的地区，分配尽可能多的资源——如果你愿意，你可以把你所有的氮气余额都花在这上面——当你终止一个盒子时，你的氮气会被退还。

![](img/5ef4a1beafab3953f208e6996b3f6b65.png)

点击“创建盒子”后，你的盒子将被准备好——就像流浪者一样。你只是看不到那么多的输出，而且会快得多。

![](img/ceab94c2dbca7a2b749ce4d9da67af87.png)

完成后，你会看到一个友好的、有点熟悉的界面，以及一个引导你完成其余设置的介绍性文件。

![](img/2fce3f260042fd7678c349137077a186.png)

你也可以通过使用任何其他模板来运行 PHP box，然后使用 Autoparts -> Install Parts 菜单来安装 PHP 相关的工具，如 Apache、PHP、MySQL 等。在 PHP box 模板中，包含了这些部分。如果您现在使用附带的控制台输出 PHP 版本，您应该会看到显示的 5.5.8:

![](img/79c7875300f8cf9c7f4cdf18cf875cb4.png)

“Parts”类似于 Nitrous 的定制软件包管理器，您可以使用它来启动服务和安装部件。Parts 也可以自动为你安装 WordPress，但是谁会希望这样呢？

### PhpInfo

打开工作区文件夹，在`www`子文件夹中创建一个名为`index.php`的文件，内容如下:

```
<?php
    phpinfo();
```

然后，转到预览->端口 3000(默认)。您应该会在浏览器中看到以下 PhpInfo 屏幕:

![](img/dbb32faf3128fbf1398f883dffbeb227.png)

请注意，除非您这样配置，否则其他端口不会工作。第一个(3000 SSL)是一个高级功能，您必须为它付费，而所有其他功能都可以通过使用控制台编辑`httpd.conf`来定义:

```
vim /home/action/.parts/etc/apache2/httpd.conf
```

![](img/4065cc5f12a9ebf917899b15a416ef6d.png)

请注意，您也可以通过文件浏览器访问这些文件，只需记住单击文件浏览器底部的“显示隐藏”。不过，我发现使用命令行更简单。

### 安装 PECL 库

Composer 需要 Zip 库才能工作，在我们的机器上安装它非常简单，按照[说明](http://help.nitrous.io/php/)

```
pear config-set php_ini /home/action/.parts/etc/php5/php.ini
pecl config-set php_ini /home/action/.parts/etc/php5/php.ini     
pecl install zip
```

要在控制台中粘贴，请记住按 CTRL + SHIFT + V，而不是只按 CTRL + V。构建和安装可能需要几分钟，请耐心等待。

但是拥有一个`www`文件夹已经是 21 世纪初的事情了。几乎有共享宿主的味道，不是吗？让我们支持它。

### 配置虚拟主机

这里有两条路可以走。一种是定义多个虚拟主机，这在 Apache 中很常见。另一个是将`www`文件夹重命名为`public`，并将`workspace`文件夹用作我们应用程序的文件夹。我不是后者的粉丝，因为它对我的预建应用程序施加了一些限制，所以让我们使用前者。

再一次，vim 进入`httpd.conf` : `vim /home/action/.parts/etc/apache2/httpd.conf`

在文件底部，添加以下块:

```
<VirtualHost *:4000> ServerName doesnotmatter
    DocumentRoot "/home/action/workspace/myapp/public"
    ServerAdmin bruno.skvorc@sitepoint.com <Directory  "/home/action/workspace/myapp/public"> Options Indexes FollowSymLinks
            AllowOverride All 
            Require all granted
            Order allow,deny
            Allow from all </Directory>  </VirtualHost>
```

字面上，将其添加到`httpd.conf`文件的末尾:

![](img/3840d87b805bb01c982e2b7b51546c16.png)

此外，在文件顶部的`Listen 0.0.0.0:3000`处，您应该添加另一行:`Listen 0.0.0.0:4000`。对每个想要监听的端口都这样做。

另一种方法是将所有这些内容放入`config`子文件夹，就像靠近`httpd.conf`文件顶部的注释所说的那样。选择你的毒药，两者都可以。

现在创建这个配置中提到的文件夹。

```
mkdir -p /home/action/workspace/myapp/public
```

`-p`参数强制递归创建，这意味着它也创建所有父文件夹。现在将带有`phpinfo();`内容的同一个`index.php`文件添加到这个文件夹中。一个简单的命令是这样的:

```
cd /home/action/workspace/myapp/public/ echo "<?php phpinfo();"  > index.php
```

使用`parts restart apache2`重启 Apache，并尝试通过 Preview - > Port 4000 打开端口 4000 上的站点。您应该会看到和以前一样的 PhpInfo 屏幕。

您可以用同样的方式设置其他虚拟主机。

### 安装作曲者

更新:请注意，Composer 现在是预安装的，您可以跳过这一步。尽管如此，这一部分涵盖了定制二进制文件的安装，因此您可能还是想阅读它。

既然我们已经解决了虚拟主机的问题，并且如果我们愿意，可以在一个盒子下运行几个应用程序，那么让我们安装 Composer。我们将在全球范围内安装它——在每个应用程序中安装一次完全没有意义，尤其是在硬盘空间如此有限的机器上。

Nitrous.io 框受到限制，因为您不能在个人文件夹之外创建文件夹。为了避开这个限制，我们将把它安装到我们主文件夹中的一个文件夹中，并将这个文件夹添加到`$PATH`中。

```
mkdir ~/.tools
cd ~/.tools
curl -sS https://getcomposer.org/installer | php vim ~/.bashrc
```

一旦完成 vimmed，通过在`.bashrc`末尾添加以下内容来编辑它:

```
export PATH=$PATH:$HOME/.tools/
```

然后，通过执行`source ~/.bashrc`重新加载`.bashrc`文件。

至此，Composer 已经在全球范围内安装，你可以从任何你想要的应用程序中使用它。通过执行以下命令进行尝试:

```
cd /home/action/workspace/myapp
composer self-update
```

![](img/5673266b1b91e355bfb8e4770676d2e9.png)

### 创建示例 Laravel 应用程序

作曲家，检查。虚拟主机，检查。让我们点燃拉弗尔，好吗？仅供参考，我选择 Composer 作为安装 Laravel 的方法，因为它不仅对 Laravel 有用，而且最终你也会在其他非 Laravel 项目中用到它。

首先，让我们删除 myapp 文件夹。Composer 不会在非空的文件夹中创建项目，因此这将允许它从头开始。

```
rm -rf /home/action/workspace/myapp
```

然后，启动 create-project 命令:

```
cd /home/action/workspace
composer create-project laravel/laravel myapp --prefer-dist
```

一两分钟后，所有内容都应该下载完毕。访问我们机器上的同一个 4000 端口应该会给我们一个 Laravel 欢迎界面。

![](img/cc6a36a5efd9c109dfda9c6603fe4c57.png)

按照[快速启动指令](http://laravel.com/docs/quick)，我们通过在`app/routes.php`中增加以下内容，在`/test`中增加一条关闭路线:

```
Route::get('test',  function()  {  return  'Test!';  });
```

现在，访问`{$URL}:4000/test`给我们以下输出:

![](img/61c7b19b7b96aefddd83553e87e47e0d.png)

### 将 Laravel 连接到 MySQL

让我们首先检查我们的数据库是否工作。尝试在 Nitrous 命令行中输入`mysql -u root`，看看是否能进入。如果您可以用`SHOW DATABASES;`列出数据库，用`USE test;`将数据库改为`test`，然后用`SHOW TABLES;`列出该数据库中的表(结果应该是一个空集)，那么您就准备好了。

默认情况下，Laravel 被配置为使用 Nitrous 使用的不安全设置——没有密码的数据库用户名“root”。因此，默认情况下连接已经就绪——您需要做的只是将配置文件中的数据库名称更改为“sample ”,因为这是我们将在下面的示例中使用的名称。

很自然地，您应该更改您的凭证——在 MySQL 中，添加一个只允许使用特定数据库的用户，并将该用户添加到`app/config/database.php`文件的适当部分。对于基于用户的数据库权限，请阅读[本答案](http://stackoverflow.com/questions/1720244/create-new-user-in-mysql-and-give-it-full-access-to-one-database)。我们这样做不仅仅是为了这次演示。

我们可以遵循快速入门文档中的迁移示例，但是我更愿意一次性完成所有工作，并立即准备好我们的示例数据。要创建一个包含一个`test`表和三行样本值的`sample`数据库，请在 Nitrous 控制台中执行以下命令，无论您当前位于哪个文件夹:

```
wget -O-  -q https://gist.github.com/Swader/8994154/raw/9bb8d253f92791de77fa01138febd404a306ccc6/sample.sql | mysql -u root
```

如果你好奇的话，它的作用是获取我的要点和实现它所需的 SQL 代码，输出到 shell 中的标准输出流，同时消除所有错误和其他日志信息，并在以 root 用户身份登录后将其应用到 MySQL。

我们不会仅仅为测试数据建立模型——我假设你可以按照 Laravel 页面上极其简单的说明自己去做。相反，我们将在这里演示 Laravel 连接到数据库的能力。

在之前的`routes`文件中，用以下内容替换我们之前编写的闭包:

```
Route::get('test', function()
{
  var_dump(DB::select('select * from test'));
});
```

刷新/test URL，我们得到数据库输出:

![](img/7245fa52fe9a3052d8ba67102fcd5bf1.png)

### 结论

本文证明了在 Nitrous.io 上设置并运行 Laravel 配置是多么简单。如果您这样做了，只需终止并重新创建该框，就这么简单。

我们正处于一个每一步都是虚拟机的时代，我们的硬件和带宽足够强大，可以防止我们因不必要的安装而污染我们的主要桌面环境。大胆尝试，让我们知道你的想法，我们很乐意看一看。

## 分享这篇文章