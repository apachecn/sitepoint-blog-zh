# 为开源做贡献——更新 Phalconphp.com

> 原文：<https://www.sitepoint.com/contributing-open-source-updating-phalconphp-com/>

前阵子，我写了一篇关于在 Windows Azure 上安装 [Phalcon 的教程。然而，Phalconphp.com 的主页在“可以安装 Phalcon”部分仍然没有提到 Azure 是一个受支持的环境:](https://www.sitepoint.com/phalcon-windows-azure/)

![](img/59a5df77a05113f62842bac9bb312a53.png)

在本教程中，我们将把 Phalconphp.com 网站克隆到我们的本地 PuPHPet 虚拟机，更新它以包含有关受支持环境的新信息，并向项目所有者提交一个 pull 请求。这篇文章是我的 [Dillinger.io Pull Request 教程](https://www.sitepoint.com/contributing-open-source-dillinger-case-study/)的精神继承者，所以请务必在继续之前查看它，因为这篇教程将更多地关注 PuPHPet 工作流，而不是实际的 Pull Request 工作流。

请注意，虽然我将使用 puhppet 来准备一个启动网站的环境，但您决不会受到此限制的约束——如果您更喜欢将它克隆到您已经设置好的本地环境中，请随意这样做，尽管我个人认为如果您像我一样做，pupppet 方式会更快。

## 初始配置

如果你还不熟悉 PuPHPet，请参见[马修·塞特的教程](https://www.sitepoint.com/build-virtual-machines-easily-puphpet/)。让我们访问 PuPHPet 并设置我们的虚拟机。为了让事情变得简单，我们需要记住的一件事是，Phalcon 网站的文档根在哪里，所以我们可以在我们的 VM 中绑定它。如果我们检查[他们的 Github](https://github.com/phalcon/website) ，我们可以看到他们在主“网站”回购中使用标准的`public`文件夹，这也是我们将要做的。

我选择了下面的选项，所以请随意跟随我的脚步或者在这里下载我的最终版本(我没有提到的都保留默认值):

*   环境:本地
*   服务器:Apache
*   操作系统:Ubuntu Precise 12.04 x64–Virtualbox 4.3
*   名称:升级-phalconphp
*   已安装的软件包:vim
*   时区:欧洲/萨格勒布(如果你想，你可以自己离开)
*   安装 XDebug:是
*   MySQL 选项:db1、user1、pass1

在 Apache 虚拟主机下，我将值设置如下:

![](img/1f36b0db9c59f2d42068f8f7f52ce17a.png)

创建并下载档案，把它放在你的流浪者盒子的位置并解压。

接下来，让我们将这篇文章中的 Phalcon provision 脚本添加到 shell 子文件夹中，并将下面一行添加到所有的 provision 块之下:

```
config.vm.provision :shell, :path => "shell/install_phalcon.sh"
```

这将确保 Phalcon 安装在虚拟机启动。

接下来，我们需要确保网站在虚拟机启动时准备好运行。我们首先需要分叉网站存储库。去[https://github.com/phalcon/website](https://github.com/phalcon/website)叉它。

将您的 fork 克隆到盒子的文件夹根目录中:

```
git clone http://github.com/YOUR_USERNAME/website
```

克隆后，我的文件夹结构如下所示:

![](img/f05977f73ffc1e49cce860d3c81b2fe9.png)

如果您使用了上面的 PuPHPet 过程，运行`vagrant up`并等待 VM 引导完成。如果您像我一样选择 Apache 作为服务器，在 VM 启动后，您可能必须禁用默认的 vhost。您可以通过使用`vagrant ssh`切换到框中并执行:

```
sudo a2dissite 15-default.conf
sudo service apache2 reload
```

这将删除缺省配置，只留下我们在“虚拟主机”下设置的配置。由于 Phalcon 网站不使用数据库(大部分材料是静态的)，我们可以立即在本地运行它，无需任何进一步的修改:

![](img/ee6c973c2cfcba69db855fc6ef0c2223.png)

可能会有一些关于 PHP 无法写入源代码文件夹中的缓存目录的警告，但我们现在不会处理它，因为它对于这个特殊的快速修复并不重要。

将您的回购连接到原来的上游，然后创建一个名为`adding-azure`的新分支并切换到它，通过执行:

```
git remote add --track master upstream http://github.com/phalcon/website
git branch adding-azure
git checkout adding-azure
```

理论上，我们可以将默认的 vhost disable 命令和 git 过程添加到最终的 provision shell 文件中。我会让你自己做实验。

## 更新网站

终于可以开始工作了。

相关文件将是:

*   `app/views/index/index.volt`
*   `app/views/pages/hosting.volt`

我们还需要一个新的图像文件作为 Windows Azure 的标志。我做了一个你可以下载的:

![](img/858b7fa3d2722ad0330e831d34220e75.png)

将此图像添加到`publimg/hosting`。鉴于该文件夹中的所有主机提供商图像都有其对应的颜色，即使从未使用过彩色版本，也让我们尊重这一传统，添加我们自己的图像。

![](img/1269b860dc6f64b85df29a2478931ae7.png)

接下来，我们需要编辑主页，将图像包含到图像集链接中。打开`index.volt`并将其添加到宿主图像的末尾，就像这样:

```
<img src="{{ cdn_url }}images/hosting/azure-gray.jpg" alt="Windows Azure" />
```

![](img/2c2cff7ed97c2bb64e6207e0e1aeaeac.png)

现在输入`hosting.volt`，复制最后一个`<tr>`块，提到 RackSpace 的那个。更改此内容以反映 Azure，如下所示:

```
 <tr>
            <td align="center">
                <img src='{{ cdn_url ~ 'images/hosting/azure-gray.jpg' }}' title='Windows Azure' alt='Windows Azure' />
            </td>
            <td>
                <h4><a target="_blank" href='http://www.windowsazure.com'>Windows Azure</a></h4>
                <p>{{ tr('hosting_azure_1', 'http://www.windowsazure.com') }}</p>
                <p>{{ tr('hosting_azure_2', 'https://www.sitepoint.com/phalcon-windows-azure/') }}</p>
            </td>
        </tr>
```

注意我们如何使用一些翻译占位符，如`hosting_azure_1`。这是因为 Phalcon 使用语言文件作为多语言网站，网站上呈现的内容的实际文本在其他地方定义。

这些语言文件在`app/var/languages`里。它们是 PHP 文件中定义的简单的 key= >值数组，很容易编辑。

打开英文的`en.php`文件，找到主机描述所在的部分(应该是第 198 行)。在该部分的底部，添加我们自己的:

```
'hosting_azure_1'    => "The <a href=':1:'>Windows Azure cloud</a> provides you with highly scalable and manageable Windows hosting for your web apps. Use Phalcon to reduce your instance costs.",
'hosting_azure_2'    => "To install Phalcon, follow the step by step procedure in <a href=':1:'>this SitePoint article</a>.",
```

最终结果应该是这样的:

![](img/0386700bfce7df40fa9a82ccb3fefdb2.png)

当您重新加载托管页面时:

![](img/c9ff75d73fe85f27097ed20a40084f08.png)

如果你觉得特别慷慨，在其他语言文件中添加一些其他的翻译——我相信 Phalcon 团队会很感激的。

## 发送拉取请求

一旦我们用`git add`把所有东西都添加到 Git 中，并在主页和托管页面上提交一条像样的消息，比如“把 Azure 添加到托管提供商”。链接到描述程序的文章。”，我们可以在线推送回购。

推送后，我们提交拉取请求。

![](img/e0e009beff74d72806b443fdb72df600.png)

## 结论

现在剩下要做的就是等着看开发者是否会接受它。不管公关是否成功，这篇文章已经告诉你为一个开源项目做贡献是多么简单。

如果你想让你的贡献增加，或者只是需要缓慢但稳定地获得一些经验，那就四处寻找一些需要帮助和协助的项目。就像我们在这篇文章中做的那样，从小处着手——对 HTML 和文本进行修改，然后当你发现缺少一些功能时，慢慢地移动到适当的功能上。

如果你有自己的贡献故事想要分享，一个具体的要求，或者只是关于这篇文章的一般反馈，请不要犹豫，在下面的评论中留下它！

## 分享这篇文章