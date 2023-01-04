# 在 Win32 上运行 PHP-GTK 和 PHP-Apache

> 原文：<https://www.sitepoint.com/run-php-gtk-php-apache-win32/>

设置场景:在 Windows 开发机器上，PHP 作为 Apache 模块愉快地运行。非常明智的是，你只在 Windows 上测试 PHP/Apache，并且总是上传一些*NIX 风格的文件来托管你的页面和应用程序。到目前为止，一切顺利。

然后你会听说 PHP-GTK，并看到用 PHP 开发 x 平台客户端 GUI 的巨大优势，而不是用 JAVA 或 VB。你已经知道 PHP 可以处理几乎任何你可以扔给它的东西，而且你已经对它了如指掌——那么你为什么不使用它呢？

所以你从 gtk.php.net 下载了最新的 PHP-GTK 二进制文件，并仔细按照说明进行操作……这就是问题开始出现的地方。让我们看看为什么，看看我们是否能让你回到在 Win32 上成功运行 PHP-GTK 和 PHP-Apache 的路上。

##### 安装

一旦你下载了 PHP-GTK，如果你要按照 PHP-GTK 的安装说明，你会打开一个命令提示符和类型 c:gtkphp4p HP . exe-q c:gtksamplestest . PHP。现在，当你点击'返回'，4 件事之一会发生:

1.  PHP-GTK 将按预期工作，不会影响您现有的 PHP-Apache 设置(首选，但不太可能)

3.  PHP-GTK 将按预期工作，但是当您启动 Apache 时，您会收到几个不寻常的错误
4.  PHP-Apache 将继续正常工作，但是 PHP-GTK 每次尝试都会失败
5.  PHP-GTK 和 mod_php 都不再工作了

为什么会这样？因为你按照说明做了！

许多人做的第一件事正是 PHP-GTK 下载告诉他们的——他们将 php4ts.dll 解压缩到 system32 文件夹中，从而覆盖任何现有的版本，并且，除非你非常幸运，否则会导致 mod_php apache 设置悲惨地失败。

请注意，如果在安装了 PHP-GTK 之后，apache-php 安装似乎失败了，那么您需要重新安装 PHP——Apache 不是问题所在。还要注意，如果您的机器上还没有 PHP-Apache，gtk.php.net 推荐的安装将会完美地运行，但是对于任何对 PHP-GTK 感兴趣的开发人员来说，这是一个不寻常的场景。

##### 为什么会失败？

推荐的安装可能失败有两个主要原因。

***1。PHP 版本***

由于 PHP-GTK 和 PHP 都在以不同的速度发展，让 PHP & PHP-GTK 二进制文件在同一个版本上编译并不总是容易的。因此，php _ GTK . dll & php4ts.dll 可能是针对 PHP4.1.* cvs 编译的，而您的主要 PHP 二进制文件是针对 4.2.1 编译的(希望您现在已经升级到 4.2.2)。

你可以[在这里](http://kromann.info/php4.php)获得更多最新的 PHP-GTK 版本，但是即使你有所有正确的版本，我仍然建议你在 PHP-GTK 开发周期的这个阶段使用下面的结构。为什么？简单地说，因为它不会出错——这必须(而且确实)工作，并且即使在您升级了 Apache-PHP 版本之后也将继续工作。

***2。**PHP . ini*

php.ini 问题部分与版本问题有关。php(作为一个 Apache 模块)使用的 php.ini 列出了几个不兼容的模块，加上完全不同的包含路径，还包括几个其他可能的绊脚石(例如 Zend optimiser)。同样，由于很难获得兼容的 GTK 和 PHP 版本，我们需要将两者完全分开。

…那么让我们开始吧！

##### 假设

我假设 PHP 作为 Apache 模块运行。

PHP 和 Apache 二进制文件的安装位置无关紧要，除非 PHP 安装到 C:PHP。PHP-GTK 将假设 PHP 安装到 C:PHP(除非你另外告诉它)，如果你已经在其他地方安装了它，你可能会发现一些 PHP-GTK 脚本可以工作，而其他的则不能。

##### 装置

从 gtk.php.net 获取最新的 GTK 二进制文件，将其解压缩到您选择的文件夹中(出于本教程的目的，我建议使用 c:gtk)，您应该有一个类似如下的结构:

*   php4 -> php 和 php-gtk 二进制文件
*   winnt ->默认的 php.ini 文件
*   winntsystem32 ->扩展使用的 gtk 二进制文件
*   样例->几个样例来演示用法

我会假设你解压到 c:gtk，虽然任何地方都可以。接下来，进入 winntsystem32 文件夹，获取其全部内容，并将这些文件放入 php4 文件夹。在 winnt 中对 php.ini 文件做同样的操作。完成后，您可以删除 c:gtkwinnt 文件夹(不要担心，您将一直有这个 zip 文件作为备份)。就是这样:装好了！

如果这看起来太容易了，其实很容易。然而，现在你要检查它是否工作。PHP-GTK 附带了几个示例脚本，但是让我们在这里快速完成一下。我们会让它变得越简单越好——一个带按钮的窗口——但是现在不用担心它是如何工作的。只需测试以确保它确实有效！

##### 测试

将下面的代码保存为-f c:gtksamplessimple.php(不要担心代码中有什么)。

```
<?   

dl('php_gtk.'.(strstr(PHP_OS, 'WIN') ? 'dll' : 'so'));   

function delete_event(){   

    return false;   

    }   

function shutdown(){   

    print("Shutting down...  

");   

    gtk::main_quit();   

    }   

function hello(){   

    global    $window;   

    print 'Hello World! "  

."';   

    $window->destroy();   

}   

$window = &new GtkWindow();   

$window->connect('destroy', 'shutdown');   

$window->connect('delete-event', 'delete_event');   

$window->set_border_width(10);   

$button = &new GtkButton('Hello World!');   

$button->connect('clicked', 'hello');   

$window->add($button);   

$window->show_all();   

Gtk::main();   

?>  
```

要运行这个脚本，我们需要使用只能被描述为一些不雅的命令行涂鸦。为此，我通常会写一个批处理文件——这比在 Windows 糟糕的命令窗口中打错字要容易得多。

在我们的命令行中有 3 个部分需要担心:

1.  PHP 之路

3.  php.ini 的路径和

5.  脚本的路径

```
cd c:gtkphp4php.exe -q
```

如果 php.exe 不在您的路径中，这个命令告诉 Windows 在哪里可以找到它(如果它在您的路径中，它可能是错误的 PHP . exe)。`-q`开关抑制 PHP 可能创建的任何 HTTP 头。

尝试在系统目录中使用现有的 php.ini 可能会导致许多错误，这取决于您对它进行了多少修改(不过，首先，您可以预期所有的包含和扩展路径都是错误的)。

`-c` 开关让我们指定一个替换的 php.ini 到缺省值，即 c:windows(WINNT)php.ini 或 c:PHP PHP PHP . ini 或？your _ pathphpphp.ini

```
-c c:gtkphp4php.ini
```

现在:确定我们想要运行的脚本的路径。开关告诉 PHP 这是一个文件，而不是其他参数。实际上，在大多数情况下，没有它你也能生活，但使用它几乎不是一件麻烦的事！

```
-f c:gtksamplessimple.php 
```

所有这些加在一起虽然笨拙却有效:

CD c:gtkphp 4 PHP . exe-q-c:gtkphp 4 PHP . ini-f c:gtks example . PHP

您可以看到批处理文件如此有用的原因(如果受虐狂愿意，他们可以在命令提示符下键入上面的内容)。我会将上述内容保存为 c:gtksimple.bat(或您机器上的任何地方),然后单击离开。瞧啊。一个没有蛇的 x 平台窗口。

总而言之，让你的 gtk 目录看起来像这样:C:gtkphp4

*   gdk-1.3.dll
*   glib-1.3.dll
*   gmodule-1.3.dll
*   gnu-intl.dll
*   gtk-1.3.dll
*   iconv-1.3.dll
*   libglade-0.14.dll
*   libxml.dll
*   php.exe
*   php.ini
*   php_gtk.dll
*   php_gtk_libglade.dll
*   php_gtk_sqpane.dll
*   php_win.exe
*   php4ts.dll

和一个测试目录 C:gtksamples

*   simple.php(以及其他国家)

这是最基本的——也是运行 PHP 实际需要的一切——GTK！

##### 注释和资源

***php_win.exe？***

你可能注意到了 php_win.exe。据我所知，php_win.exe 的唯一不同之处在于它允许你的 PHP-GTK 脚本在没有 DOS 窗口的情况下运行，否则每当你运行 PHP-GTK 应用程序时都会弹出 DOS 窗口。这对于演示来说非常好，但是请注意，如果您回显到 stdout，那么应用程序将会崩溃，所以如果您想使用 php_win.exe 进行调试，请将任何输出定向到文件。

此外，php_win.exe 似乎会首先查找扩展名等。在 C 中:PHP。如果找到它们，它将使用它们，而不是 system32 中的那些(这只是我的观察)。

***分机***

您的 c:gtkPHP4php.ini 应该没有启用扩展。我们 dl()GTK 模块，你将不得不在试错的基础上测试单独的扩展(MySQL/FTP 等都是可用的)。

##### 结论…

现在你已经开始运行了，你会想知道接下来会发生什么。我建议你从 gtk.php.net 下载 PHP-GTK 手册并浏览网站——手册中有一些有用的片段，以及一些应用程序和其他资源的链接，这些都是通过 PHP & PHP-GTK 统治世界的良好起点！尽情享受吧！

## 分享这篇文章