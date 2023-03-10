# 在 Nitrous.io 上安装 PHP 扩展

> 原文：<https://www.sitepoint.com/installing-php-extensions-nitrous-io/>

受到我上一篇文章的一个评论的启发，我意识到 Nitrous 仍然有点太复杂，无法正确定制。在本教程中，我们将在 Nitrous.io PHP 盒子上安装 cURL 和 Phalcon。

cURL 是一个捆绑的 PHP 库，通常与 PHP 一起安装。它包含在 PHP 源代码中，但是在安装过程中需要向 PHP 传递一个标志，以便它包含并激活 cURL。Phalcon 是一个高性能的基于 C 的 MVC 框架。我们在之前已经[写过了。Phalcon 是第三方扩展，因此不与 PHP 捆绑在一起。](https://www.sitepoint.com/up-and-running-with-the-fastest-php-framework-on-php7-in-5-mins/)

### 入门指南

要开始使用 Nitrous.io PHP 盒子，请参阅我的[上一篇文章](https://www.sitepoint.com/getting-started-laravel-nitrous-io/)。在最初的简短过程之后(您可以跳过整个 Composer 和 Laravel 业务)，您将安装 PHP，但是没有我们需要的扩展。要验证 cURL 是否丢失，请尝试将以下内容添加到默认`www`文件夹中的`index.php`文件中:

```
<?php
    echo "Curl is" . ((function_exists('curl_version')) ? "" : " not") . " installed";
```

在默认端口 3000 上的浏览器中预览将显示 cURL 显然没有安装。但是，它是在系统范围内安装的。您可以通过在控制台中检查版本来验证这一点，并获得以下输出:

![](img/75f3b850f80cf67baad7efe38d23074d.png)

因此，我们只需要 PHP 扩展就可以使用它。通常情况下，PHP 的 cURL 扩展是在执行`configure`命令的同时，用带有标志`--with-curl[=DIR]`的 PHP 安装的。在其他场景中，可以通过调用像`sudo apt-get install php5-curl`这样简单的东西来轻松安装。然而，在我们的案例中，这两种方法都不起作用。前者是无法实现的，因为 PHP 已经安装，源代码也不在机器上，后者是我们无法获得的，因为我们没有 sudo 访问权限。

我们接下来要做的是从 PHP 的源代码构建 cURL，并手动将其添加到我们的配置中。

### 下载 PHP 的源代码

如果我们此刻在 Nitrous.io 上查看 PHP 版本，是 5.5.8。然而，最新的 PHP 版本是 5.5.9。为了避免任何潜在的不兼容问题，我们将克隆我们正在构建的扩展的确切版本。

浏览[旧档案](https://php.net/releases/)并选择合适的版本。当您找到您喜欢的链接时，执行以下操作(注意，如果您跟随上一篇文章，工具目录应该存在；如果没有，创建它):

```
cd /home/action/.tools
wget http://at1.php.net/distributions/php-5.5.8.tar.bz2
```

如果你下载了`.bz2`文件，通过运行`tar -xjvf`来解压文件；如果你下载了`.gz`，则通过运行`tar -xzvf`来解压文件。这将产生一个与存档同名的文件夹。我们现在有了 PHP 5 . 5 . 8 版本的源代码。

### 构建 cURL 扩展

值得注意的是，这个过程可以应用于 PHP 附带的、默认情况下没有安装的任何扩展。我们在本文中使用 cURL 是因为对上一篇文章的评论，也因为它是一个流行的扩展。进入 PHP 源代码的扩展文件夹。

```
cd php-5.5.8/ext
```

所有可用的捆绑扩展都在这里，并附有完整的源代码。CD 放入`curl`文件夹，并运行以下程序:

```
cd curl /home/action/.parts/bin/phpize ./configure --with-curl
make
make install
```

这将从源代码构建扩展，将`curl.so`文件放入`modules`子文件夹，并将其复制到`/home/action/.parts/packages/php5/5.5.8/lib/extensions/no-debug-zts-20121212/`中的 PHP 扩展文件夹。

### 激活卷曲延伸

要激活扩展，我们需要做的就是在我们的`php.ini`中添加一行，然后重启 web 服务器。激活在文件浏览器面板中显示隐藏文件，并转到`~/.parts/etc/php5`。在那里，编辑`php.ini`文件:找到字符串“模块设置”，然后在它上面添加一行`extension=curl.so`:

![](img/1ab15bfd7b7bf65c3c573abe2192e5a8.png)

保存文件，并在控制台中运行`parts restart apache2`。您可能会得到类似这样的错误

```
(98)Address already in use: AH00072: make_sock: could not bind to address 0.0.0.0:3000 no listening sockets available, shutting down
AH00015:  Unable to open logs
parts: ERROR:  "/home/action/.parts/packages/apache2/2.4.7/bin/apachectl start" failed Aborting! 
```

如果你这样做了，只需再次运行相同的命令，它应该工作。

现在再次尝试在端口 3000 上预览之前的`index.php`文件，如果一切顺利，消息应该显示 cURL 现在已经安装。

![](img/ed84d9042467832a8a0e2f6f7ea6c4fb.png)

您可以通过将内容改回`phpinfo()`并查找卷曲部分来进一步确认:

![](img/281b9171788f44fea2ce5ede7f1008b4.png)

### 下载和构建 Phalcon

根据他们网站上的说明，我们克隆回购协议，然后构建扩展。

```
cd ~/.tools
git clone --depth=1 git://github.com/phalcon/cphalcon.git
cd cphalcon/build ./install
```

在最后一步中，我们不需要`sudo`,因为 Nitrous boxes 使用的`action`帐户权限很高，超过了典型用户帐户的权限。

编译一完成，Phalcon 的构建脚本就会自动将`phalcon.so`文件复制到 PHP 扩展文件夹中。然后，您可以按照上述相同的过程来激活扩展。输入`php.ini`，将`extension=phalcon.so`加入混音，如下图所示:

![](img/8815569f42689c3ce4496eda8f57dade.png)

运行`parts restart apache2`后，重新检查之前的`phpinfo()`输出，应显示安装了 Phalcon:

![](img/4927c867ef3c2bbd1bc305f232fa8338.png)

## 结论

在本教程中，我们展示了在 Nitrous.io 上安装定制 PHP 扩展的简易性。这个过程可以应用于在任何地方安装 PHP 扩展——您不需要仅仅为了添加一个捆绑的扩展而重新构建整个 PHP 源代码。随着时间的推移，随着受欢迎程度的提高，Nitrous 可能会增加一种更简单的方式来引入扩展——甚至可能通过它们的包管理器“parts”——但目前，我们仍然停留在这种手动过程中。虽然单调乏味，但肯定不难。

你玩过氮气了吗？如果有，你造了什么？如果没有，是什么阻止了你？请在下面的评论中告诉我们！

## 分享这篇文章