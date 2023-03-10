# 使用 SQL Server Express 在 Windows 上开始使用 PHP

> 原文：<https://www.sitepoint.com/sql-server-php/>

在最近的一篇文章中，我们看到了使用 web Platform Installer 在 Windows 上用 PHP、IIS 和 SQL Server Express 安装并运行 Web 服务器是多么容易。现在。让我们看看如何将这些工具结合起来构建一个简单的 web 应用程序。

*别忘了看看最后的[测验，由微软赞助，有可能赢得一份免费的 SQL Server Developer edition。数量有限，所以赶快开始吧。](https://www.sitepoint.com/quiz/microsoft/sql-server-php/)*

## 开始使用

Microsoft Web Platform Installer，简称 WPI，是 Web 应用程序和相关软件的统一安装程序。WPI 可以安装 PHP、Microsoft SQL Server。NET 框架和各种开源应用程序，这里仅举几个例子！安装程序还为这些组件配置 Internet 信息服务(Windows 附带的 web 服务器)，因此入门很容易。但是在我们开始构建我们的 web 应用程序之前，我们将使用 WPI 来设置我们的开发环境。

本文假设您对 PHP 和关系数据库有基本的了解——如果您能编写 PHP 和 SQL 代码来操作数据库记录，那么您就可以开始了。我们将快速浏览 PHP，以便将重点放在平台的强大和简单上。

### 安装 PHP 和 SQL Server

首先，访问 WPI [下载页面](http://www.microsoft.com/web/Downloads/platform.aspx)并获得安装程序的副本。你需要一个运行 Windows XP SP2+、Server 2003 SP1+、Server 2008、Vista SP1 或 Windows 7 的系统；我用 Vista SP1 为这个教程。安装程序本身只有 1MB，但是当安装程序下载平台的组件时，要为几百兆的数据传输做好准备。

在我们开始安装之前，请检查您的系统是否安装了 IIS。IIS 与大多数 Windows 版本捆绑在一起，但是它不包含在家庭版中。检查控制面板中程序和功能下的 Windows 功能(注意，虽然名称不同)以验证是否安装了 IIS 如果没有，只需在继续之前安装 IIS。

打开 Web 平台安装程序，查看可用的选项。你可能会在应用标签下认出一些开源应用，比如 WordPress 安装人员可以轻而易举地安装这些应用程序和他们需要的所有平台软件。

切换到 Web 平台选项卡，开始安装我们需要的组件。使用定制链接选择组件。至少，我们需要选择 Web 服务器 > 应用开发 > CGI ，框架和运行时>PHP>PHP 5 . x，以及数据库下的所有内容。如果您阅读了上一篇文章[在 Windows 上安装 PHP 的简单方法](https://www.sitepoint.com/article/php-windows-web-platform-installer)，其中的一些组件可能已经安装好了。[图 1，“要安装的组件”](#fig_components "Figure 1. Components to be installed")显示了我的设置使用的组件(加上 SQL Server Management Studio):

**图一。要安装的组件**

![Components to be installed](img/cb2228384cba82b2292fd9c741a348cb.png)

请注意，SQL Server 2008 Native Client 被列为 PHP 驱动程序的依赖项。native client 软件为 PHP 驱动程序提供 ODBC 功能，即使在使用 SQL Server 2005 实例时也是必需的。

单击我接受，在安装过程中，将要求您为新的 SQL Server 设置配置身份验证。在本教程中我们将使用 SQL Server 认证，因此选择混合模式认证，而不是 Windows 集成认证；不过，请注意，两者都适合开发。我们将保留默认用户名`sa`，但是您可以选择任何您想要的密码。

**warning:** Enter a Strong Password

即使 WPI 没有提醒您，如果您输入弱密码，SQL Server Express 的安装也不会完成。 [WPI 故障诊断页面](http://learn.iis.net/page.aspx/604/troubleshoot-microsoft-web-platform-installer-issues/)提供了确保您的密码安全所需的详细信息。

继续让 WPI 下载并安装这些项目。如果您遇到任何挑战，请访问 [WPI 故障排除页面](http://learn.iis.net/page.aspx/604/troubleshoot-microsoft-web-platform-installer-issues/)。

### 您的新 SQL Server 安装

平台安装程序完成后，SQL Server 应该在您的计算机上启动并运行。使用安装程序放在开始菜单中的快捷方式启动 SQL Server Management Studio。

**图二。开始菜单链接**

![Start menu link](img/6d627212291465963caa2308564a8659.png)

您将被提示连接到一个 SQL Server 实例，如图[图 3，“连接到一个服务器实例”](#fig_sql_connect "Figure 3. Connecting to a server instance")所示。为此，请使用您之前设置的用户名和密码组合(或您的 Windows 登录凭据，如果您选择了 Windows 身份验证)。

**图 3。连接到服务器实例**

![Connecting to a server instance](img/76aa579b6efd4be1bfba563206164848.png)

注意，`BUSLN1`是我的计算机名，`SQLEXPRESS`是 WPI 安装的 SQL Server (Express Edition)的默认实例名。这应该已经填写好了，但是您可能需要从认证方法列表中选择 SQL Server 认证。如果无法连接，检查 SQL Server 是否正在运行:打开服务控制台( Start > Run 并输入 **`services.msc`** )如图[图 4，“SQL Server 正在运行”](#fig_services "Figure 4. SQL Server is running")，检查 SQL Server 是否已启动。

**图 4。SQL Server 正在运行**

![SQL Server is running](img/cece2eede763bc43218ff55ac21e892d.png)

一旦连接完毕，SQL Server Management Studio (SSMS)将显示您的数据库以及其他服务器信息，如图 5 中的“SSMS 界面”所示。

**图 5。SSMS 接口**

![The SSMS interface](img/2e0888a443d6fa42724f51e12af7cae5.png)

SSMS 允许您配置、管理您的 SQL Server 实例，以及管理其数据库。有关 SSMS 的更多信息，请参见[介绍 MSDN 上的 SQL Server Management Studio](http://msdn.microsoft.com/en-us/library/ms174173.aspx) 。

如果您像我一样安装了 IIS 的数据库管理器，那么您也可以使用 IIS 管理器中的数据库管理器来验证您的数据库服务器已经启动并正在运行(它在首页的管理部分下)。然而，数据库管理器希望连接到一个特定的数据库，所以您必须首先使用 SSMS 创建一个数据库。

## 从 PHP 访问 SQL Server

WPI 安装了 PHP 的最新版本，并为其配置了 IIS。让我们检查一下 IIS 是否已经启动并正在运行。最简单的方法是访问 [http://localhost/](http://localhost/) 并检查默认的 IIS 页面是否显示在您的浏览器中。

加载如图 6[所示的 IIS 管理器，“IIS 管理器界面”](#fig_iis_manager "Figure 6. The IIS Manager interface")——它在控制面板的管理工具下，但是如果你在开始菜单搜索栏中键入 **`IIS`** 它也会出现。

**图 6。IIS 管理器接口**

![The IIS Manager interface](img/0472df137e643d11c2ef0b69bf4faec2.png)

在这里，我们可以配置和管理我们的 IIS 服务器，以及管理托管网站。因为 IIS 包含在大多数版本的 Windows 中，所以 WPI 只是添加并启用了某些必需的组件，比如 CGI 组件。你看到的选项可能和上面的不完全一样，但是只要你启用了 CGI，你就应该准备好了。

如果您之前在 [http://localhost/](http://localhost/) 无法访问您的 IIS 服务器，请单击您的 web 服务器(左侧窗格中的顶级条目，很可能标有您的计算机名；例如，对我来说是巴士 1 。在右侧的动作窗格中点击开始。然后选择默认网站并使用右侧的动作窗格来确保网站也在运行。

现在让我们确保 PHP 安装正确。我们的服务器将通过 FastCGI 模块执行 PHP，这是 IIS 的推荐方法。首先，在左侧的连接下选择您的服务器的顶部条目，双击中间窗口的模块(它是分组在 IIS 下的组件之一)。

**图 7。模块列表**

![The Modules list](img/8bf2658209f1b7ee00614dea3c1f5189.png)

你应该在这个列表中看到 FastCgiModule ，如图[图 7，“模块列表”](#fig_modules_list "Figure 7. The Modules list")。如果没有，点击右侧的配置本机模块，使能 FastCgiModule 。如果 FastCgiModule 不可用，点击 Register ，将模块命名为 FastCgiModule ，选择`%windir%System32inetsrviisfcgi.dll`作为模块的路径，然后将其启用。

现在选择您的默认网站，打开模块部分，并再次确保 FastCgiModule 在列表中。(如果没有，使用前面的说明放在那里。)

最后，选择默认网站，双击处理程序映射。

**图 8。处理器映射列表**

![Handler Mappings list](img/23364e543c664fa2c3f91cdb8b6ef051.png)

当安装 PHP 时，WPI 应该添加一个名为 PHP_via_FastCgi 的条目，如图 8[中的“Handler Mappings list”](#fig_handler_mappings "Figure 8. Handler Mappings list")。但是，如果这个选项没有出现在列表中，点击右边的添加模块映射，输入 **`*.php`** 作为请求路径，从模块列表中选择 **`FastCgiModule`** ，选择你的`php-cgi.exe`二进制作为路径——这个应该在`C:Program FilesPHPphp-cgi.exe`或者类似的地方。(记得在可执行路径周围使用引号，因为在`Program Files`中有空格。)选择一个类似 **`PHP via FastCgi`** 的名称，点击确定。IIS 将提示您为 PHP 创建一个 FastCgi 应用程序，如图[图 9，“添加模块映射”](#fig_add_mapping "Figure 9. Adding a module mapping")所示。

**图 9。添加模块映射**

![Adding a module mapping](img/8b16cf632a8c9ae30413593389629b7e.png)

现在我们准备在 IIS 下运行我们的 PHP 应用程序。您的默认网站将有一个类似于`C:inetpubwwwroot`的文档根目录—您可以使用 IIS 管理器来更改它。现在，删除这个文件夹中的默认 IIS 文件并创建一个`index.php`文件。打开您最喜欢的文本编辑器，添加以下内容:

```
<?phpphpinfo();exit;
```

切换到您的网络浏览器，再次访问`http://localhost/`(如果您没有删除 IIS 启动页面，则访问`http://localhost/index.php`)。搜索`sqlsrv`，这是微软提供的 PHP 原生 SQL Server 驱动程序。跳过第一个条目，您应该会发现驱动程序已经安装并启用，如图[图 10，“来自 phpinfo 的 SQL Server 信息”](#fig_phpinfo "Figure 10. SQL Server information from phpinfo")所示。

**图 10。SQL Server 信息来自`phpinfo`**

![SQL Server information from phpinfo](img/812d63f8e66294c1182c529d3b419387.png)

现在让我们创建一个数据库，并使用 PHP 检索它。切换回 SQL Server Management Studio，连接到您的数据库服务器(如果尚未连接),并创建一个新数据库。只需右键点击数据库，选择新建数据库… ，显示新建数据库对话框，如图[图 11，“创建新数据库”](#fig_create_db "Figure 11. Creating a new database")。

**图 11。创建新数据库**

![Creating a new database](img/9b1055bebfdf9b987f74b9115a4532ac.png)

您将看到在 Databases 下为您的新数据库出现了一个新条目。展开数据库的条目，右键单击表，并选择新表… 来创建一个新表。

**图 12。创建新的数据库表**

![Creating a new database table](img/de999f2d43ec76a4c6d936ed95045961.png)

您可以使用右边的属性窗格更改表名，输入模式信息(这里我们在一个名为`people`的表中只有一个 ID 和一个`name`字段)，并通过右键单击列名字段将您的`id`字段设置为主键。对于一个 *自动递增* 字段的等价物，选择 ID 作为右边表设计器下的标识列。这并不完全必要，但我认为经验丰富的 MySQL 用户会选择这个选项。

最后，点击保存按钮(或者点击**Ctrl**+**S**)来创建你的表格。您将看到它出现在左侧的对象浏览器中，如图[图 13，“新表已创建”](#fig_new_table "Figure 13. New table created")。

**图 13。新表格已创建**

![New table created](img/170bb9e745bdb7f3c41df2562c2e219c.png)

右键点击左边的 dbo.people ，点击编辑前 200 行，将一些哑数据录入一行，如图[图十四，“录入数据”](#fig_enter_data "Figure 14. Entering data")。

**图 14。输入数据**

![Entering data](img/c88fe61f1be099a5c276dda6c3329399.png)

现在回到你的文本编辑器，打开我们之前处理过的`index.php`文件，用下面的内容更新它:

```
<?php$server = "*`BUSLN1\SQLEXPRESS`*";$options = array(  "UID" => "sa",  "PWD" => "*`password`*",  "Database" => "test");$conn = sqlsrv_connect($server, $options);if ($conn === false) die("<pre>".print_r(sqlsrv_errors(), true));echo "Successfully connected!";sqlsrv_close($conn);
```

您肯定需要更改以斜体显示的选项—这些选项应该反映您之前在 SQL Server Management Studio 的连接对话框中输入的配置信息。此外，我们使用`\`而不是 `in the server name because `is the escaping character in PHP. Change `$server`来反映您的计算机名，并用您选择的密码替换`“PWD”`数组值。如果在安装 SQL Server 时选择了 Windows 身份验证，请省略`“UID”`和`“PWD”`行；SQL Server 将使用您当前的 Windows 帐户向数据库服务器进行身份验证。``

 ``此代码尝试使用本机 SQL Server 驱动程序连接到本地 SQL Server 实例。该 API 与 PHP 自带的`mysql`、`mysqli`或`pgsql`驱动略有不同，但很容易上手。关于驱动程序的更多细节，请参见 MSDN 主办的[文档](http://msdn.microsoft.com/en-us/library/ee229548(SQL.10).aspx)。

加载 http://localhost/index.php ，您应该会看到消息“成功连接！”如果没有，请检查您的 SQL Server 正在运行，并且这些凭据在 SQL Server Management Studio 中有效。PHP 驱动程序应该会给出相同的结果(包括错误消息的措辞)。有关连接到 SQL Server 的更多细节，请参见 [sqlsrv_connect 文档](http://msdn.microsoft.com/en-us/library/cc296161%28SQL.90%29.aspx)。

现在让我们检索之前创建的行。回到您的文本编辑器，在`"Successfully connected!"`行后添加以下代码:

```
$sql = "SELECT * FROM dbo.people";$query = sqlsrv_query($conn, $sql);if ($query === false){  exit("<pre>".print_r(sqlsrv_errors(), true));}while ($row = sqlsrv_fetch_array($query)){  echo "<p>Hello, $row[name]!</p>";}sqlsrv_free_stmt($query);
```

这将从我们的`people`表中检索所有记录，并输出每条记录的`name`字段中的值。最后，我们调用`sqlsrv_free_stmt`来释放与查询语句相关的资源。最后一个调用是可选的，但是对于复杂的预准备语句和大型结果集来说会有所不同。

在你的网络浏览器中访问[http://localhost/index . PHP](http://localhost/index.php)；您应该看到`people`表中的每条记录都有一个条目。在 SQL Server Management Studio 中再添加几行，并刷新页面以获得新的结果集。现在我们知道了如何从 PHP 访问 SQL Server，我们已经准备好构建我们的 web 应用程序了！`` 

## ``分享这篇文章``