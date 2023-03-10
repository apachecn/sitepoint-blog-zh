# 如何管理和批量安装 WordPress 插件

> 原文：<https://www.sitepoint.com/batch-install-your-wordpress-plugins/>

根据福布斯的数据，全球超过 6000 万的网站由 WordPress 提供支持。这样的数字表明，WordPress 无疑是内容管理系统(CMS)的主要竞争者。

WordPress 的应用已经不仅仅是一个博客平台；经过多年的发展，它已经成为几乎任何网站选择的平台，所有这一切都归功于 WordPress 开发的灵活主干。

该平台最受欢迎的功能之一是使用插件——扩展 WordPress 功能的工具。

如果你一直是 WordPress 的用户，无论是建立你的博客网络还是为客户开发网站，你都应该有一个插件集合，准备安装在每个 WordPress 站点上。

WordPress 缺乏一次安装多个插件的能力。因此，我们别无选择，只能一次安装一个插件。

如果你和我一样对此感到沮丧， [WPCore](http://wpcore.com) 一定会让你兴奋不已。

## WPCore 简介

我在谷歌上的一次调查让我找到了 WPCore，这是一个管理你最喜欢的 WordPress 插件的简单工具。WPCore 也给你批量安装 WordPress 插件的能力。

WPCore 的工作分为四个简单的步骤:

1.  创建收藏并添加你最喜欢的 WordPress 插件。
2.  访问你的任何一个 WordPress 站点并安装 WPCore 插件。
3.  将收集密钥放入 WordPress 的 WPCore 插件设置中。
4.  WPCore 会自动从你的收藏中获取所有插件，并安装到你的网站上。

当我带你走过上面的步骤时，请跟我来。

### 创建收藏

前往[WPcore.com](http://wpcore.com)，注册并登录您的账户。

点击新的收藏导航菜单来创建你的插件收藏。

输入集合名称/标题，设置可见性为公共或私有，点击`Add Plugins`按钮开始整理插件。

![WPCore Plugins](img/3947552d66f831b24c60d9404e96d1be.png)

开始输入插件名称并点击`Add`按钮将它们包含在集合中。

![WPCore Collection](img/055a9c3921a4dec275abfd9749246065.png)

注意右上角显示的收藏`Key`。在 WordPress 中安装插件集合时，这将派上用场。

### 将插件集合安装到 WordPress

在 WPcore 网站上，点击`Download Plugin`链接下载插件。

转到你的 WordPress 仪表盘，上传并激活插件。

在插件设置页面上，添加集合密钥并提交。

![WPCore Key](img/5aa5345a8042fb44b11c4ba3dc4e1500.png)

您收藏的插件将被列为“准备安装”。

![WPCore Ready to Install](img/ae01dc454c9bd00ca9b17196acb63fab.png)

点击安装按钮进入安装页面。

在安装页面上，检查所有插件，从下拉菜单中选择`install`，点击应用按钮启动插件安装。

![WPCore Initiate Installation](img/92f9d85ecfc394d5c098b343a60f9368.png)

观看 WPCore 一次安装所有插件。

![WPCore Installing](img/ca666530020cdeaa6eab3aa7607ba2c6.png)

看看一次点击安装多个插件有多简单，而不必一次安装一个？

## WP 安装描述文件插件

与 WPCore 类似的是 [WP 安装配置文件(WPIP)](http://wordpress.org/plugins/install-profiles/) 插件。

它允许用户定义插件组，称为配置文件。

一旦输入了个人资料，WPIP 将发送呼叫到 WordPress 插件目录，下载插件文件并解压到网站的插件文件夹。

此外，WPIP 以可下载的格式保存配置文件，所以你可以把它上传到你的下一个网站，只需点击一下就可以下载相同的插件。

要使用 WPIP 批量安装多个 WordPress 插件，获取插件库 URL slugs，将它们添加到`Install these plugins`文本区域字段，然后点击提交按钮开始插件安装。

![WPIP](img/ca2f33d6e140411cb1bd3436e6d9f6d3.png)

以获取插件的 URL 段为例:`Simple Feed Customizer`带有存储库 URL-`https://wordpress.org/plugins/simple-feed-customizer/`,`simple-feed-customizer`部分是 URL 段。

您可以使用带或不带连字符的 slug(例如，simple-feed-customizer = simple feed customizer)。

除了 WPcore 和 WPIP，通过 WordPress `Plugin Favorites`和`Must Use Plugins`功能，你可以显著减少一次安装一个插件的时间和痛苦。

## 插件收藏夹

2012 年，插件库增加了`favorite`插件的功能。版本 3.5 引入了从仪表板的 Add New plugins 页面显示和轻松安装用户喜爱的插件的功能。

若要收藏插件:

*   你必须登录到官方的 [WordPress 插件库](http://wordpress.org/plugins/)。
*   查看插件页面时，点击插件下载按钮下方的`Favorite`链接。

![WordPress Plugin Repository](img/07d27c3edefd42d974e073f6b0cc291e.png)

一旦你喜欢一个插件，它将会显示在你的公共档案中，如果适用的话，还会显示你对该插件的评价。

要从你的收藏夹列表中安装插件，在 WordPress 仪表盘中:

前往`Plugins` > `Add New`。

在`Favorites`下，输入你的 WordPress.org 用户名并点击`Get Favorites`按钮。

安装你想在网站上安装的插件。

![WordPress.org Favorite Plugins](img/f17acf1b4b9a44a8fc95b875ef2e2dd6.png)

使用`Favorite`可以节省你搜索常用插件的时间。

## 必须使用插件

必须使用的插件(又名 mu 插件)是安装在 WordPress 内容文件夹中的一个特殊目录中的插件。它们会在安装的所有站点上自动启用。

必须使用的插件不会显示在`wp-admin`插件页面的默认插件列表中——它们出现在一个特殊的必须使用部分——并且不能被禁用，除非从必须使用目录中移除插件文件，该目录默认位于`wp-content/mu-plugins`中。

要安装阿木插件，请按照以下步骤操作:

1.  FTP/SFTP 到你的服务器，并导航到你的 WordPress 安装文件夹。
2.  如果`wp-content`中没有`mu-plugins`文件夹，则创建一个。
3.  将需要制作 *mu 插件*的插件文件夹复制到`wp-content/mu-plugins`中。
4.  不像普通插件，你不需要在它们开始工作前激活它们。

如果你使用 WordPress 多用户，当你使用*必须使用的插件*时，你可以省去在你的网站网络中安装相同插件的压力。

## 包裹

建立一个新的 WordPress 网站的大部分时间都花在了安装插件上。

批量安装它们可以节省大量时间，而不必一次安装一个。

除了本文提到的步骤，当你开始一个新的 WordPress 博客或网站时，你是如何安装你最喜欢的插件的？我们很想在评论中听到它。

## 分享这篇文章