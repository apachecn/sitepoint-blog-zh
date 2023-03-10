# 如何将 WordPress 站点迁移到一个新的域名和主机

> 原文：<https://www.sitepoint.com/how-to-migrate-a-wordpress-site-to-a-new-domain-and-hosting/>

将你的 WordPress 站点迁移到一个新的主机上会有点压力。如果处理不当，可能会导致一些恼人的意外错误。然而，如果处理得当，这应该是一种没有压力的体验。

在本教程中，我将演示如何将一个 WordPress 站点迁移到一个新的主机和域名，避免迁移中遇到的许多常见问题。

注意:有许多方法可以实现迁移，在本教程中，我将向您介绍一种手动方法。这并不总是最容易的选择，但是一旦你理解了它是如何工作的，你就能更好地处理你在移动网站时遇到的任何问题。如果你正在寻找一个用于迁移的插件，试试这篇文章，这篇文章贯穿了【WordPress 备份和迁移的五个有用的插件，但是请记住，理解这里介绍的手动方法将会在使用这些插件时给你很大的帮助。

## 停用所有插件

在你开始之前，我建议你关闭所有的插件，因为当你把一个 WordPress 站点转移到一个新的主机上时，可能会失败的是你的 WordPress 插件。WordPress 本身设计得很好，可以适应新的变化，但是插件可能不会。

因此，要停用所有已激活的插件，进入管理页面-> **插件**->-**已安装插件** - >标记全部->-**批量操作** - > **停用** - > **应用**

![Deactivating Plugins](img/f636fd07257385e12dba6417cd525b2e.png)

## 备份 WordPress 数据库和文件

在做任何改变之前，最好总是备份你的网站，WordPress 迁移也不例外。有几个 WordPress 插件可以做到这一点，但是如果你想完全控制这个过程，手动方式是最好的。

现在，我将向你展示如何手动备份你的 WordPress 文件和数据库，也可以通过插件。

## 手动备份

手动备份你的 WordPress **数据库**:登录你的 phpMyAdmin，点击你的 WordPress 数据库并选择**导出**,如下图所示。

![Export Database](img/4f84a4e2b68ae4ddaec0a654626fe4fe.png)

确保您正在以 **SQL** 格式导出，然后点击 **Go** 。你的 WordPress 数据库将被下载为一个. sql 文件。我建议您将下载的 SQL 文件保存到计算机上的一个特殊文件夹中，以便以后可以方便地找到它。你也可以使用命令行来导出你的数据库的副本，你可以在这里找到[的更多信息。](https://codex.wordpress.org/Backing_Up_Your_Database#Using_Straight_MySQL_Commands)

手动备份你的**文件**:我建议你下载完整的 WordPress 安装文件夹，这样你就不会丢失任何东西。

使用任何 FTP/SFTP 客户端，如 [FileZilla](https://filezilla-project.org/download.php?type=client) ，通过 FTP/SFTP 登录到你的主机，下载你的 htdocs 或者 public_html 文件夹(或者你的 WordPress 安装所在的任何地方)。

### 使用插件备份

下载并安装 [UpdraftsPlus 备份和恢复](https://wordpress.org/plugins/updraftplus/)插件。在“已安装的插件”页面，找到并激活 UpdraftsPlus 插件。

安装好插件后，在管理页面，进入**设置** - > **更新 Plus 备份**，然后点击**备份**。

![Updraft Plus](img/dae0ef0d41343c5fbabad72d1874921c.png)

这可能需要一段时间，取决于你的 WordPress 站点的大小，但是一旦这个过程完成，点击**现有备份**标签下载备份到你的计算机，然后你也可以把它移动到一个特殊的文件夹。

## 准备上传您的备份

现在我们有了备份，我们需要修改一些东西，然后才能继续上传我们的备份。在继续之前，登录到新主机的 phpMyAdmin 并创建一个新的数据库。记下数据库的名称，因为在本教程的后面会用到它。

### 更新 wp-config.php 文件

在继续之前，我们的 WordPress 配置文件中有一些信息需要修改。解压你之前下载的 WordPress 安装文件夹的 ZIP 压缩文件，找到**wp-config.php**文件，用你喜欢的文本编辑器，比如记事本和 Notepad++打开它进行编辑。

编辑下面的行，替换为新主机的详细信息，然后保存。

```
define('DB_NAME', 'replace_with_database_name_you_noted_down');

/** MySQL database username */

define('DB_USER', 'replace_with_new_host_mysql_user_name');

/** MySQL database password */
define('DB_PASSWORD', 'replace_with_new_host_mysql_password');

/** MySQL hostname */
define('DB_HOST', 'replace_with_new_mysql_host'); 
```

## 上传您的备份

我们快完成了。下一步要做的是通过 FTP/SFTP 上传你修改后的 WordPress 安装文件夹到新的主机上。

上传完成后，继续上传您的数据库备份。

要上传数据库备份，请在新主机中登录 phpMyAdmin，然后单击 **Import** 菜单，如下所示。

![Import](img/838e07182f1db23dbe0481ff89790007.png)

选择 SQL 数据库备份并上传。

## 更新 WordPress 主页 URL 和站点 URL

为 WordPress 设置 URL 有几种方法，我们将讨论几种常见的方法。

如果你也要迁移到一个新域，确保你已经成功地将你的新域指向你的主机帐户(请参考你的主机支持文档以获得准确的细节)。

### 使用 wp-config.php

你可以通过添加下面几行到`wp-config.php`来设置你的 URL，这将覆盖并将禁用 WordPress 仪表盘中的这些选项。

```
 define('WP_HOME','http://example.com');
define('WP_SITEURL','http://example.com');

```

WordPress Codex 对这个有[很好的解释。](https://codex.wordpress.org/Changing_The_Site_URL)

### 使用 WordPress 主题 functions.php 文件

将下面几行粘贴到你的活动主题的 functions.php 文件中，紧接着初始的 `line, using the editor provided by an online FTP/SFTP client, or your preferred text editor.`

```
update_option ( 'siteurl', 'http://your_new_domain_name.com' );

update_option ( 'home', 'http://your_new_domain_name.com' ); 
```

用您的新域名 URL 替换`http://your_new_domain_name.com`。上面的代码会自动用数据库中新的博客 URL 更新`siteurl` `home`选项名称。访问你的 WordPress 登录或管理页面，你的网站应该工作。

一旦你的网站备份，确保你删除你添加到你的主题的 function.php 文件行，以防止每次你的网站被浏览更新。

### 手动更新 WordPress 主页 URL 和站点 URL

要手动更新你的 WordPress 主页 url 和站点 URL，重新登录到你的 phpMyAdmin，打开你的 WordPress 数据库，点击 **wp_options** 表，用你的新 URL 修改 **site_url** 和 **home** 列的值。

![Browse](img/3c2f063d1247d0064945eeb970eee54a.png)

## 重新激活所有插件并更新永久链接

现在一切正常，继续激活你需要的插件。为此，导航到**插件**->-**已安装插件**，从**批量操作**下拉列表中选择**激活**，并点击`Apply`按钮。

要更新您的永久链接，请进入**设置**->-**永久链接**，选择您想要的永久链接结构并**保存更改**。

## 301 重定向设置

要将你的旧域名 SEO 汁(或 SEO 值)转移到新域名，重要的是你要如下设置 301 重定向。

打开。htaccess 文件放在你的旧 WordPress 安装文件夹中，并附加下面的重写规则，这样任何使用你的旧域名访问你的站点的人都会被重定向到新的域名。

```
<IfModule mod_rewrite.c>

RewriteEngine On

RewriteCond %{HTTP_HOST}^olddomain.com$

[OR]

RewriteCond %{HTTP_HOST}^www.olddomain.com$

RewriteRule (.*)$ http://www.newdomain.com/$1 [R=301, L]

</IfModule> 
```

## 一些有用的提示

**php.ini 和。htaccess 文件**尝试重命名或注释掉各种行，并寻找新环境中可能不存在的硬编码路径。

**缓存插件**
有些缓存插件会修改你的。htaccess 或创建文件的绝对路径可能不存在于您的新主机设置。

Google reCaptcha 插件
如果你安装了任何 Google reCaptcha 插件，你应该记得为你的新域名获取一个新的 API 密钥[，这样插件就不会觉得显示 reCaptcha 很困难。](https://google.com/recaptcha/admin#list)

另外，如果你安装了 Jetpack 插件，你应该记得重新连接你的 WordPress 网站到 WordPress，因为你已经转移到一个新的域名。

**Genesis Club Lite 显示模块**
如果您安装了 [Genesis Club Lite](https://wordpress.org/plugins/genesis-club-lite) 插件，并且显示模块被激活，您应该记得将您的 URL 的域名部分从旧域名更改为新域名。这是在**Genesis Club Lite**->-**Display**->-**Logo**实现更改并保存更改。

## 结论

在本教程中，我讲述了如何将一个 WordPress 站点移动到一个新的主机上，以及如果需要的话，如何更改与之相关的域名。如果你在你的内容中使用了绝对网址，你也需要更新它们。许多插件也需要更新，但希望这能给你指明正确的方向。

在你开始任何网站迁移之前，我强烈推荐你阅读一下 WordPress Codex 来了解更多关于各种过程的细节。

如果你有任何问题或建议，请在下面的评论中告诉我们，这是一个广泛的话题，所以我很乐意听到你对自己的方法的反馈和想法。

## 分享这篇文章