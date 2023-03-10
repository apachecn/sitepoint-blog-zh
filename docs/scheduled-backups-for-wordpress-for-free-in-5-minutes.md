# 快速提示:WordPress 的计划备份在 5 分钟内免费

> 原文：<https://www.sitepoint.com/scheduled-backups-for-wordpress-for-free-in-5-minutes/>

不管你在网上读到多少次，你就是没有时间备份你的 WordPress 网站，尽管你已经投入了很多时间。你不备份的原因有很多——太贵了，安装时间太长，如果你的服务器被黑了，你的备份也可能会丢失，等等。你明白了。但是黑客和服务器硬件问题不会听你抱怨你在你的网站上工作了多长时间，一旦它被黑客攻击，它几乎没有备份就永远消失了。

这就是为什么，你需要现在就采取行动。在 WordPress 中设置计划备份会非常容易。如果我告诉你，有一种方法可以免费备份你的网站，将备份的文件存储在你的服务器之外的安全云环境中，并且可以在 5 分钟内设置好，会怎么样？你很幸运。我向你展示的是 [WordPress 备份到 Dropbox (WPB2D)插件](http://wpb2d.com/)。

*本文假设你已经有了一个 Dropbox 账户。如果你没有，你可以在注册一个免费账户。*

## WordPress 备份到 Dropbox 的优缺点

许多 WordPress 专家可能会指出，还有许多其他更强大的备份应用程序，以及许多强大的付费选项。不过对于大部分用户来说，WPB2D 绰绰有余。WPB2D 插件非常棒，因为它非常容易设置，也非常容易使用。两年前我在我的一个 WordPress 网站上设置了它，从那以后它就再也没有出现过问题。它非常适合一次设置，然后就放在那里。下面是运行中的插件的图像:

![WordPress Backup to Dropbox Plugin](img/6d2c556785da9327a49870c882d966f7.png "WordPress Backup to Dropbox Plugin")

![WordPress Backup to Dropbox plugin](img/66c025d1e155191b56413f45ddb9f37d.png)

然而，WPB2D 确实缺乏更强大的功能，如一键恢复和备份到不同的云服务。如果你在寻找更高级的东西，试试类似于 [Updraft 加](https://wordpress.org/plugins/updraftplus/)的东西，或者看看我们之前的文章[比较最好的 WordPress 备份插件](https://www.sitepoint.com/best-wordpress-backup-plugins/)。

## 配置 WordPress 备份到 Dropbox

1.  前往**插件>在你的 WP 仪表盘中添加新的**。
2.  在搜索栏中输入“**备份到 Dropbox** ”。
3.  这是你应该安装的:
    ![Backup to Dropbox install](img/ef017073ca69b71f5a3e7b2a4cb07754.png)
4.  安装并激活插件。
5.  在你的 WP 仪表盘中进入 **WPB2D >备份设置**。
6.  你需要做的第一件事，就是授权你的 Dropbox 账户使用这个插件。为了安全起见，我建议每个网站创建一个新的 Dropbox 账户(因为它是免费的)。
    ![Backup to Dropbox authorize](img/149bfd3ff0e2ed315974652bfcb59936.png)
    
7.  允许访问插件后，返回备份设置页面，按**继续**。
8.  在那里，您可以访问一些基本设置，以便开始使用。您可以选择存储新备份的频率。请注意，如果您选择了每日备份，并且您在一个小而慢的服务器上，它可能会降低服务器的速度，尤其是当您的站点较大时。
9.  您还可以排除要从备份中排除的文件。例如，如果你有一个目录，里面有几个 GB 大小的电影，你可能会想把它们从备份中排除，因为 Dropbox 只给你 2GB 的免费空间。
10.  按下页面底部的**保存更改**，就这样！您刚刚设置了每日/每周/每月备份到 Dropbox。这大概是你能得到的最安全的了，因为 Dropbox 不会很快走向任何地方。
11.  您可以前往 Dropbox 查看您的备份。插件创建一个名为 **Apps > wpb2d** 的文件夹。

就是这样！如果你有任何问题，请在下面提问。

## 分享这篇文章