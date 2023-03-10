# 用 PHP 构建跨平台窗口应用

> 原文：<https://www.sitepoint.com/platform-windowed-apps-php/>

Java 如此受欢迎的原因之一是因为它可以用来构建可以在包括 Windows、Linux 和 Solaris 在内的多种平台上运行的应用程序、网页、小程序和 beans。Java 运行一个叫做 JVM 的虚拟机，代码被编译成一种叫做 Java 字节码的中间格式，这种格式是独立于平台的。当在 JVM 中执行这段特定的代码时，JVM 在编译代码的同时，为它运行的平台优化代码。

微软的最新技术，。NET 遵循相同的原则。代码被编译成微软中间语言(MSIL)，然后在。NET 框架作为应用程序域。微软希望将 C#和 MSIL 标准化。NET 代码可以跨平台运行。

那么所有这些和 PHP-GTK 有什么关系呢？嗯，Java 和。NET 可以用来构建窗口应用程序和网页。多亏了 PHP-GTK，我们现在也可以用 PHP 构建跨平台的窗口应用程序。

在这篇文章中，我们将看看 PHP-GTK:它是什么，如何下载和安装它，以及如何使用它来构建跨平台的交互式 GUI 应用程序。要测试本文中的 PHP-GTK 示例，您应该在 Windows、Linux 或 Unix 机器上安装 PHP 4.1.1 或更高版本。

##### 什么是 PHP-GTK？

就个人而言，我认为 PHP-GTK 是一组优秀的类，可以用来构建包含按钮、窗口、工具栏、菜单、滚动条、数据库访问、列表等等的应用程序。开发 PHP-GTK 的团队将其定义为 PHP 扩展，允许我们轻松编写跨平台的基于 GUI 的应用程序。

有趣的是，写 PHP-GTK 是为了证明 PHP 实际上可以用来构建网页以外的东西，让我告诉你，他们已经成功了。

毫无疑问，你对 PHP 这个缩写词很熟悉，但是 GTK 是什么意思呢？GTK 代表 GIMP 工具包，它基本上只是一组我们可以用来创建应用程序的小部件。小部件相当于“Linux speak”中的控件(如按钮、列表框、框架或单选按钮)。

GIMP 是 GNU 图像处理程序的首字母缩写，是一个运行在 Linux 上的全功能图形编辑程序。它拥有许多(如果不是全部)流行的 Windows 程序的功能，如 Photoshop 和 Paintshop。这是大多数 Linux 用户选择的图形编辑器。

GTK 实际上是用 C 语言编写的一系列库的一部分，叫做 GTK+。GTK+是随着时间的推移建立起来的，现在是 Gnome 的主要部分，Gnome 是一个 Linux GUI 桌面环境。GTK+具有面向对象的特性，还包括另外两个库:

1.  GLib: 一个工具库，可以用来帮助开发者用 GTK+创建应用。

3.  **GDK:** 与用于 Win32 的 GDI 类似，GDK 是 GIMP 绘图工具包的标准，它将一组低级绘图函数封装到类中，使得用 GTK+开发应用程序更加容易。如果你沿着 MFC for C++的思路思考，那么你就做了一个公平的比较:MFC 包装了几个控件，并对开发人员隐藏了对底层 Windows API 的调用。GDK 为 GTK+做了同样的事情。

虽然 GTK 在许多其他应用程序和项目中使用，但就 PHP-GTK 而言，它是一组高级的类，可以被引用来创建小部件，然后我们可以通过编程来操纵它们。PHP-GTK 的编程风格类似于事件驱动编程语言，如 Visual Basic 和 C++，因为它发出信号。这些信号可以被捕获，并且可以定义特定的函数(称为回调函数)来处理它们。

我知道我们甚至还没有看 PHP-GTK，但是考虑下面的 PHP 代码:

```
$button = &new GtkButton("Click me"); 

$button->connect("clicked", "buttonClicked");
```

在上面的代码中，我实例化了一个新的`GtkButton`类。然后，我调用了它的 connect 函数，传递了两个参数:要捕获的信号的名称(`clicked`)以及当这个信号被捕获时要执行的回调函数的名称(`buttonClicked`)。这告诉 PHP-GTK，当按钮发出“点击”信号时，应该调用`buttonClicked`函数。

所以如果我们像这样添加一个`buttonClicked` 函数:

```
function buttonClicked() 

{ 

 echo "You clicked the button"; 

}
```

那么当按钮被点击时,“你点击了按钮”将回显到屏幕上。

这让我想到了另一点:PHP-GTK 应用程序是通过普通的 PHP 解释器调用它们来运行的。你调用 PHP-GTK GUI 应用程序的控制台窗口位于后台，应用程序位于顶部。当应用程序运行时，您仍然可以使用 echo、print 等输出到控制台窗口。

在我看来，这非常好，因为这意味着你可以设置一个调试标志，把控制台窗口当作一个调试窗口，在你构建 PHP GTK GUI 应用程序时输出调试语句。当您准备好发布应用程序时，只需将 debug 标志设置为 false 或删除 echo 命令。

希望现在你对 PHP-GTK 有一点了解了。下一步是实际下载并安装 PHP-GTK，让我们开始吧！

##### 下载和安装 PHP-GTK

和所有 GPL 许可软件一样，PHP-GTK 是完全免费下载的。访问 GTK 网站,选择与你的操作系统相匹配的 PHP-GTK 版本。在撰写本文时，您可以选择下载源代码、windows 二进制文件以及并发版本系统(CVS)下载。

***为 Windows 安装 PHP-GTK***

我运行的是带有 PHP-GTK 的 Windows 2000，所以我会给你我在 Windows 上安装 PHP-GTK 的概述。首先，确保你安装了一个解压程序，比如 WinZip。接下来，找到 PHP 的安装目录。如果你使用默认选项安装了 PHP，它将是 c:php4。

将下载的 zip 文件中的所有文件解压到一个目录中，比如 c:temp。将 php4 目录中的所有文件复制到当前的 php 扩展目录中，该目录可以在 php.ini 文件中找到(通常是 php 的安装目录，如 c:php4，或者是 PHP 安装的扩展目录，如 c:php4extensions)。

您会注意到一个名为 php_win.exe 的文件。这个可执行文件可以用来运行 PHP-GTK 脚本，只需双击它们。在继续之前，让我们设置窗口，这样当我们双击它们时，我们的 PHP-GTK 脚本将自动运行。

双击你电脑上的任何 PHP 脚本。当“打开方式…”对话框出现时，选择“其他”并浏览到 c:php4php_win.exe。确保“总是使用此程序”复选框被选中，然后单击“确定”。你会注意到所有的 PHP 文件现在都有了 PHP-GTK 图标。

好了，回到装置上来。您的 PHP-GTK 下载中的 winnt 目录包含一个 php.ini 文件。如果您的 windows 目录中还没有 php.ini 文件(对于 Windows 95 和 98 是 c:windows，对于 Windows NT、2000 和 XP 是 c:winnt)，那么将 php.ini 文件复制到那里。

接下来，将所有文件从下载的 winntsystem32 目录复制到 Windows 系统目录，对于 Windows 95 和 98 用户，该目录为 Windows system 对于 Windows NT、2000 和 XP 用户，该目录为 c:winntsystem32。

运行 sysedit(开始->运行-> sysedit)，并将以下行添加到 autoexec.bat 文件的末尾:

```
path=%path%;c:php4
```

最后，将测试目录复制到安装 PHP 的同一个目录中。测试目录包含几个 PHP-GTK 脚本，演示了如何创建各种 PHP-GTK 文件并与之交互。切换到 c:php4test 目录并双击 hello.php。您应该会看到一个包含一个按钮的 PHP-GTK 窗口。如果一切正常，那么你已经成功安装了 PHP-GTK。

此时重新启动 Windows 可能也是一个好主意。

***为 Linux 安装 PHP-GTK***

我将向你展示如何使用 PHP 的 CVS 服务器下载 PHP-GTK。在你可以安装 PHP-GTK 之前，确保你有[PHP 4 . 1 . 1 或更高版本](http://www.php.net)、 [CVS](http://www.cvshome.org) 和[Linux GTK+库](http://www.gtk.org/download)，以及:

*   libtool 1.3.3 或更高版本，
*   automake 1.4 或更高版本，
*   autoconf 2.12 或更高版本，bison 1.28 或更高版本，以及
*   flex 2.54 或更高版本

已安装并正常工作。

首先，使用`phpfi` 作为密码登录 CVS 树:

```
cvs --d :pserver:cvsread@cvs.php.net:/repository login
```

接下来，从 PHP 的 CVS 服务器下载 PHP-GTK 到你的机器上:

```
0  

cvs -d :pserver:cvsread@cvs.php.net:/repository co php-gtk
```

切换到下载 PHP-GTK 的目录:

```
cd php-gtk
```

运行 phpize 工具，它应该在您安装 PHP 时就已经安装了。它应该在 phpize 路径指向的目录中。

```
phpize
```

现在你需要配置、制作和安装 PHP-GTK:

```
./configure && make && make install
```

最后，运行下面的命令来执行下载 PHP-GTK 时安装的 hello.php 脚本。您应该会看到一个包含一个按钮的 PHP-GTK 窗口:

```
php --q test/hello.php
```

##### 构建我们的第一个 PHP-GTK 应用程序

现在 PHP-GTK 已经安装好了，我们准备创建我们的第一个 GUI 应用程序。创建一个名为 window.php 的新 PHP 脚本，并在其中输入以下代码:

```
<?php   

 // Load the PHP-GTK extension   

 $extension = "php_gtk" . (strpos(PHP_OS, "WIN") >= 0 ? ".dll" : ".so");   

 dl($extension);   

 // Create a new window   

 $window = &new GtkWindow();   

 $window->set_title("Test App");   

 $window->set_usize(300, 100);   

 $window->set_position(GTK_WIN_POS_CENTER);   

 $window->set_policy(false, false, false);   

 // Set a callback function for the destroy signal   

 $window->connect("destroy", "killwin");   

 function killwin()   

 {   

 echo("Window Destroyed!n");   

 gtk::main_quit();   

 }   

 // Show the window   

 $window->show_all();   

 // Start the main PHP-GTK listener loop   

 gtk::main();   

?>
```

切换到您创建 window.php 的目录，并使用以下命令执行它:

```
php --q window.php
```

以下是 window.php 在我的 Windows 2000 机器上的运行结果:

![697_fig1](img/874af855680487ee45040118e6f354dd.png)

##### 我们的第一个 PHP-GTK 窗口

如果你摆弄这个窗口，你会注意到你不能调整它的大小或最大化它，因为 window.php 调用了各种 GTK 函数。当您关闭窗口时，您会注意到一些文本已经输出到控制台窗口:

```
C:>php -q window.php   

Window Destroyed!
```

让我们来看一下我们用来创建 window.php 的代码。

```
// Load the PHP-GTK extension   

$extension = "php_gtk" . (strpos(PHP_OS, "WIN") >= 0 ? ".dll" : ".so");   

dl($extension);
```

如注释所示，我们加载 PHP-GTK 扩展。`PHP_OS`变量包含一个代表你的操作系统的单词。在我的机器上，`PHP_OS`是“WINNT”。我们将`$library`变量的值设置为`php_gtk.dll`(对于 Windows)或`php_gtk.so`(对于 Linux)，这取决于 `PHP_OS`是否包含字符串“WIN”。然后我们调用`dl()` 函数将扩展加载到内存中。

```
// Create a new window   

$window = &new GtkWindow();   

$window->set_title("Test App");   

$window->set_usize(300, 100);   

$window->set_position(GTK_WIN_POS_CENTER);   

$window->set_policy(false, false, false);
```

任何应用程序最重要的方面是用户界面。PHP-GTK 定义了一个名为`GtkWindow`的类，我们可以通过引用实例化它来创建一个新窗口。`GtkWindow`类包括几个函数，我们可以用它们来指定窗口的外观、位置等等。在我们的例子中，我用`set_title`设置了窗口标题，用`set_usize`设置了它的宽度和高度，把它的位置设置在屏幕的中央，还用`set_policy`告诉 PHP-GTK 不能调整窗口的大小。

```
// Set a callback function for the destroy signal   

$window->connect("destroy", "killwin");   

function killwin()   

{   

 echo("Window Destroyed!n");   

 gtk::main_quit();   

}
```

大多数 PHP-GTK 小部件都包含一个名为`connect()`的方法，它允许我们指定在捕获特定事件时应该执行的回调函数。PHP-GTK 库监控我们的应用程序的方式与其他任何面向对象、事件驱动的编程语言非常相似。PHP-GTK 基于信号和回调的概念，这意味着如果我们将鼠标移动到一个按钮上，就会发出一个信号。我们可以注册一个回调函数来告诉 PHP-GTK 在发出这个特定信号时运行哪个(哪些)函数。

在我们的例子中，我使用了 connect 函数来告诉 PHP-GTK，当我们的窗口发出“销毁”信号(当我们关闭窗口时发出)时，它应该执行我们的`killwin`函数。我们的`killwin`函数包含一个对 gtk 类的静态函数`main_quit`的调用，它卸载我们的窗口并终止我们的应用程序。

```
// Show the window   

$window->show_all();   

// Start the main PHP-GTK listener loop   

gtk::main();
```

`show_all()`函数显示我们的窗口，如果它包含任何小部件，那么这些也会被显示。gtk 类的主函数被静态调用，并告诉 PHP-GTK 库开始它的监听器循环。

##### 注册多个回调函数

PHP-GTK 的一个伟大之处在于，你可以为任何信号注册多个回调函数。如果您需要在单击一个按钮、关闭一个窗口、展开一棵树等情况下执行多个任务，这将非常方便。

让我们快速看一下如何为我们在 window.php 中创建的窗口的 destroy 信号注册多个回调函数:

```
// Set a callback function for the destroy signal    

$window->connect("destroy", "function1");    

$window->connect("destroy", "killwin");    

function function1()    

{    

 echo("This is some output before the killwin function is called.n");    

}    

function killwin()    

{    

 echo("Window Destroyed!n");    

 gtk::main_quit();    

}
```

正如您所看到的，我已经添加了对我们的 windows connect 函数的另一个调用，指定它在被销毁时也应该调用 function1 函数。我已经在调用`connect("destroy", "killwin")`之前调用了`connect("destroy", "function1")`。这是需要记住的重要一点:注册回调函数的顺序就是它们执行的顺序。

使用新的回调处理程序和函数运行 window.php 时，控制台窗口的输出如下所示:

```
C:>php -q window.php    

This is some output before the killwin function is called.    

Window Destroyed!
```

##### 将 GTK 小部件添加到我们的窗口

所以，我们已经创建了一个基本的 PHP-GTK 窗口并设置了 GTK 循环，但是当我们甚至不能与它交互时，我们的应用程序还有什么用呢？让我们添加一个文本框和按钮到我们的窗口。当我们点击按钮时，文本框中的任何文本都将使用 PHP 的 strrev 函数进行反转。

在我们看代码之前，我们需要了解一下`GtkEntry`和`GtkButton`类。`GtkEntry`类是一个单行文本框，允许我们输入、复制、粘贴和删除文本。`GtkButton`类是一个可以点击、双击等的按钮。`GtkEntry`和`GtkButton`类都支持 connect 事件来注册回调函数。

创建一个名为 reverse.php 的新文件，并在其中输入以下代码:

```
<?php    

 // Load the PHP-GTK extension    

 $extension = "php_gtk" . (strpos(PHP_OS, "WIN") >= 0 ? ".dll" : ".so");    

 dl($extension);    

 // Create a new window    

 $window = &new GtkWindow();    

 $window->set_title("Test App");    

 $window->set_usize(300, 50);    

 $window->set_position(GTK_WIN_POS_CENTER);    

 $window->set_policy(false, false, false);    

 // Set a callback function for the destroy signal    

 $window->connect("destroy", "killwin");    

 function killwin()    

 {    

 echo("Window Destroyed!n");    

 gtk::main_quit();    

 }    

 // Add a GtkVBox class to our window    

 $box = &new GtkVBox();    

 $window->add($box);    

 // Add a GtkEntry class to our window    

 $entry = &new GtkEntry();    

 $entry->set_text("Hello World");    

 $box->pack_start($entry);    

 // Add a GtkButton class to our window    

 $button = &new GtkButton("Reverse Text");    

 $button->connect("clicked", "reverseText", $entry);    

 $box->pack_start($button);    

 function reverseText($theButton, $theEntry)    

 {    

 $text = strrev($theEntry->get_text());    

 $theEntry->set_text($text);    

 }    

 // Show the window    

 $window->show_all();    

 // Start the main PHP-GTK listener loop    

 gtk::main();    

?>
```

运行 reverse.php。当您单击按钮时，文本框中的文本将被反转，如下所示:

![697_fig2](img/f426fb8bf8f288cb1a3850b9dfd58432.png)

是时候介绍一个关于 PHP-GTK 的新概念了。简单地说，PHP-GTK 不支持窗口小部件的绝对定位，这意味着我们不能在窗口左边 10 像素、右边 20 像素的地方添加一个文本框。PHP-GTK 只支持相对定位，这需要我们定义可以放置小部件的区域。

`GtkHBox`和`GtkVBox`类创建水平和垂直框，作为其他小部件的容器。在上面的例子中，我们创建了一个新的`GtkVBox`对象，并将其添加到我们的窗口中，如下所示:

```
// Add a GtkVBox class to our window     

$box = &new GtkVBox();     

$window->add($box);
```

所有作为容器的 GTK 小部件(即可以容纳其他小部件)都公开了 add 函数，该函数接受一个参数:一个小部件。然后，这个小部件显示在该容器中。

`GtkVBox`小部件从其基类继承了许多功能。所有 GTK 小部件都是从基类派生的，所以大多数小部件都有相似的功能。下面是`GtkVBox`类的层次结构:

```
GtkObject     

|-- GtkWidget     

|---- GtkContainer     

|------ GtkBox     

|-------- GtkVBox
```

`GtkBox`类公开了一个`pack_start`函数。该函数用于向`GtkBox` 类或派生类添加一个小部件。在我们的例子中，我们创建了一个新的 `GtkEntry`和`GtkButton` 类，使用其`pack_start`函数将它们添加到我们的`GtkVBox`对象中，如下所示:

```
// Add a GtkEntry class to our window     

$entry = &new GtkEntry();     

$entry->set_text("Hello World");     

$box->pack_start($entry);     

// Add a GtkButton class to our window     

$button = &new GtkButton("Reverse Text");     

$button->connect("clicked", "reverseText", $entry);     

$box->pack_start($button);
```

正如你在上面的例子中看到的，我们为按钮的“点击”信号注册了一个回调函数。回想一下我们的第一个 window.php 例子，其中我们的连接调用只包含两个参数。在上面的例子中，我们指定了第三个参数，这是我们的`GtkEntry`小部件`$entry`。我们指定的回调函数是`reverseText`，看起来是这样的:

```
function reverseText($theButton, $theEntry)     

{     

 $text = strrev($theEntry->get_text());     

 $theEntry->set_text($text);     

}
```

`reverseText`函数接受两个参数:发出信号的小部件(在我们的例子中是按钮)和一个自定义参数(是我们的`GtkEntry`小部件)。第一个参数`$theButton`是由 PHP-GTK 隐式传入的，当我们为按钮的“点击”信号定义回调函数时，我们手动传入了第二个参数。

我们的`reverseText`函数通过调用它的`get_text` 函数从我们的`GtkEntry`对象获取文本。使用 PHP 的`strrev`函数反转该文本，然后通过调用其`set_text`函数将其放回到我们的`GtkEntry`对象中。

##### 其他 PHP-GTK 小部件

PHP-GTK 有许多其他的小部件，我们可以在应用程序中实例化和使用它们。这里有一个列表:

*   **GtkButton:** 可以点击的按钮。
*   **GtkCalendar:** 显示可选择日期的日历。
*   **GtkCheckButton:** 可以开关的复选框。
*   **GtkCList:** 有几列可以滚动的列表。
*   GtkColorSelection: 一个非常酷的小工具，可以用来选择颜色。
*   GtkCombo: 一个下拉列表，也可以接受输入的文本。
*   **GtkCTree:** 可以包含可以展开和折叠的节点的树形列表。
*   **GtkDialog:** 可以是模态的弹出对话框。
*   **GtkEntry:** 一个可以更改文字的文本框。
*   GtkFileSelection: 类似于 Windows 通用对话框控件，GtkFileSelection 控件显示一个对话框，允许我们选择本地机器上的文件。
*   **GtkFontSelection:** 一个允许我们选择各种字体属性的小部件。
*   **GtkFrame:** 可以包含标题的框架。
*   **GtkHRuler:** 水平标尺。
*   **GtkHScrollBar:** 水平滚动条。
*   **GtkImage:** 显示图片的小工具。
*   **GtkMenu:** 一个可以保存菜单项的容器小部件。
*   **GtkNoteBook:** 可以显示多个选项卡的选项卡式控件。
*   **GtkProgressBar:** 可以用来显示某个事件进度的条。
*   **GtkRadioButton:** 可以分组的单选按钮。
*   **GtkSpinButton:** 用于使用箭头增加/减少数值。
*   **GtkToolTips:** 用于给其他小工具添加工具提示。
*   **GtkVBox:** 立式箱式容器。
*   **GtkVScrollBar:** 垂直滚动条。

*   **GtkWindow:** 一个应用程序窗口。

从上面显示的类中可以看出，PHP-GTK 包含了所有必要的类，有助于开发一个完整的跨平台 GUI 应用程序。

##### 用 PHP-GTK 建立一个数据库应用程序

PHP-GTK 很酷的一点是它与 PHP 无缝集成，所以我们仍然可以在我们的 PHP-GTK 应用程序中使用 PHP 的所有常规函数。PHP 使用的最流行的数据库是 MySQL，所以让我们创建一个 PHP-GTK 应用程序，它连接到 MySQL 数据库并检索一些记录。

在 MySQL 控制台应用程序中输入以下命令:

```
create database people;      

use people;      

create table programmers      

(      

 pId int auto_increment,      

 pName varchar(50),      

 pAge tinyint,      

 primary key(pId),      

 unique id(pId)      

);
```

使用以下插入命令向我们的程序员表中添加一些人:

```
insert into programmers values(0, 'Bill Smith', 23);      

insert into programmers values(0, 'James Black', 45);      

insert into programmers values(0, 'Lyonel Jones', 18);      

insert into programmers values(0, 'Phil Brown', 22);      

insert into programmers values(0, 'Michael Clay', 56);
```

现在让我们创建一个 PHP-GTK 应用程序，它连接到我们的数据库并从程序员的表中检索记录。这个应用程序有点大，不能完整地发布在这里，所以如果你想在你的机器上测试它，请下载本文的[支持材料。](http://webmasterbase.com/examples/php-gtk/supportfile.zip)

我们的应用程序首先创建几个标签和文本框，以及一个命令按钮，单击该按钮将连接到 MySQL 服务器:

```
$button = &new GtkButton("Connect and Get Rows");       

$button->connect("clicked", "getdata");       

$box->pack_start($button);
```

![697_fig3](img/007d3c04f51be880bc07344fbbe2c9fb.png)

`getdata`函数使用我们应用程序中`GtkEntry`控件的主机、用户名和密码值连接到 MySQL:

```
function getdata($theButton)       

{       

global $server;       

global $user;       

global $pass;       

...       

$dServer = $server->get_text();       

$dUser = $user->get_text();       

$dPass = $pass->get_text();       

$s = mysql_connect($dServer, $dUser, $dPass) or die("Couldn't        

connect to database server");       

$d = mysql_select_db("people", $s);
```

接下来，占据我们的`GtkVBox`小部件的所有控件都被隐藏:

```
// Hide all controls       

$label1->hide();       

$label2->hide();       

$label3->hide();       

$server->hide();       

$user->hide();       

$pass->hide();       

$button->hide();
```

然后，它查询程序员的表，遍历每个结果并将其添加到一个字符串变量中:

```
$result = mysql_query("select * from programmers order by pName asc");       

$pList = "";       

while($row = mysql_fetch_array($result))       

{       

 // Create a string array       

 $pList .= $row["pName"] . " is " . $row["pAge"] . "n";       

}
```

然后，它创建一个新的`GtkLabel` 小部件，并通过将变量 `$pList` 的值传递给 labels 构造函数，将该值赋给标签。然后标签被添加到 `GtkVBox`小部件中，它是我们主窗口的一部分:

```
// Create a GtkCList and show it       

$p = &new GtkLabel("$pList");       

$box->add($p);       

$p->show();
```

这是窗口之前的样子:

![697_fig4](img/207b75e71d899179c46e2cf8538542ab.png)

…在我点击连接按钮后:

![697_fig5](img/5ed154796c159bb98254f6b0daa9950e.png)

如你所见，`GtkLabel`和`GtkEntry`对象被隐藏，新标签显示在`GtkVBox`对象的内部。

##### 结论

在本文中，我们已经介绍了开始用 PHP 和 PHP-GTK 库创建跨平台 GUI 应用程序所需的一切。我们已经看到了如何下载和安装 PHP-GTK，以及用它创建简单应用程序的几种方法。

当然，PHP-GTK 包含了大量的其他功能和小部件，所以如果你想了解更多关于 PHP-GTK 的信息，一定要看看下面的链接。您也可以使用下面显示的链接下载本文的支持材料。

*   [PHP-GTK 手册](http://gtk.php.net/manual/en/)
*   [Sourceforge](http://sourceforge.net/) ，全球最大的开源开发网站。
*   本文的[支持材料](http://webmasterbase.com/examples/php-gtk/supportfile.zip)。

## 分享这篇文章