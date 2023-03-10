# 在 Windows 上使用 PostgreSQL 和 PHP

> 原文：<https://www.sitepoint.com/use-postgresql-php-windows/>

我想每个 SitePoint 的访问者都已经看过“使用 PHP & MySQL 的数据库驱动网站”这句话无数次了。你并不是每天都能看到“PHP & PostgreSQL”的组合，尤其是不能与后置脚本“…on Windows”组合在一起。

有些人认为在 Windows 上运行 PostgreSQL 非常困难，因为没有易于使用的 Win32 安装程序(在撰写本文时，7.3.4。是最新版本)。用户必须首先安装某种 unix 模拟器，并在这个“平台”上运行数据库系统。

PostgreSQL 技术文档网站说，“主要的 PostgreSQL 项目计划是在我们的 7.4 版本中添加对原生 Windows 版本的支持。这个大概会在 2003 年年中发布…”我还没看到这个发布…可能 2003 年末？然而，这意味着，在未来，PostgreSQL 将是比 MySQL 更有吸引力的选择！

用 PostgreSQL 建立一个数据库驱动的网站？你为什么要做这种事？如果你使用 PHP，你的数据库不就是 MySQL 吗——句号！？

对我来说，直到我被分配了将 ASP/MSSQL 解决方案转换成 PHP/PostgreSQL 的任务。变化的原因？

1.  我们(分配者)不想再使用微软产品(由于相关的许可费用)
2.  我们想要 MySQL 中没有的数据库特性(外键等)

对我来说，当我们执行这种“转换”时，在同一台(Windows)开发机器上安装旧的 ASP 和新的 PHP 似乎也是一个好主意。尝试 Windows、PostgreSQL 和 PHP 组合的另一个很好的理由是，找到那个古老问题的答案总是很有趣，“它能有多难？”

我必须承认，我是一个微软式的开发人员。我主要使用 MSSQL，较少使用 MySQL，几乎从不使用 PostgreSQL。操作系统也是如此；90%的时间我用 Windows，10% Linux。尽管如此，我迁移到 PostgreSQL 的过程还是愉快地结束了。如果对我来说可行，那对你也是可行的。

更多关于 MySQL 替代方案的阅读，请参阅 Wayne Luke 的文章[超越 MySQL——高端数据库解决方案](https://www.sitepoint.com/high-end-database-solutions/)。

请注意，本教程假设您已经阅读了 Kevin Yank 的优秀文章《使用 PHP 和 MySQL 构建您自己的数据库驱动网站》的第一章，或者其他一些解释如何在 Windows 上安装 Apache 和 PHP 的教程。更重要的是，我假设在学会这个过程后，你已经成功安装了一个带有 PHP“插件”的 Web 服务器。

我们开始吧！

##### Cygwin 安装

第一个任务是下载并安装 [Cygwin](https://cygwin.com/) ，这是一个类似 linux 的 Windows 环境。在撰写本文时，Cygwin 的最新版本是 1.5.5。将 setup.exe 重命名为 cygwin_setup.exe 并不是一个坏主意——这样，当您以后想要添加/删除/更新 cygwin 组件时，该文件将更容易定位。
当然，当你启动安装程序时，你应该以管理员组的用户身份登录。

Cygwin Net 版本设置程序将询问您以下八个问题:

1.  **Disable Virus Scanner?**
    As some antivirus programs may cause problems during the Cygwin installation, you have the option to disable the virus scanner temporarily. In most cases, the default option Leave Virus Scanner Alone works. (If no antivirus program is detected, then — sooner or later you’ll encounter another type of problem…)
2.  **Choose Installation Type**
    Select Install from Internet.
3.  **Choose Installation Directory**
    You can use the default options here too, unless you have some really good reason not to designate C:\cygwin as Root Directory, Install For All Users and select Unix as Default Text File Type.
4.  **Select Local Package Directory**
    The folder you specify here is the location where the downloaded installation files will be saved. Enter the path of your choice, e.g. E:\cygwinfiles.
5.  **Select Connection Type**
    Here you select the appropriate connection type; the default option is Direct Connection. Most likely, your antivirus/firewall program will pop up after you click Next, to check if you want to allow cygwin_setup.exe to access the Internet. Yes, that’s exactly what we want!
6.  **Choose Download Site**
    Select any site from the list, preferably one that’s close to you. Depending on your connection speed, it will take a moment or two before the next screen appears.
7.  **Select Packages**
    You only need to make two additions to the by-default selected packages; cygrunsrv (category Admin) and postgresql (category Database). Cygrunsrv is an NT/W2K service initiator, and PostgreSQL is… well, you know what it is. At the time of writing, the version of the Database Management System is 7.3.4.

    如果您是一个控制狂，请验证 cygipc (category Devel)是否在预先选择的包中。Cygipc 为 Cygwin 提供 ipc(进程间通信)支持，但最终将被“cygwin-daemon”所取代。在撰写本文时，PostgreSQL 依赖于 cygipc。

    现在你可以休息一下，喝点咖啡，同时下载和安装文件。

8.  **创建图标**
    嗯，这最后一个屏幕是不言自明的。

点击完成，当你看到…

安装完成！

##### 安装服务

安装完成？不，还没有。文件就在那里，在 C:\cygwin 中的某个地方，或者您选择放置它们的任何地方。现在您必须安装服务，创建一个 postgres 用户并初始化 PostgreSQL。

这些说明基于 Jason Tishler 编写的 postgresql-7.3.4.README 文件，该文件位于/usr/doc/Cygwin 中。在该文件中，您可以找到基本 Cygwin PostgreSQL 安装的说明，如果您运行的是 Windows 95/98/ME，这是您唯一的选择(但如果您问我，对于运行 Windows 9x 的人来说，唯一的选择是:升级！).

启动新安装的 Cygwin Bash Shell 并完成以下步骤:

***安装 ipc-daemon2 作为 NT 服务***

正如您在 Cygwin 安装中所记得的，Cygipc 是 PostgreSQL 所依赖的进程间通信守护进程。

```
ipc-daemon2 --install-as-service
```

如果您是一个好奇的人(像我一样)，请在 Windows (services.msc)中启动服务 MMC 控制台管理单元，并验证列表中是否有一个名为 Cygwin IPC Daemon 2 的新服务。

***创建新的用户账号***

PostgreSQL 文档中的一段引文指出:“建议在单独的用户帐户下运行 PostgreSQL”。

所以，我们来添加一个新用户，由于缺乏想象力，称这个新用户为“postgres”。请注意，您应该将下面的`********`替换为您想要用于 postgres 的密码。

您可以在一行中发出一个带有所有选项(fullname、comment、homedir)的单一 net user 命令，但是我在这里将它们作为单独的命令来编写，以使文本更具可读性。要查看 net user 语法，请键入“net user /？”。

```
net user postgres ******** /add   

net user postgres /fullname:postgres   

net user postgres /comment:'PostgreSQL User Account'   

net user postgres /homedir:"$(cygpath -w /home/postgres)"
```

如果您愿意，可以在本地用户和组 MMC 管理单元(compmgmt.msc)中查看 postgres 是否加入了该团队。还应该有一个新创建的文件夹，名为 C:\cygwin\home\postgres。

***更新文件/etc/passwd***

```
mkpasswd -l -u postgres >> /etc/passwd
```

上面的命令翻译成英语，意思是:将指定用户 postgres 的信息附加到/etc/passwd。请注意，应该有两个大于号！

***授予 postgres 作为服务登录的权限***

为什么？这是相当明显的；postgres 是稍后应该运行 postmaster 服务的用户。启动本地安全设置 MMC 管理单元(secpol.msc ),并在本地策略用户权限分配下将 postgres 添加到可以作为服务登录的用户列表中。

***创建数据目录***

当然，这是 PostgreSQL 存储数据的地方。

```
mkdir /usr/share/postgresql/data
```

***使 postgres 成为数据区的主人***

另一件自然的事；当然，只有 postgres 应该拥有数据目录。

```
chown postgres /usr/share/postgresql/data
```

稍后，初始化过程将撤销除 postgres 之外的所有人的权限。

***安装邮局主管作为 NT 服务***

Postmaster 是 PostgreSQL 服务器的名称，位于/usr/bin 中。Postmaster 也会是你创造的服务的名字(谁说的“缺乏想象力”？！).

```
cygrunsrv -I postmaster -p /usr/bin/postmaster   

    -a "-D /usr/share/postgresql/data -i"   

    -y ipc-daemon2 -s INT -u postgres -o 
```

注意，这个命令在这里被分成了三行，以便于阅读，但是你应该把所有的内容写在一行上。

哇哦。所有这些选项意味着什么？翻译:安装(`-I`)一个名为 postmaster 的新服务，应用路径(`-p` ) /usr/bin/postmaster，参数(`-a`)"-D/usr/share/postgresql/data-I "，这反过来意味着 postmaster 应该使用/usr/share/PostgreSQL/data 作为数据目录(`-D`)，应该启用 TCP/IP 连接(`-i`)。

接着，postmaster 服务依赖于(`-y`)另一个名为 ipc-daemon2 的服务，当终止时发送信号 INT(`-s`)，由用户(`-u` ) postgres 运行，并在系统关闭期间停止(`-o`)。

Cygrunsrv 会以交互方式向您(两次)询问密码。

要查看 cygrunsrv 语法，请键入“cygrunsrv -h”。如果您想了解有关 PostgreSQL 服务器选项的更多信息，请键入“post master–help”。

现在，如果您在服务 MMC 控制台管理单元(services.msc)中查看，您将在列表中看到另一个服务。双击 postmaster 以查看您刚刚分配的不同属性(登录、依赖等。).

***创建数据库集群***

在启动 postmaster(服务)之前，必须初始化数据库存储。您必须以 postgres 用户的身份运行命令`initdb`。

注意:以 postgres 用户身份登录！为了避免所有可能的问题，请从 Windows 注销，然后再次登录，但这次是以 postgres 的身份登录(即不要使用 cygwin 中的`su`命令来切换用户)。

启动 Cygwin Bash Shell 并输入:

```
initdb -D /usr/share/postgresql/data
```

开关`-D`用于指示数据区的位置(很容易猜到，不是吗？).现在您可以注销，并再次以您自己的身份登录。

***测试看看 PostgreSQL 是否工作***

最后，您现在应该能够连接到 PostgreSQL 了。启动 Cygwin，以用户 postgres 的身份连接，并使用 initdb 创建的数据库模板 1。

```
psql -U postgres template1
```

如您所知，数据库`template1`是默认的模板，当您使用`createdb`命令或 CREATE DATABASE 语句创建一个新数据库时，会复制这个模板。

如果出于某种原因，您收到类似“psql:无法连接到服务器”的消息，请使用以下命令启动 postmaster 服务:

```
net start postmaster
```

要了解有关如何使用 psql(PostgreSQL 交互式终端)的更多信息，请键入“psql–help”。我想我们可以在这里定义一个通用规则:任何命令加上“help”都会向您显示一些有用的文本！

##### 从 PHP 连接到 PostgreSQL

打开 php.ini 文件(很可能位于 C:\winnt 中)，找到名为 Dynamic Extensions 的部分，并取消对包含“extension=php_pgsql.dll”的行的注释。保存并关闭 php.ini，然后重启 Apache。如果 Web 服务器抱怨说无法加载动态库，您可能在 php.ini 中输入了错误的 extension_dir 路径(path 和 Directories 部分)。另一个可能的原因是 php_pgsql.dll 文件不在 extensions 目录中。

现在，您应该能够运行下面的小 PHP 脚本了:

```
<?   

$conn = pg_connect("host=localhost    

    port=5432    

    dbname=template1    

    user=postgres    

    password=********");   

$sql = "SELECT current_date AS today;";   

$result = pg_query($conn, $sql);   

$row = pg_fetch_object($result, 0);   

echo "Today is: " .$row->today;   

?>
```

该脚本并不太令人兴奋，但至少它向您展示了如何连接到数据库并执行查询。

注意，上面的代码并不是 PHP 最佳实践的例子。当然，你应该添加错误处理，当你完成时使用`pg_close()`来关闭连接。数据库模板 1 应该用作模板(还有什么？)用于您为应用程序创建的新数据库，您应该创建新用户并授予他们适当的权限(即不要以 postgres 的身份连接)。

有关如何创建数据库、用户、表等的更多信息。，参见 [PostgreSQL 文档](http://www.postgresql.org/docs/)。

更多关于如何使用 PHP 的 PostgreSQL 函数的阅读，请参阅文章[将你的站点从 MySQL 迁移到 PostgreSQL 第 1 部分](https://www.sitepoint.com/site-mysql-postgresql-1/)和[第 2 部分](https://www.sitepoint.com/site-mysql-postgresql-2/)，当然还有 [PHP 文档](http://www.php.net/manual/en/ref.pgsql.php)。

##### 有用的工具

如果你是一个真正的极客，命令行 SQL 可能会很有趣，但为了管理数据库，我使用了 AquaFold 公司的 Aqua Data Studio v 3.0。嗯，它不是 MSSQL 企业管理器，甚至不是 MySQL 控制中心，但它给你一个比通过 psql 交互式终端更好的对象视图。Aqua Data Studio 的一个很好的特性是，你可以连接到其他类型的数据库，并在相同的树结构中查看；MySQL、MSSQL、Oracle 等等。

如果你只是想管理你的 PostgreSQL 数据库， [pgAdmin](https://www.pgadmin.org/) 就是你的工具。我猜测“全球数以万计的开发者”不可能完全错误。请记住，这不是一个软件审查，因此寥寥数语。另一个选择是使用 MS Access(毕竟你运行的是 Windows，所以很大概率你也有 MS Office)作为 PostgreSQL 的前端，但是之后你要先安装 [psqlODBC 驱动](https://odbc.postgresql.org/)。

既然你有 Apache 和 PHP，你可以使用 phpPgAdmin T1，这是一个基于浏览器的工具，与 MySQL 的 phpMyAdmin 并不完全不同。

##### 收场白

希望您觉得这篇文章很有趣，您迫不及待地想亲自尝试安装过程，现在您正忙于吸收关于 PostgreSQL 的知识(见上面的链接)。如果你是 MySQL 用户，我相信你会“发现”MySQL 中缺少的有用功能。如果您是 MSSQL 用户，您会发现 PostgreSQL 是免费提供的，而且功能强大。

请注意，Cygwin 安装程序中没有卸载程序。如果你想在阅读完本教程后删除你添加的内容，你必须手动删除…

*   HKEY _ 当前 _ 用户\软件\小天鹅解决方案
*   HKEY _ LOCAL _ MACHINE \ SOFTWARE \ Cygnus 解决方案
*   HKEY _ LOCAL _ MACHINE \ SYSTEM \ current control set \ Services \ IPC-daemon 2
*   HKEY _ LOCAL _ MACHINE \ SYSTEM \ current control set \ Services \ post master
*   postgres 用户帐户
*   Cygwin 安装目录(别忘了；您必须成为数据目录的所有者，才能删除它)
*   本地包目录

祝你好运！

## 分享这篇文章