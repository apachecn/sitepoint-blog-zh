# Kev 的命令提示符备忘单文章

> 原文：<https://www.sitepoint.com/command-prompt-cheat-sheet/>

这些年来，我收到许多心烦意乱的初学者发来的信息，他们被卡住了，因为他们遵循的指令提到了可怕的 **MS-DOS 命令提示符**。这种越来越隐蔽的功能可以追溯到 Windows 还只是一层薄薄的 16 色面纱覆盖在最基本的 MS-DOS 操作系统上的时候。

作为一名 Web 开发人员，有许多原因可能需要在 Windows 中使用命令行。像 MySQL 和 Apache 这样的开源程序并不总是能让所有(或任何！)可以通过 Windows 提供的漂亮图标和菜单使用它们的功能，相反，相信像你我这样的开发人员会有一定程度的命令行悟性来帮助我们。

要运行 MS-DOS 命令提示符，请在开始菜单中单击它的图标。它可能在主*程序*列表中，或者在*附件*下，也可能被称为 *MS-DOS 命令提示符*或者仅仅是*命令提示符*，这取决于你拥有的是哪个版本的 Windows。无论如何，一旦你启动它，你会得到一个丑陋的黑色窗口。可怕吧。

命令提示符的整体思想是在提示符后键入命令。这里有一些信息可以帮助你开始使用这个神秘的工具:

*   像在文本编辑器中一样键入命令。您可以像在记事本中一样，使用左右箭头键删除或退格。但是，不要使用鼠标——命令提示符是严格由键盘驱动的。
*   在每个命令之后，键入 *Enter* 键来执行它。通常，会出现一条消息，通知您所执行命令的结果。如果消息告诉您命令有问题，不要觉得不好——显示错误消息是命令提示符最擅长的。
*   向上和向下箭头通常可用于调出之前的命令进行编辑并再次执行。

命令提示符本身会告诉您系统的位置。例如，如果您的提示如下所示:

```
C:WINDOWS>
```

这意味着您当前正在`C:`驱动器上的`WINDOWS`文件夹中工作。

要获得当前文件夹中所有文件和子文件夹的列表，请键入`dir`命令，这是*目录*的缩写，然后按回车键。下面是我这样做时的样子(我总是用粗体显示您键入的部分，以免混淆):

```
C:WINDOWS>dir
 Volume in drive C is WINDOWS ME 
 Volume Serial Number is 3436-18D4 
 Directory of C:WINDOWS
23/11/2001  05:09 PM    <DIR>          . 
23/11/2001  05:09 PM    <DIR>          .. 
23/11/2001  05:09 PM    <DIR>          SYSTEM 
23/11/2001  05:24 PM            21,520 WINSOCK.DLL 
... 
             249 File(s)     93,256,655 bytes
              27 Dir(s)   1,141,923,840 bytes free
```

如果您的目录列表太长，无法在命令提示符窗口中显示(这种情况经常发生)，请在`dir`命令后键入`/p`(即键入`dir /p`，然后按 Enter 键)。然后，您可以一次一页地查看目录列表，并键入任意键查看下一页，直到到达列表的末尾。

`cd`命令用于改变当前文件夹(即改变目录)。例如，如果你在`C:WINDOWS`文件夹中，你可以通过键入`cd system`然后回车来移动到`C:WINDOWSSYSTEM`目录。这是它在我电脑上的样子:

```
C:WINDOWS>cd system
C:WINDOWSSYSTEM>
```

看到提示是如何改变的，以指示我们现在工作的目录了吗？

我们可以通过键入`cd ..`返回到当前目录的父目录(在本例中，`C:WINDOWS`是`C:WINDOWSSYSTEM`的父目录)，因为`..`是一个特殊的名称，总是表示“当前目录的父目录”:

```
C:WINDOWSSYSTEM>cd ..
C:WINDOWS>
```

您可以通过键入驱动器名称来更改正在使用的驱动器。例如，要移动到`D:`驱动器，我这样做:

```
C:WINDOWS>d:

D:>
```

看看我们现在是在 D:？现在，如果我在`D:Program FilesMySQL`中安装了 MySQL，我可以像这样转到我的 MySQL 安装(`D:Program FilesMySQL`)的`bin`目录:

```
D:>cd "Program Files"
D:Program Files>cd MySQL
D:Program FilesMySQL>cd bin
D:Program FilesMySQLbin>
```

注意，我用引号将目录名`Program Files`括起来，因为它包含一个空格。如果我没有这样做，命令就不会起作用。上面的三个命令可以合并成一个命令，如下所示:

```
D:>cd "Program FilesMySQLbin"
D:Program FilesMySQLbin>
```

现在您已经进入了 MySQL `bin`目录，您可以使用`dir`命令查看其中包含的文件:

```
D:Program FilesMySQLbin>
 Volume in drive D is DATA1 
 Volume Serial Number is 18FA-3226 
 Directory of D:Program FilesMySQLbin 
04/12/2001  03:26 PM    <DIR>          .
04/12/2001  03:26 PM    <DIR>          .. 
24/11/2001  04:43 AM         1,110,016 mysqld-opt.exe 
24/11/2001  05:36 AM         1,990,656 mysqld-max.exe 
24/11/2001  05:30 AM         1,994,752 mysqld-max-nt.exe 
24/11/2001  05:17 AM         1,118,208 mysqld-nt.exe 
24/11/2001  04:12 AM         2,867,246 mysqld.exe 
24/11/2001  05:38 AM           274,432 isamchk.exe 
24/11/2001  05:39 AM           335,872 myisamchk.exe 
24/11/2001  05:40 AM           266,240 myisamlog.exe 
24/11/2001  05:41 AM           262,144 myisampack.exe 
24/11/2001  04:28 AM           278,578 mysql.exe
24/11/2001  04:28 AM           237,568 mysqladmin.exe 
24/11/2001  05:42 AM           237,568 mysqlbinlog.exe
21/10/2001  01:25 AM           334,712 mysqlc.exe 
24/11/2001  05:42 AM           229,376 mysqlcheck.exe 
24/11/2001  04:28 AM           241,664 mysqldump.exe 
24/11/2001  04:28 AM           225,280 mysqlimport.exe 
24/11/2001  04:29 AM           520,192 MySqlManager.exe 
24/11/2001  04:29 AM           229,376 mysqlshow.exe 
24/11/2001  05:44 AM            40,960 mysqlshutdown.exe
24/11/2001  05:45 AM            45,056 mysqlwatch.exe 
24/11/2001  05:45 AM           229,376 pack_isam.exe 
24/11/2001  05:46 AM           155,648 perror.exe 
24/11/2001  05:46 AM           172,032 replace.exe 
19/06/2000  03:51 AM               818 winmysqladmin.cnt 
03/11/2001  01:09 AM         1,167,872 winmysqladmin.exe 
19/06/2000  03:52 AM         1,856,816 WINMYSQLADMIN.HLP
              26 File(s)     16,422,458 bytes 
               2 Dir(s)   9,239,920,640 bytes free
```

看到所有的`.exe`文件？从命令提示符运行一个程序只需要在目录中输入它的名字。所以在上面的例子中，我现在准备运行诸如`mysqld`、`mysqladmin`和`mysql`这样的命令。

哦，当你完成后，你可以通过输入`exit`退出命令提示符。

## 分享这篇文章