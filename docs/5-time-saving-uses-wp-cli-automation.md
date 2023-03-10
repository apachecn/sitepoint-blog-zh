# WP-CLI 自动化的 5 个省时用途

> 原文：<https://www.sitepoint.com/5-time-saving-uses-wp-cli-automation/>

![WP-CLI Automation](img/b4b39c1725362530ad8968f374450196.png)

*本文是与 [SiteGround](https://www.siteground.com/go/article-sp) 合作创作的系列文章的一部分。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

## 什么是 WP-CLI？

WP-CLI 是一个允许你直接从命令行对 WordPress 安装执行操作的工具。WP-CLI 自动化是通过使用 WP-CLI 驱动的脚本来自动化重复的手动任务。当你部署或管理一个 WordPress 安装时，这可能看起来是不必要的、笨拙的或太困难的任务，但是当你管理许多，或不断地创建类似的新站点进行测试时，它成为你的开发工具包中一个非常有价值的工具。

### 关于 WP-CLI

使用 WP-CLI，您可以通过管理面板执行任何操作，但是要从命令行执行。你可以安装或更新核心 WordPress 文件、插件或主题。您可以激活和停用插件或重新生成图像缩略图。您还可以执行数据库操作，如导出和导入数据库，或者查找和替换数据库信息，如迁移期间更改的 URL。

一些插件也支持 WP-CLI——包括许多更流行的插件。这意味着你可以设置自动脚本来安装和设置 WordPress，安装这些插件，然后使用他们自己定制的 WP-CLI 命令来设置插件！

### WP-CLI 自动化

当设置或管理多个 WordPress 安装时，WP-CLI 自动化不仅仅是简单的命令行使用。一次更新或备份多个站点，或者用单个命令重复创建复杂的样板文件安装的能力非常有用，可以为这些站点的维护人员节省大量时间。

> 如果你还没有安装 WP-CLI，看一下[安装文档](http://wp-cli.org/#installing)，让 WP-CLI 启动并运行。

## WP-CLI 自动化的五个用例

### 安装 WordPress

一旦安装了 WordPress，这个示例脚本可以下载、配置和安装 WordPress 核心，删除启动插件，添加和激活一个指定的主题(保存在 example-theme.zip 中)，并安装和激活一个您希望在新安装中使用的插件列表。

示例:

```
#!/usr/bin/env bash

#plugins to install and activate (slugs)
WPPLUGINS=( test-plugin1 test-plugin2 test-plugin3 )

echo "Starting WordPress Installation Script"

# Site Name Input
echo "Site Name: "
read -e sitename

# Site URL Input
echo "Site URL: "
read -e siteurl

# Download WP and configure it
wp core download
wp core config --dbname=$dbname --dbuser=root --dbpass=root
wp db create
wp core install --url=$siteurl --title="$sitename" --admin_user="admin" --admin_password="examplePassword123" --admin_email="test@example.com"

# Remove default plugins, install plugins, install Base Theme
wp plugin delete --all
wp theme install example-theme.zip --activate
wp plugin install ${WPPLUGINS[@]} --activate

echo "WordPress installation complete!" 
```

但是，您可以通过向用户询问相对路径信息来进一步自动化这个过程，这样您就不必在安装目录中运行它，还可以询问数据库名称和密码等等。你也可以设置一个主机环境，在一个服务器上处理多个 WordPress 安装，并且一次设置和安装多个站点。以您需要的方式定制脚本，以便它可以最大限度地对您自己的项目有效，并且您不必不断地重写它——使它高效！

### 备份 WordPress

备份你的 WordPress 安装是必须的，但是有很多方法可以做到。你可以用一些 [WordPress 备份插件](https://www.sitepoint.com/5-wordpress-plugins-backups-migrations/)轻松备份，但是你也可以直接从命令行备份。

首先，您想要从网站的目录运行(无论是在命令行还是通过脚本)`wp db export example.com_20170501T1420`，最后一个参数是您喜欢的文件名。当然，如果将这个过程完全自动化，在文件名中添加时间戳会很方便。

一旦完成，你的网站的根目录将包含一个`.sql`文件，它是网站数据库导出时的备份。然后你可以运行一个简单的`tar -vczf example.com_20170501T1420.gz .`(对这个备份存档使用相同的文件名)，它将压缩网站的文件和`.sql`文件。现在，通过命令行、脚本或 SFTP 客户端，您可以在瞬间将该归档文件复制到另一台计算机、驱动器或云存储中，即文件和数据库的备份！

### WordPress 核心更新

要在当前目录中更新站点的 WordPress 核心文件，运行`wp core update`命令。当您设置一个脚本来遍历当前服务器上的安装列表，并通过输入一个命令来依次更新每一个安装时，这个命令真的很棒。

示例:

```
#!/usr/bin/env bash

# Assumes site directories are under /var/www/siteurl

WPSITES=( example.com example2.com example3.com )

WPPATH=/var/www/

echo "Starting WordPress Core Updates"

for i in "${WPSITES[@]}"
do
  : 
  wp core update --path:$WPPATH$i
  echo "Updates for $i Completed!"
done

echo "WordPress Core Updates Complete!" 
```

### WordPress 插件和主题更新

与核心更新类似，遍历你的站点列表，运行`wp plugin update -all`来更新每个站点上安装的所有插件，或者运行`wp theme update --all`来更新主题。

示例:

```
#!/usr/bin/env bash

# Assumes site directories are under /var/www/siteurl

WPSITES=( example.com example2.com example3.com )

WPPATH=/var/www/

echo "Starting WordPress Plugin and Theme Updates"

for i in "${WPSITES[@]}"
do
  : 
  wp plugin update --all --path:$WPPATH$i
  wp theme update --all --path:$WPPATH$i
  echo "Updates for $i Completed!"
done

echo "WordPress Plugin and Theme Update Complete!" 
```

> 如果你想更新核心 WordPress 以及插件和主题，你也可以把它们合并成一个更新脚本。

### WordPress 迁移

作为迁移流程的一部分，当在服务器之间、向另一个域或在开发和生产或登台环境之间迁移站点时，您也可以使用 WP-CLI 处理所有数据库问题。

使用以下命令从旧的托管服务器导出数据库(从网站根目录运行):

```
wp db export example.com_20170501T1420
```

使用以下命令将其导入新的托管服务器(从网站根目录运行):

```
wp db import example.com_20170501T1420
```

然后用新信息(从网站根目录运行)替换旧信息(如 URL ),具体如下:

```
wp search-replace oldurl.com newurl.com
```

`search-replace`命令用`newurl.com`替换`oldurl.com`的任何实例。

通过扩展您可能用于备份的相同脚本，这个过程也可以自动化。您可以轻松地创建一个导出脚本，然后创建一个添加了搜索和替换字段输入的导入脚本，如果数据库凭证发生了变化，甚至可以用新的数据库凭证选项来扩展它。

## 结论

使用 WP-CLI 可以实现自动化的任务数量惊人。你可以定制一个安装脚本来下载 WordPress 核心，创建你的配置和你的数据库，安装 WordPress，去掉任何臃肿，添加默认插件和主题并激活它们，等等。您还可以使用它来运行备份、更新、迁移等等。

当你想使用 WP-CLI 时，选择一个好的主机是很重要的。许多主机不支持 WP-CLI 的使用，所以如果您打算利用 WP-CLI 自动化，找到一个支持的主机是至关重要的。SiteGround 是积极支持和投资 WP-CLI 项目维护的主办单位之一。这是托管你的 WordPress 网站的一个很好的选择，尤其是当你需要使用 WP-CLI 的时候——它在所有的 WordPress 托管计划中都启用了 WP-CLI。SiteGround 也有一个关于在他们的服务器上使用 WP-CLI 的有用的[教程。](https://www.siteground.com/tutorials/wordpress/wp-cli.htm?afcode=97a975da3502771c04e59cbae092b1dd&campaign=wp-tutorial)

看看它们，开始用 WP-CLI 自动安装和维护 WordPress 吧！

## 分享这篇文章