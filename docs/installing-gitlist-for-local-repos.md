# 正在为本地仓库安装 GitList

> 原文：<https://www.sitepoint.com/installing-gitlist-for-local-repos/>

GitHub 是代码协作和存储库管理的一个很好的解决方案，但是一些个人和公司不觉得在云中托管他们的代码是安全的，而是更喜欢维护一个内部网。使您的存储库在本地内部网中可用是相当容易的，但是拥有一个好的界面来与这些存储库交互，使团队之间的协作更容易，就不是那么简单了。

Git 提供了一个 web 接口来解决这个问题，gitweb，但它不是一个优雅而现代的解决方案。很难看出发生了什么变化，谁做的，什么时候发生的。此外，浏览工作树很麻烦。还有其他可用的解决方案，但有些太难安装，或者对眼睛不好。我最近发现了 GitList，[一个免费的开源 Git 库查看器](http://www.gitlist.org "GitList - An elegant and modern git repository viewer")。它的界面很像 GitHub，但旨在保持一切简单明了。

在本文中，我将引导您完成设置自己的 Git 存储库查看器的过程。不要担心，它不会伤害和它比安装 WordPress 更快！

## 环境

对于本指南，我假设你正在使用基于 Debian 的 Linux 发行版，但是 GitList 可以使用任何东西。基本上，您需要:

*   带有 mod_rewrite 或 Nginx 的 Apache
*   饭桶
*   PHP 5.3 以上

如果您没有安装这些软件，请打开一个终端并运行:

```
sudo apt-get update
sudo apt-get install php5 apache2 git
```

我还会对您的环境做出以下假设:

*   Git 存储库的路径:`/home/bob/code`
*   Apache 文档根目录的路径:`/var/www`
*   Git 可执行文件的路径:`/usr/bin/git`
*   您的 Apache 根 URL: `http://localhost`

Apache 用户将访问您的 Git 存储库，因此您需要应用适当的访问权限:

```
sudo chmod -R 744 /home/bob/code
```

## 安装和配置 GitList

首先，[下载 GitList](https://github.com/klaussilveira/gitlist "klaussilveira/gitlist") 。您可以选择最新的稳定版本或主版本，但请记住，主版本可能会有错误，因为开发人员正在积极地工作。选择包后，将其解压缩到 Apache 文档根目录下的一个名为`gitlist`的文件夹中。

现在来配置 GitList！将`config.ini-example`文件重命名为`config.ini`，用文本编辑器打开它，确保它看起来像这样:

```
[git]
client = '/usr/bin/git' ; Your git executable path
repositories = '/home/bob/code/' ; Path to your repositories

; You can hide repositories from GitList, just copy this for each repository you want to hide
; hidden[] = '/home/bob/code/SecretProject'

[app]
baseurl = 'http://localhost/gitlist' ; Base URL of the application

; If you need to specify custom filetypes for certain extensions, do this here
[filetypes]
; extension = type
; dist = xml

```

只剩下一步了:我们必须在`/var/www/gitlist`中创建一个名为`cache`的文件夹，并赋予它适当的权限。所以:

```
cd /var/www/gitlist
mkdir cache
chmod 777 cache
```

现在去`http://localhost/gitlist`看看吧。

## 救命啊！找不到页面！

GitList 使用 Apache 的 mod_rewrite 来创建漂亮的 URL。因此，如果没有找到该页面，可以通过运行以下命令使 Apache 启用 mod_rewrite:

```
sudo a2enmod rewrite
```

另外，确保 Apache 能够从 GitList 目录中读取`.htaccess`文件。一个`.htaccess`文件用于覆盖 Apache 目录并向其中添加新规则。打开默认的 Apache 网站配置文件(通常位于`/etc/apache2/sites-enabled/000-default`)并查找以下内容:

```
<Directory /var/www/>
	Options Indexes FollowSymLinks MultiViews
	AllowOverride None
	Order allow,deny
	allow from all
</Directory>

```

将`AllowOverride`选项从`None`更改为`All`。保存您的更改并重新启动 Apache。

```
sudo /etc/init.d/apache2 restart
```

## 定制的

GitList 接口是使用 [Twitter 引导](http://twitter.github.com/bootstrap/ "Twitter Bootstrap")构建的，使用[比](http://lesscss.org/ "LESS - The Dynamic Stylesheet language")少。在`web/less`下可用的文件越少。提供了一个 makefile，你所要做的就是根据你的口味定制少的文件，运行`web`目录下的`make`，最终的 CSS 就生成了。当然，你必须安装`lessc`，这可以通过运行 [npm](https://github.com/isaacs/npm "isaacs/npm") : `npm install less`很容易地完成

GitList 由 [Twig](http://twig.sensiolabs.org/ "Twig - The flexible, fast, and secure template engine for PHP") 模板引擎提供支持，所有模板都可以在`views`下获得。为了了解这是怎么回事，我推荐你看一下这篇[教程](http://twig.sensiolabs.org/doc/templates.html "Twig - The flexible, fast, and secure template engine for PHP")。修改`.twig`文件后，不要忘记清理`cache`文件夹的内容！

图片 via[Fotolia](http://us.fotolia.com/?utm_source=sitepoint&utm_medium=website_link&utm=campaign=sitepoint "Royalty Free Stock Photos at Fotolia.com")

## 分享这篇文章