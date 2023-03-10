# 如何用 Let's Encrypt 把 WooCommerce 迁移到阿里云 ECS

> 原文：<https://www.sitepoint.com/how-to-migrate-woocommerce-to-alibaba-cloud-ecs-with-lets-encrypt/>

*本文是与[阿里云](https://www.alibabacloud.com/)合作创作的。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

阿里云是一个供应商，最近获得了很大的知名度。它提供的主要产品之一是 [ECS(弹性计算服务)](https://int.alibabacloud.com/m/1000000356/)或者简单地说，一种灵活的基于云的 VPS。那些熟悉云计算产品市场的人会注意到与亚马逊弹性云的相似之处——他们是对的。在测试阿里云的 ECS 时，我们可以说它是亚马逊和云计算领域其他供应商的强大竞争对手。

事实上，如果我们根据阿里云提供的产品的多样性来判断，我们或许可以说它们已经存在了。

在本文中，我们承担了将一个成熟的生产电子商务网站迁移到阿里巴巴的 ECS 实例的实际任务(我们使用了一个在另一个域上运行的真实网站，并将其迁移到一个测试域)。

我们使用的安装是一个 WooCommerce 网站，拥有数百个产品、用户和大量磁盘空间。

## 弹性计算服务

一旦我们[在阿里云](https://account.alibabacloud.com/register/intl_register.htm)注册，我们将获得价值 300 美元的信用额度来试用其产品(一旦我们确认了支付方式，如信用卡或 PayPal 账户)。这些资金可以用于部署和测试 ECS 实例。

登录后，我们导航到我们的管理控制台，在左侧菜单中选择 *[弹性计算服务](https://int.alibabacloud.com/m/1000000356/)* ，然后选择*实例*。一旦我们点击这个链接，我们会在右边看到一个按钮*创建实例*。

![](img/f2e7c511a51a395fe9b159501e245630.png)

然后，我们可以选择实例的选项——*计费方式*、*地区*——表示我们服务器的位置——以及*实例类型*。我们可以从几个架构(我们选择了 x86 架构)和类别中进行选择，例如*通用*、*计算优化*、*内存优化*、*高时钟速度*和其他类型。

然后，我们可以根据内存大小、虚拟内核数量、时钟速度等，从提供的服务器规模中选择一个。

在这个下面，我们可以选择一个提供的图像——有许多操作系统可供选择，包括阿里巴巴自己的阿里云 Linux、CentOS、Ubuntu、Windows Server 等。在市场映像中，我们可以选择大量预配置的应用程序映像。

![](img/dd923b93d275b54bfca3bb232fc46cf0.png)

在这里，我们选择 Plesk 免费图像，因为它为我们提供了许多方便的方法来管理我们的服务器——域管理、数据库管理和 WordPress 应用程序管理——所有这些都在一个 web 界面中。

![](img/380b5fd0765f8975a4d29256d102bae9.png)

如果我们有单独获得的 Plesk 许可证，或者我们想支付更多费用，我们可以获得 Plesk 的高级版本，它具有更多功能，使专业服务器管理更加简单。

Plesk 实例的另一个便利之处是它们已经通过配置的防火墙规则得到了加强和保护。它还预装了 phpMyAdmin。这很好，因为考虑到我们使用的是 LEMP 服务器堆栈，安装和配置 phpMyAdmin 可能需要一些工作。

然后，我们选择存储–SSD 磁盘是推荐选项，容量高达 500GB。

然后，我们可以选择网络选项，如果我们使用默认的预选选项，可能就没问题了。我们还可以配置安全组——如果我们有多个服务器实例，这是一件很有用的事情。

现在我们来看 SSH 访问配置。在这里，我们可以为无密码访问配置我们的 SSH 密钥，这是普遍推荐的。但是，由于我们正在进行迁移，我们将配置密码访问，因此我们不必从迁移源服务器复制公共 SSH 密钥。

![](img/72f5a31d9c8ef47925d7e8a3283c2ebc.png)

我们应该记下我们在这里创建的密码。如果我们忘记了密码，我们可以在 ECS 控制台中重置它。

完成订单后，我们可以返回 web 控制台，等待一两分钟，直到实例创建完毕。一旦实例运行，我们就可以通过浏览器中的 VNC 会话连接到系统外壳。一旦我们这样做了，我们的实例就会在 shell 中给出进一步的指令:

![](img/00350cbe83cd931d6a73d013ffbe2dae.png)

我们需要创建一个安全规则，它将打开我们需要的端口，并使我们能够访问我们的实例。完成后，我们可以通过 SSH shell 连接到我们的实例(或继续我们的 VNC 会话)，并生成我们的 Plesk admin 帐户:

![](img/cff36639e3f8d6271440241ead825c41.png)

我们将复制第二个 URL，这将允许我们通过 web 访问 Plesk admin。

一旦我们在浏览器中访问此 URL，我们将创建我们的帐户:

![](img/8d0af9859c95556d6204dcd27e0e881d.png)

这里我们假设我们有一个域，并且我们已经将我们的域——或者子域——指向我们实例的公共 IP 地址。否则，一旦我们登录 Plesk 管理控制台，就会看到我们的 IP 地址。一旦我们将域名指向这个 IP，我们就可以创建一个*网络空间*——基本上是我们网站的虚拟主机。否则，我们需要为此手动配置 nginx，但是有了 Plesk，我们可以方便地以简单的方式做到这一点。

我们还可以使用加密证书来保护域。同样，我们将手动完成这项工作，安装 certbot 并为我们的虚拟主机/网站创建一个证书。在这里，它只是我们需要选中的一个复选框。

![](img/00b7caa7756a787e5084a17bfbc9c196.png)

## Plesk 的便利和简单

在本指南中，我们需要保持现有 WordPress 和 WooCommerce 安装的完整性，为了避免冲突，我们将整个 WordPress 目录克隆到我们创建的网站/域名根目录中。但是对于新的安装，Plesk 提供了 WordPress 的一键部署，以及其他几十个应用程序，如 OpenCart、Magento、Prestashop、Drupal、Typo3 等。

使用 Plesk 界面的便利之处在于，我们将能够从一个统一的 web 界面管理我们的整个堆栈，从管理我们的服务器配置、编辑我们的`php.ini`文件，到安装和更新我们的 WordPress 和插件。

如果我们像以前一样通过 SSH / rsync 迁移我们的网站，我们需要点击`scan`按钮让 Plesk 检测我们的网站。

![](img/8854b87c43486f343e3ad30becb43a25.png)

从 Plesk 界面，我们还可以打开 nginx 缓存，并备份和恢复我们的网站。Plesk 的付费用户有更多的选择(我们可以很容易地升级我们的许可证)，这将使网站管理变得容易得多。

但是为了达到这个阶段，我们将首先克隆我们的网站到新的实例。

## 移民

为了迁移我们现有的电子商务商店，我们将通过 SSH 登录到我们现有的服务器，并创建一个数据库转储:

```
mysqldump -u ourusername -p ourdatabasename > ourdatabase.file.sql 
```

上面的查询(显然，带有我们自己特定的数据库名称和所需的文件名)将为我们留下我们的网站数据库文件，我们将使用 scp 将该文件传输到我们的新服务器实例:

```
scp ./ourdatabase.file.sql root@ourdomainname:/var/www 
```

这里，用我们的域或新的服务器 IP 代替*我们的域名*，我们将输入新的服务器密码并传输数据库。

我们将把整个网站文件夹(可能在旧服务器上的`/var/www/`下——或者在其中一个子文件夹中)转移到新服务器上:

```
rsync -avzh /var/www/our-website-folder/ root@ourdomainname:/var/www/vhosts/ourdomainname/httpdocs 
```

我们将把上面的*我们的域名*与我们在 Plesk interface 中分配给我们网站的域名互换。路径`/var/www/our-website-folder/`假设我们的网站文件夹与*有些名字*在/var/www 下，在我们的旧服务器上，Plesk 通常会把我们的新网站根目录放入`/var/www/vhosts/ourdomainname/httpdocs`。

使用 rsync 需要注意的一点是源文件夹路径后面的斜杠——这里是`/var/www/our-website-folder/`。如果我们省略斜线，rsync 会将整个文件夹复制到我们的新文件夹中。如果我们在末尾使用斜杠，它将复制我们的源文件夹*的内容*。这是需要注意的一件事。

如果复制顺利，我们现在就有了自己的文件系统。我们需要创建我们的数据库，并从我们转移到`/var/www`的数据库转储文件中导入内容。

![](img/a072f9991f92a6af7f3442844b106786.png)

在我们的数据库部分，我们可以很容易地添加数据库，导出和导入它们。在我们的迁移案例中，我们将通过 shell 导入我们的转储，因为我们的生产网站有一个高达几百兆字节的数据库。对于较小的数据库，我们可以通过 web 界面轻松导入。

![](img/799305037c71cf13fad09ca74b3799d8.png)

完成后，我们将通过 shell 导入我们的文件:

```
mysql -u test1 -p test1 < /var/www/ourdatabase.file.sql 
```

现在 mysql 会提示我们输入我们创建的密码，输入密码后，它会将我们的数据库文件导入到我们的新数据库中。

## 配置迁移后的网站

我们现在已经迁移了我们的网站根文件夹，我们的虚拟主机和域已经完全配置好，我们的 SSL 证书已经设置好并处于活动状态，我们已经创建了一个数据库并将数据库转储文件导入其中。现在我们需要配置我们的新网站来使用新的数据库。

我们假设我们的新网站在`/var/www/vhosts/ourdomainname/httpdocs`–Plesk 的默认设置中。我们将切换到该文件夹并编辑`wp-config.php`文件，以输入数据库详细信息:

![](img/c328fe324aa0382ad10936c8d78ab075.png)

这就是我们需要做的。我们的网站应该在我们指向新实例的域名下可用。

如果我们将网站迁移到另一个域，而不是旧服务器上的域，或者迁移到一个子域——这是标准做法，直到我们确保迁移后的网站正常运行——我们将需要执行一个搜索-替换程序，为新的 URL/域替换数据库中的旧 URL。

为了使网站正常运行，数据库中的 URL 需要与网站 URL 一致。

现在，我们不能像处理其他文本文件一样直接处理数据库文件，因为 WordPress 数据库中的 URL 是以序列化格式保存的，如果直接处理，我们的数据库将会崩溃。

所以我们需要一个工具。这里最快的工具是[WP-CLI](https://wp-cli.org/)——安装它有点超出了本文的范围，但是[这里的说明](https://wp-cli.org/#installing)相当简单明了，所以你应该不会遇到这个问题。

一旦安装了 WP-CLI，我们可以简单地使用`wp search-replace //olddomain //newdomain --allow-root`，WP CLI 将改变给定 URL 的所有实例(在我们的例子中，是几万个)。

如果我们的新安装遇到任何问题，我们也希望使用`wp cache flush --allow-root`刷新缓存。我们还需要[删除瞬变](https://developer.wordpress.org/cli/commands/transient/)或[刷新重写规则](https://developer.wordpress.org/cli/commands/rewrite/flush/)。

所有这些命令都假设我们使用 shell ( `/var/www/vhosts/ourdomainname/httpdoc`)位于我们的网站根目录中。如果 WP-CLI 抱怨内存问题，我们将想要更改`php.ini`中的`memory_limit`值——WP-CLI 使用 *cli* php.ini 文件，在本例中是在`/etc/php/7.0/cli`中找到的。

## 结论

如果你从零开始创建一个网站，阿里云的 Plesk image 将使事情变得非常简单——我们将能够拉多个在线网站，并且只需几次点击就可以执行高级配置。一个界面有助于快速简单的工作流程。高级许可是为了给我们提供适合网络主机和客户端账户的特性。

这一点，再加上阿里巴巴 ECS 实例的灵活性，使我们能够根据需要扩大或缩小我们的堆栈，使其成为网络专业人士的可靠选择。

## 分享这篇文章