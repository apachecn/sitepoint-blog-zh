# 如何使用 PHPbrew 和 VirtPHP

> 原文：<https://www.sitepoint.com/use-phpbrew-virtphp/>

我们都遇到过只安装了一个版本的情况。也许那个版本就是安装在我们操作系统上的东西。也许它是捆绑在 MAMP/WAMP/XAMPP 的一个版本。

你如何切换 PHP 版本？

你如何切换到一个版本，然后再切换回来？

你如何着手切换 PHP 的版本，但只针对你电脑上的一个应用程序？

Ruby 和 Python 社区拥有处理这种情况的工具已经很多年了。PHP 现在也有了，但是远远不够大张旗鼓。

## PHPbrew

PHPbrew 是一个自 2012 年问世的令人惊叹的小工具。

它构建并安装 PHP 版本，并将它们放在您的主目录中的一个文件夹中。这意味着你可以管理你自己的 PHP 版本。PHPbrew 将构建各种版本，将它们放在 home 文件夹中，并允许您随时在它们之间切换。

### 安装 PHPbrew

值得注意的是，PHPbrew 有相当多的[需求](https://github.com/phpbrew/phpbrew/wiki/Requirement)，但是安装起来并不困难。我不需要安装任何东西，因为在使用这台 Macbook 两年多之后，我已经具备了所有的需求。

如果你是一个 Mac OS X 用户——我将继续假设你是——那么你可以使用自制软件(没有关系)来安装依赖项。

```
brew install automake autoconf curl pcre re2c mhash libtool icu4c gettext jpeg libxml2 mcrypt gmp libevent
brew link icu4c
```

然后，您需要安装 PHPbrew 本身:

```
curl -L -O https://github.com/phpbrew/phpbrew/raw/master/phpbrew
chmod +x phpbrew
sudo mv phpbrew /usr/bin/phpbrew
```

这会下载 PHPbrew，添加“可执行”权限并将其移动到`/usr/bin`目录。

跳到他们的[基本使用说明](https://github.com/phpbrew/phpbrew/#basic-usage)来看看如何更详细地初始化，但是基本应该是:

```
phpbrew init
```

phpbrew 初始化后，您需要将这些行添加到您的`.bashrc`:

```
echo "source $HOME/.phpbrew/bashrc" >> ~/.bashrc
```

如果你使用像 ZSH 这样的非默认 shell，那么你需要编辑你的`.zshrc`文件。

### 使用 PHPbrew 安装 PHP

在我们安装一个版本的 PHP 之前，我们需要看看 PHPbrew 有哪些可用的版本。我们可以用一个简单的命令做到这一点:

```
phpbrew known
Available stable versions:
5.6+	5.6.0
5.5+		5.5.17, 5.5.16, 5.5.15, 5.5.14, 5.5.13, 5.5.12, 5.5.11, 5.5.10
5.4+	5.4.33, 5.4.32, 5.4.31, 5.4.30, 5.4.29, 5.4.28, 5.4.27, 5.4.26
5.3+		5.3.29, 5.3.28, 5.3.27, 5.3.26, 5.3.25, 5.3.24, 5.3.23, 5.3.22
```

在撰写本文时，PHP 5.6.0 是最新版本，不支持 PHP 5.6.0 之前的版本。

我们想安装 PHP 5.6.0，这样我们就可以使用所有伟大的新功能，所以让我们请 phpbrew 来做:

```
phpbrew install 5.6.0
```

请注意，如果您使用的是 PHPbrew 1.14 或更早版本，那么在某些系统上这将会失败，并出现一个关于没有启用 XML 的错误。当 XML 丢失时，PHPbrew 将无法安装名为 PEAR 的东西，构建将会中断。我们可以使用`+xml_all`选项来解决这个问题:

```
phpbrew install 5.6.0 +xml_all
```

这个`+xml_all`选项就是 PHPbrew 所说的“变体”，还有很多可用的选项。

### 变体

当你自己安装 PHP 时，有很多选项来启用或禁用特性。PHPbrew 简化并抽象了它，使用了一个叫做 [Variants](https://github.com/phpbrew/phpbrew#variants) 的特性。

像数据库驱动程序、curl、GD 图像库和 JSON 都是可选的变体。

PHPbrew 有一个名为“default”的变体，与预期相反，默认情况下不使用它。相反，它充当启用以下变体的快捷方式:

*   bcmath(数学)
*   bz2
*   日历
*   硬币指示器 （coin-levelindicator 的缩写）命令行界面（Command Line Interface for batch scripting）
*   ctype
*   数字正射影像图
*   文件信息
*   过滤器
*   工业程序控制（ industrial process control 的缩写）
*   json
*   mbregex
*   mbstring
*   mhash
*   pcntl
*   pdo
*   可移植性操作系统接口
*   readline,
*   套接字
*   xml_all
*   活力

`default`可能包含比您需要的更多的内容，因此更细粒度的方法可能更合您的心意。

假设我们只想安装 PHP 5.6.0 来构建一个 CLI 应用程序，它使用 PDO 与 SQLite 数据库进行对话。为此，我们可以做到以下几点:

```
phpbrew install 5.6.0 +cli +pdo +sqlite +xml_all
```

该命令将使能 [PDO 扩展](http://php.net/manual/en/book.pdo.php)本身，`sqlite`使能 [SQLite 驱动](http://php.net/manual/en/ref.pdo-sqlite.php)。`cli`变种将安装命令行界面，`xml_all`将停止 PHPbrew 对 PEAR 的抱怨。

如果您在安装 PHP 版本时遇到任何问题，请尝试运行相同的命令，但添加`-d`选项。这将把调试信息发送到控制台，而不是发送到日志文件。

```
phpbrew install -d 5.6.0 +default +sqlite
```

### 切换 PHP 版本

因此，在这一点上，我们应该已经安装了 PHP 的一个版本。

如果我们的安装成功，那么 PHPbrew 将输出如下消息:

```
Congratulations! Now you have PHP with php-5.6.0.
To use the newly built PHP, try the line(s) below:

    $ phpbrew use php-5.6.0

Or you can use switch command to switch your default php version to php-5.6.0:

    $ phpbrew switch php-5.6.0
```

列出的第一个命令`use`将让您在控制台会话中使用 PHP 5.6.0。如果你关闭标签/窗口或者重启你的电脑，你将会回到默认的 PHP 版本。

第二个命令`switch`将切换 PHP 的默认版本，PHPbrew 将在新的会话中使用该版本。

让我们试着将默认版本设置为 PHP 5.6.0，看看是否可行。

```
$ phpbrew switch php-5.6.0
$ php -v
PHP 5.6.0 (cli) (built: Sep 30 2014 15:30:22) 
Copyright (c) 1997-2014 The PHP Group
Zend Engine v2.6.0, Copyright (c) 1998-2014 Zend Technologies
```

上面的输出向我们展示了我们想要看到的东西:`PHP 5.6.0`。

如果我们现在尝试安装旧的 PHP 5.5，我们可以再次使用`$ phpbrew known`来查看哪些版本可用。选择一个版本，并尝试安装它:

```
phpbrew install 5.5.17 +default +sqlite
```

这将安装带有`default`和`sqlite`变体的 PHP 5.5.17。为了使用 PHP 5.5.17，我们必须运行另一个命令:

```
$ phpbrew use php-5.5.17
$ php -v
PHP 5.5.17 (cli) (built: Sep 30 2014 17:41:05) 
Copyright (c) 1997-2014 The PHP Group
Zend Engine v2.5.0, Copyright (c) 1998-2014 Zend Technologies
```

现在我们可以在这个会话中使用 PHP 5.5.17，再次使用 PHP 5.6.0。由于我们使用了`use`命令而不是`switch`，当我们打开另一个标签或窗口时，PHP 5.6.0 将再次出现。

### PHPbrew vrs。系统

当我们使用 PHP 版本时，我们的 bash 会话将使用 PHP 版本的特殊路径。我们可以通过`which`命令找出正在使用的版本:

```
$ which php 
/Users/phil/.phpbrew/php/php-5.6.0/bin/php
```

如果我们想停止使用这个 phpbrew 版本，回到系统版本，我们可以使用`off`命令。

```
$  phpbrew off
phpbrew is turned off.
$ which php
/usr/bin/php
$ php -v
PHP 5.4.24 (cli) (built: Jan 19 2014 21:32:15) 
Copyright (c) 1997-2013 The PHP Group
Zend Engine v2.4.0, Copyright (c) 1998-2013 Zend Technologies
```

我们再次使用默认版本。你可能不需要这个，但是知道如何摆脱 phpbrew 是很方便的。`which`命令也将帮助您进行调试。

PHPbrew 本身就是一个有用的工具，您可能会发现这就是您所需要的。也就是说，当您发现各种项目需要更多扩展时，这些版本的 PHP 会变得复杂。PHPBrew *可以*添加 PECL 扩展，但是不能逐个项目地添加。

它还假设您能够记住应用程序应该使用哪个版本的 PHP。它可能不是默认的，在另一个版本下运行它可能会导致问题。

为此，我们需要在 PHPbrew 上使用另一个工具。

## VirtPHP

VirtPHP 允许你在一台机器上创建独立的 PHP 环境，就像 [Python 的 virtualenv](https://www.sitepoint.com/virtual-environments-python-made-easy/) 一样。这听起来可能有点复杂，但这个想法很简单。

首先，标记一个包含应用程序或组件的目录，并给它命名。想象一下，我们正在开发“airpair-api ”,它是一个 PHP 应用程序，我们想让它成为自己的环境。

然后，我们可以安装“airpair-api”需要的 PECL 扩展，而不会影响其他应用程序。

这就是理论，让我们看看我们是如何做到这一点的。

### 安装 virtPHP

转到 [virtPHP 版本页面](https://github.com/virtphp/virtphp/releases)并找到最新版本。它会有一个“virtphp.phar”的链接，你需要右击并复制这个 URL。

```
$ wget https://github.com/virtphp/virtphp/releases/download/v0.5.1-alpha/virtphp.phar
chmod +x virtphp.phar
sudo mv virtphp.phar /usr/bin/virtphp
```

现在我们可以检查它是否工作:

```
$ virtphp -V
virtPHP version v0.5.1-alpha 2014-08-13 16:05:47
```

### 创造环境

VirtPHP 对使用哪个版本的 PHP 保持一种宽松的态度。当您创建一个环境时，它将获取您的控制台会话中的 PHP 版本并引用该版本。

因此，在我们尝试创建一个新环境之前，我们需要确定我们使用的是正确的版本。

```
$ which php           
/Users/phil/.phpbrew/php/php-5.6.0/bin/php
```

哎呀，它仍然使用默认版本，我想确保我的代码库工作在 PHP 5.5 上。

```
$ phpbrew use 5.5.17
$ which php           
/Users/phil/.phpbrew/php/php-5.5.17/bin/php
```

完美，当前启用的 PHP 版本是 5.5.17，在这个例子中是我们想要的版本。

现在我们可以创造一个环境。

```
virtphp create airpair-api
```

您将看到大量输出，如果进展顺利，您应该会看到以下内容:

```
Your virtual php environment (airpair-api) has been created!
You can activate your new environment using: ~$ source /Users/phil/.virtphp/envs/airpair-api/bin/activate
```

此时，新环境可以使用了，但是还没有启用。复制它给你的命令并运行它，或者运行这个更短的版本:

```
$ source ~/.virtphp/envs/airpair-api/bin/activate
```

现在您应该在控制台提示符中看到环境名称`(airpair-api)`，在`$`字符之前。这让您知道您处于一个环境中，因此您可以停用它或相应地采取行动。

### 在沙盒里玩耍

现在我们有了这个环境，我们可以在不影响其他 PHP 安装的情况下安装和配置东西。

使用环境的一个很好的用例是能够安装 PECL 扩展。你不仅可以测试一个应用程序在有或没有扩展的情况下如何工作，还可以尝试不同的版本。

```
(airpair-api) $ pecl install xdebug
(airpair-api) $ pecl install memcached-1.0.2
```

这有助于我们安装强大的调试工具 [Xdebug](http://xdebug.org/) ，并安装[memcached]扩展。

在撰写本文时，PECL 命令通过基于 PEAR 的系统安装软件包。在 virtPHP 的未来版本中，PECL 扩展将通过新的改进的 [Pickle](https://github.com/FriendsOfPHP/pickle) 系统进行安装。这将消除 OS X 在支持 PEAR 方面的一些问题。

### 退出环境

要检查您是否还在使用某个环境，两件事会有所帮助。第一个线索是在命令提示符下看到括号中的环境名称。第二种方法是使用`which php`并查看它是否指向 virtPHP 环境。

```
(airpair-api) $ which php
/Users/phil/.virtphp/envs/airpair-api/bin/php
(airpair-api) $ deactivate
$ which php
/Users/phil/.phpbrew/php/php-5.6.0/bin/php
```

在这里，您可以看到我们正在使用`airpair-api`环境。然后在停用它之后，控制台退回到使用从 PHPbrew 安装的 5.6.0，因为这是默认的。

## 结论

像这样使用许多已安装的版本，乍一看似乎有点令人困惑。实际上，这里要学的东西比试图教一个初学者所有关于完整堆栈的东西要少得多。

如果一个新的开发人员试图构建一个基本的 PHP 应用程序，传统上他们会通过以下步骤开始:

*   Apache/nginx
*   关系型数据库
*   虚拟主机和/或 etc/主机
*   黑掉核心 OS PHP 版本
*   也许安装 XAMPP/WAMP/MAMP
*   尝试升级核心版本或*AMP 版本
*   搞不清楚系统 PHP 和 MAMP PHP 有什么不同
*   尝试安装 PECL 扩展到 MAMP 版本，但安装到系统 PHP 代替

你可以用一个工具来避免这种痛苦，比如一个流浪者和一个供应脚本，但是这是假设这个初学者在一个团队中。如果单独行动，初学者会有更多的困难。

这就是 Ruby on Rails 社区多年来的做法。教初学者一个框架，抽象出许多困难的东西，让他们构建，让他们在成长过程中了解更多。

获取您的 PHP 版本，安装您需要的东西，用`php -S`运行 PHP 开发服务器，并且只在您需要的时候(或者如果需要的话)增强您的堆栈。

开发/生产奇偶校验很重要，但是如果只是一个简单的小 HTTP 服务，有时您可以不必太在意。如果你已经有了 CI 测试，那就更是如此。

最后，即使您不想通过开发服务器运行代码，拥有 PHPbrew 和 virtPHP 仍然是有用的。你可以安装新版本，只要他们出来玩新的语法，而不是打破你所有的应用程序。

## 分享这篇文章