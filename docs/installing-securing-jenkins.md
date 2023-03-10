# 安装和固定 Jenkins

> 原文：<https://www.sitepoint.com/installing-securing-jenkins/>

今年早些时候，我写了一篇关于 PHP-CI 的[文章](https://www.sitepoint.com/continuous-integration-php-ci/)，您可以将它用作 PHP 项目的持续集成工具。在这篇文章中，我指出我仍然最喜欢将[詹金斯](http://jenkins-ci.org/)作为 CI 工具。是时候深入 Jenkins，看看我们如何为我们的 PHP 项目设置这个了。

## 詹金斯简介

由于 Jenkins 拥有丰富的插件系统，所以它可以提供的东西非常多。基本上，Jenkins 只是一个工具，它将各种不同的工具和插件连接在一起，为您创建一份报告。例如，它可以运行 PHPUnit 并以图表的形式显示结果。它可以通过运行`php -l`来检查 PHP 代码的错误。然而，您也可以让 Jenkins 构建一个项目并输出一个 zip 文件，您可以用它来部署您的应用程序。这样做的好处是，您可以获得一个完整的 zip 文件来升级您的生产应用程序，而不必在您的生产服务器上运行 Composer 或 NPM 等工具。

Jenkins 可用于许多不同的应用。您可以将它用于用 PHP 编写的 web 应用程序，也可以用于用 Java 或任何其他语言编写的软件和移动应用程序。这使得 Jenkins 成为一个非常通用的工具，对于处理许多不同项目的公司来说非常有趣。

因为 Jenkins 有如此多的选项和可能性，人们开始使用它看起来势不可挡。在这一系列文章中，我们将慢慢了解 Jenkins。我们将从安装和设置开始。之后，我们将继续分析一个项目。最后，我们将仔细看看我们可以用来检查产品质量的许多其他插件。

我们将主要关注 PHP 代码的质量，但最后我们也将简要分析一下与 PHP 密切配合的 HTML、CSS 和 JavaScript。

## 安装 Jenkins

你可以在任何流行的操作系统上安装 Jenkins。在这篇文章中，我们将把 Jenkins 安装在 LTS 的 Ubuntu Server 14.04 上。如果您使用的是不同的操作系统，您可以查看此处的[以了解每个操作系统的安装说明。要在 Ubuntu 上安装 Jenkins，最简单的方法就是运行这个命令。](http://jenkins-ci.org/)

```
sudo apt-get install jenkins
```

然而，你现在安装的是位于 Ubuntu 库的 Jenkins。Jenkins 自己建议直接从 Jenkins 库安装。首先，我们为存储库添加系统的密钥。

```
wget -q -O - http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key | sudo apt-key add -
```

现在我们运行以下命令将 Jenkins 添加到`sources.list`文件中。

```
deb http://pkg.jenkins-ci.org/debian binary/
```

现在我们准备安装詹金斯。

```
sudo apt-get update
sudo apt-get install jenkins
```

完成后，Jenkins 已成功安装在您的服务器上。

现在，您可以通过浏览器将端口 8080 添加到 URL 的末尾来联系 Jenkins。假设我的服务器名为`ci.myserver.com`，你可以通过`ci.myserver.com:8080`联系到詹金斯。

![Jenkins](img/6da124348b12858be33c061c2d3adedd.png)。

## 保护詹金斯

你应该注意到的第一件事是詹金斯是完全开放的。任何人现在去这个网址都可以做各种不同的任务，所以我们要做的第一件事就是保护它。我们从点击侧边栏菜单中的`Manage Jenkins`开始。在那里我们会收到 Jenkins 的通知，建议保护 CI 服务器。

![Jenkins](img/21bd7703eceaea74a8e4ed0e887b8c8c.png)

点击`Setup Security`按钮继续。在下一页，选中`enable security`复选框。正确遵循以下步骤至关重要，因为很容易将自己锁在 Jenkins 之外。如果发生这种情况，你可以按照[这些步骤](https://wiki.jenkins-ci.org/display/JENKINS/Disable+security)再次获得访问权。

您需要做的第一件事是设置您的安全领域。最简单的方法是通过勾选`Jenkins' own user database`前面的复选框，让 Jenkins 拥有自己的用户数据库。确保`allow users to sign up`选项也被启用。现在点击`save`保存您的配置。

现在，您应该可以在页面的右上角看到一个注册链接。点击此链接并填写表格进行注册。完成后，使用您新创建的帐户登录。

点击`Manage Jenkins`，然后点击`Configure Global Security`，返回安全设置。你现在应该取消选择`allow users to sign up`复选框，以确保没有你的许可不能创建新账户。

接下来，我们将设置授权。对你来说最好的选择不是`matrix based security`就是`Project-based Matrix Authorization Strategy`。这允许您为每个用户设置他们可以执行和不可以执行的操作。如果您选择后一个选项，您甚至可以对每个项目进行设置。现在，我打算选择`matrix based security`。

现在显示一个具有权限的表。您将只看到一个表格行，您可以在其中定义匿名用户可以做什么。但是，我们希望为自己的用户帐户设置权限。因此，请在下面的框中填写您的用户名，然后点击`Add`。为了确保您可以访问所有内容，您可以检查所有内容。

最后就是这个样子。

![Jenkins](img/edcbd61462ef4678207456ead7e01f9b.png)

注意:小心大写字母。peter 和 Peter 是两个不同的用户帐户，所以请确保您在两种情况下都填写了正确的用户名，否则您将被系统锁定。

保存时，您将自动注销。请注意，除了登录表单之外，您再也看不到任何东西。登录后，您应该可以像以前一样访问所有内容。如果你得到一个许可错误，你犯了一个错误，你要么改变设置，要么你把自己锁在外面。在后一种情况下，请参见上面的链接。

## 准备詹金斯

到目前为止，我们一直忙于安装和保护詹金斯。现在是时候配置 Jenkins 了，这样我们就可以开始构建和分析我们的 PHP 项目了。塞巴斯蒂安·博格曼创建了一个优秀的网站，向你展示如何设置 PHP。我们将按照文档中的步骤进行操作，但顺序略有不同。

### 安装插件

首先，我们需要安装几个插件到詹金斯。插件是 Jenkins 的小扩展，它可以为你执行任务，比如分析报告并将其转换为图表。我们需要下面的插件列表来开始。

*   检查样式
*   克洛弗普
*   crap4j
*   干燥的
*   htmlpublisher
*   jdepend
*   情节
*   pmd
*   违反
*   xunit

我们将通过界面安装这些插件。如果你觉得命令行更有经验或者更舒服，你可以查看这个文档看看如何通过命令行安装它们。另外，这个页面会给你一个简短的解释，关于每个插件会做什么。

在 Jenkins 中，转到`Manage Jenkins`，然后转到`Manage Plugins`。单击名为`available`的选项卡。在这里你会看到所有可用插件的完整列表。通过右上角的过滤器，你可以快速搜索每个插件。确保你安装了上面提到的插件的完整列表。

![Jenkins](img/2e8643fa9c04e507102b6f8c6f4e924c.png)

选中`Restart Jenkins when installation is complete and no jobs are running`前面的复选框，在一切完成后重启 Jenkins。重启后，所有需要的插件都已正确安装。

### 创建模板

当 Jenkins 建立起来后，我们通常从创建一个项目开始，有时称为作业。一个项目通常是你得到的一个 PHP 项目。在一个项目中，您将有几个构件。您可以配置 Jenkins 在特定时刻分析(构建)您的代码。例如，这可以是预定的时间间隔，也可以是来自贡献者的请求。因此，一个项目包含几个构建，每个构建都是对代码的单独分析。感谢我们安装的插件，你将能够在漂亮的图表中看到这些构建的结果。在这些图表中，您可以快速发现某个构建是否提高或降低了项目的质量。根据我们所有的工具，我们还可以看到我们需要改进代码的地方。

我们现在可以创建一个新项目。然而，我们还需要配置这个项目。例如，我们可以配置每次 PHPUnit 运行时，应该创建一个代码覆盖页面，这样我们就可以看到哪些代码被测试了，哪些代码没有被测试。对于一个新项目，我们需要配置很多测试，这可能会很耗时。幸运的是，塞巴斯蒂安·博格曼也创建了一个我们将要使用的[模板](http://jenkins-php.org/integration.html)。

我们需要打开命令行来安装这个模板。最简单的方法是下载 Jenkins 提供的 CLI 工具。然而，问题是，我们较早地保护了我们的 Jenkins 安装。这将意味着我们必须开放安全回匿名或连接公共/私人密钥到我们的帐户。

在这种情况下，我们将采用另一种方法来安装模板。首先，在命令行上以用户 Jenkins 的身份登录，并转到主目录。

```
sudo su jenkins
cd ~
```

在主目录中，进入`jobs`目录并创建一个名为`php-template`的新目录。

```
cd jobs
mkdir php-template
```

现在我们将进入这个目录并添加配置文件。

```
cd php-template wget https://raw.github.com/sebastianbergmann/php-jenkins-template/master/config.xml
```

现在，我们需要詹金斯重新加载它的配置。你可以回到网页界面，点击`Manage Jenkins`然后点击`Reload Configuration from Disk`来完成。Jenkins 现在将重新加载其配置。完成后，您应该在主概览页面上看到 php-template 项目。

![Jenkins](img/75fb5117ad6151a91f66e9ca6bc0776f.png)

詹金斯现在准备好了。我们可以基于该模板创建新项目。

## 结论

在这篇文章中，我们安装、保护并准备了 Jenkins 来开始分析我们的 PHP 项目。在下一部分中，我们将添加我们的项目，准备它，当然还有分析它。

## 分享这篇文章