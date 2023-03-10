# Heroku 突破:脸书云集成

> 原文：<https://www.sitepoint.com/heroku-breaks-through-with-facebook-cloud-integration-2-2/>

平台即服务提供商 Heroku 最近宣布，将在已经为 Ruby、Node.js 和 Clojure 编写的应用程序提供的支持基础上，增加对 Java 的支持。今天上午，Heroku 宣布与脸书建立突破性的合作伙伴关系，有效地允许任何拥有 Heroku 帐户的人成为熟练的、基于云的脸书应用程序开发人员。

为了给 SitePoint 的读者一个好的开始，我们已经获得了许可来出版下面的教程，利用了从今天开始才在脸书可用的功能。

让我把你交给 Heroku 的 Adam Wiggins。

## 在 Heroku 上开始使用您的脸书应用

本指南面向通过脸书云服务集成在 Heroku 上创建应用的脸书开发者。它假设没有 Heroku 以前的知识。Heroku 是一个云应用平台，不是一个老式的 web 主机，所以有些东西你可能会很陌生。

本文将介绍这个过程的每一个部分:创建一个应用程序和一个 Heroku 帐户，设置本地开发工具，并对您的脸书应用程序进行修改。

## 创建应用程序

(如果你已经通过脸书创建了一个 Heroku 应用，你可以跳到下一部分。)

首先进入[脸书开发者](https://developers.facebook.com/apps)，点击右上角的**创建新应用**:

![Create New App button](img/41729530c3233cc97bb8705c8c9bdef5.png)

输入您希望的应用程序名称，勾选条款框，然后点击**继续**:

![Create New App dialog](img/8d8f4d352087864159c002cf0f45c4d6.png)

在随后的对话框中填写验证码，然后点击**提交**。短暂延迟后，你将被带到你的(目前为空白的)脸书应用程序设置页面。在该页面中，在**云服务**下，点击**开始**:

![](img/96e777f2c766a88eab7fcd267753c649.png)开始使用"/ >

Heroku 是该对话框中的唯一选项，因此单击下一个的**:**

![Choose a provider dialog](img/1cc667246128014536ce95ac63918745.png)

如果您在所选的电子邮件地址下已经有一个 Heroku 帐户，新的应用程序将与您现有的帐户相关联。

从下拉列表中选择你最喜欢的编程语言(PHP、Ruby、Python 均可)。输入您希望用于 Heroku 用户帐户的电子邮件地址，然后点击**创建**:

![Heroku dialog](img/1d8fedc92b532f9b83eae81f2f476908.png)

您将看到一个成功对话框，显示您在 Heroku 上运行的脸书应用程序的新 URL:

![Success dialog](img/96e777f2c766a88eab7fcd267753c649.png)

一个模板应用程序，用您选择的语言编写，已经为您复制和部署！点击 URL 或**转到页面**访问您的新应用。

访问您的应用程序的其他用户将需要通过类似的对话框授予权限。用户只需要在第一次访问应用程序时这样做。

因为您是以用户身份访问您的应用程序，所以您需要通过单击**允许**授予应用程序访问您的脸书个人资料的权限:

![Permission dialog](img/2c04021654df04a50b46e4979d83b631.png)

默认情况下，您的新应用程序被配置为与脸书集成的网站。这意味着它将显示为一个独立的页面。如果你想让你的应用程序在脸书浏览器中作为一个画布页面运行，请遵循这个指南

恭喜你！你现在有了自己的脸书应用程序，运行在 Heroku 上:

![Sample app running](img/f945da0bb300346cfb755775a01d5b7c.png)

这个应用程序展示了访问脸书 API 来列出朋友、照片、兴趣等等的例子。一旦你开始编辑代码，你就可以使用这些功能让你的应用做更多有趣的事情。

现在，我们想让您为编辑应用程序做好准备。我们很快就会谈到这一点，但首先，简单地说一下。

## 什么是 Heroku？

(如果你已经熟悉 Heroku，可以直接跳过。)

你的脸书应用需要在某个地方运行。这可以是传统的托管，如您自己的硬件或 VPS 然而，这些选项设置起来既费时又费钱。Heroku 上的应用程序可以即时创建，除非应用程序增长到大量的流量或用户，否则它们不会花你一分钱。

[Heroku](http://www.heroku.com/) 是一个云应用平台。有了 Heroku，你根本不需要考虑服务器。你可以用你选择的编程语言用[编写应用程序，用附加资源如](http://devcenter.heroku.com/articles/cedar#supported_languages_and_frameworks) [SQL](http://devcenter.heroku.com/articles/database) 和 [NoSQL](http://addons.heroku.com/mongohq) 数据库、 [Memcached](http://addons.heroku.com/memcache) 和[许多其他的](http://addons.heroku.com/)来支持它。您使用 Heroku 命令行工具管理您的应用程序，并使用 [Git](http://devcenter.heroku.com/articles/git) 版本控制系统部署代码。[参观 Heroku 建筑。](http://heroku.com/how)

任何种类的 web 应用程序都可以部署到 Heroku 上，你可以在 Heroku [开发中心](http://devcenter.heroku.com/)阅读关于该平台的所有功能。在本指南的其余部分，我们将重点编辑您在上一节中已经创建的脸书应用程序。

## Heroku 帐户和工具设置

(如果你已经有了一个 Heroku 账号，并且设置了本地开发工具，你可以跳到[下一节](#)。)

虽然已经为您创建了一个帐户，并且已经部署了应用程序，但您仍然需要创建一个密码并设置本地工具来开始编辑它。

### 1)选择一个密码

当您通过脸书创建应用程序时，系统会为您创建一个 Heroku 用户帐户。在您用来接收 Heroku 欢迎信息的电子邮件地址下查看您的电子邮件:

![Invitation email](img/18937ecd2781881ab14d77832c32cbb2.png)

按照邮件中的第一个链接，进入您可以为 Heroku 帐户选择密码的页面:

![Password dialog](img/c2a4e6e3aaf6d64f73532e851628c48c.png)

完成后，关闭浏览器窗口并继续下一步。

### 2)安装 Heroku 工具带

要管理和编辑您的应用程序，您需要 Heroku 命令行工具和 Git 版本控制系统，这两个工具都将由 Heroku toolbelt 安装。从下面的列表中下载并安装适用于您的操作系统的工具包:

| 如果你有… | 用…安装 |
| 麦克·OS X | [下载 OS X 包](http://toolbelt.herokuapp.com/osx/download) |
| Windows 操作系统 | [下载 Windows。exe 安装程序](http://toolbelt.herokuapp.com/windows/download) |
| Ubuntu Linux | [`apt-get`知识库](http://toolbelt.herokuapp.com/linux/readme) |
| 其他的 | [Tarball](http://assets.heroku.com/heroku-client/heroku-client.tgz) (给你的`$PATH`添加内容) |

### 3)从命令行登录

安装完成后，您将可以从终端访问`heroku`命令。使用您的 Heroku 帐户的电子邮件地址和密码登录:

```
:::term
$ heroku login
Enter your Heroku credentials.
Email: adam@example.com
Password:
Could not find an existing public key.
Would you like to generate one? [Yn]
Generating new SSH public key.
Uploading ssh public key /Users/adam/.ssh/id_rsa.pub
```

出现提示时按 enter 键，上传您现有的`ssh`密钥或创建一个新的密钥，用于稍后推送代码。

## 编辑您的应用

设置好 Heroku 帐户和本地工具后，您就可以开始更改您的脸书应用程序了。

### 1)获取应用程序的源代码

我们将从使用 Git 获取应用程序源代码的副本开始。为此，你需要知道你的应用程序的名称:Heroku 给了它一个随机生成的俳句名称，所以在 URL 中查找你的应用程序名称。比如你的网址是`http://furious-robot-218.herokuapp.com`，那么你的 app 名字就是`furious-robot-218`。将此粘贴到`git clone`命令中，如下所示:

```
:::term
$ git clone git@heroku.com:furious-robot-218.git -o heroku
Initialized empty Git repository in /Users/adam/facebook-template-php/.git/
remote: Counting objects: 273, done.
remote: Compressing objects: 100% (183/183), done.
remote: Total 273 (delta 2), reused 261 (delta 0)
Receiving objects: 100% (273/273), 25.55 KiB, done.
Resolving deltas: 100% (2/2), done. $ cd furious-robot-218 $ ls AppInfo.php FBUtils.php close.php images index.php stylesheets utils.php
```

上面显示的目录输出来自 PHP 示例应用程序。其他语言会有不同的文件。

### 2)做出改变

让我们在应用程序中调整一些小的东西，并把它推回到 Heroku，说明部署过程。例如，找到显示欢迎横幅的 HTML 行(在 PHP 应用程序中，这是在`index.php`第 157 行)，应该是这样的:

```
<p>Welcome to your Facebook app, running on <span>heroku</span>!</p>
```

使用您最喜欢的文本编辑器将这一行改为:

```
<p>This is my app, I can edit it all I want.</p>
```

保存文件，然后使用您的终端将更改提交给 Git:

```
:::term
$ git commit -am
"changed greeting" [master 0ff313a]
changed greeting 1 files changed, 1 insertions(+), 1 deletions(-)
```

### 3)部署到 Heroku

现在，有趣的部分是:将我们修改过的应用程序推送到 Heroku，这样任何在脸书上访问该应用程序的人都可以看到新版本。用 Git 推给 Heroku:

```
:::term
$ git push heroku
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 347 bytes, done.
Total 3 (delta 1), reused 0 (delta 0)

-----> Heroku receiving push
-----> PHP app detected
-----> Bundling Apache... done, v2.2.19
-----> Bundling PHP... done, v5.3.6
-----> Discovering process types
 Procfile declares types -> (none)
 Default types for PHP -> web
-----> Compiled slug size is 20.9MB
-----> Launching... done, v2
 http://furious-robot-218.herokuapp.com deployed to Heroku To git@heroku.com:furious-robot-218.git 396ec84..994290d master -> master
```

现在在浏览器中查看应用程序。您应该会看到更改后的欢迎横幅:

![Modified app](img/b952dddcc1ac4048e7fc8a9ae3054eca.png)

恭喜你，你现在是脸书应用开发者了！

## 在本地工作

在上一节中，我们将应用程序的更改推送到实时生产环境中，而没有在本地环境中进行测试。一个更好的处理应用程序的工作流程是在本地运行它，在那里测试，并在你进行测试有效的改进时提交更改。当您准备好部署时，使用`git push heroku`将更改推送到实时生产应用程序。

在本地计算机上运行应用程序的方法因编程语言和您在本地开发环境中的选择而异。例如，Mac OS X 上的 PHP 开发人员可以使用操作系统附带的 Apache 和 PHP 版本，方法是将应用程序的代码签出到他们主文件夹的`Sites`子目录中，并在`/etc/apache2/users/USERNAME.conf`中配置一个`VirtualHost`。另一方面，Ruby 开发者可能会和[领班](#)一起运行他们的应用程序，并在`http://localhost:5000/`访问它。

不管你如何在本地运行你的应用程序，有两个技巧你需要知道，它们是运行脸书应用程序的本地开发版本所特有的。

### 1)创建开发脸书应用程序

您之前创建的脸书应用指向您的 Heroku 应用的 URL(看起来像`https://furious-robot-218.herokuapp.com`)。这是制作 app。

对于开发，你需要向脸书注册另一个应用程序。与第一个应用程序不同，这个应用程序不会在 Heroku 上运行，而是会在您的本地工作站上运行，并有一个类似于`http://localhost:5000/`的 URL(或者您为本地机器配置的任何 URL)。

前往[脸书开发者](https://developers.facebook.com/apps)，再次点击**创建新应用**。选择一个名称，以表明这是您现有应用程序的开发版本。例如，如果您的另一个应用程序命名为“我的酷应用程序”，您可以将这个应用程序命名为“我的酷应用程序–开发”。

创建完成后，点击**网站**复选框并输入您的本地 URL:

![Website -- local](img/8a61cb6a8bfbd2f1d6ae84f13be66b92.png)

点击**保存更改**。

### 2)设置脸书应用环境变量

在用于设置网站 URL 的应用程序的同一设置页面上，您还可以找到应用程序 ID 和应用程序密码:

![App ID and secret](img/46a3d26059b89e1efb64c9978b994a9f.png)

这些是作为[配置变量](#)自动添加到 Heroku 应用程序中的，但是在您的本地环境中，您需要将它们设置为环境变量。如何设置环境变量取决于您的操作系统和运行应用程序的方法。这里有两个例子:

#### 福尔曼。包封/包围（动词 envelop 的简写）

如果你用 Foreman 运行你的应用程序，它会自动在你的应用程序代码签出的根目录下找到一个名为`.env`的文件。将您的应用 ID 和应用密码剪切并粘贴到`.env`:

```
FACEBOOK_APP_ID='964173273189'
FACEBOOK_SECRET='dcd5d23d003d53cb2b68e01'
```

您还应该通过将这一行附加到您的`.gitignore`之后，将`.env`从源代码控制中排除:

```
.env
```

#### 阿帕奇 SetEnv

如果您使用 Apache 运行您的应用程序，您可以使用`SetEnv`指令为本地应用程序的`VirtualHost`设置 env 变量。例如:

```
<VirtualHost *:80> DocumentRoot /Users/adam/Sites/mycoolapp-dev ServerName mycoolapp-dev.localhost SetEnv FACEBOOK_APP_ID 964173273189 SetEnv
FACEBOOK_SECRET dcd5d23d003d53cb2b68e01 </VirtualHost>
```

### 结论

完成这两项更改后，您应该能够在本地访问您的应用程序并访问所有脸书功能。当你准备好在世界上分享你的改变时，`git commit`然后`git push heroku`，然后访问你的生产应用程序以确认你的改变在 live 应用程序上正确工作。

## 进一步阅读

*   Heroku [开发中心](http://devcenter.heroku.com)
*   脸书[图形 API 文档](http://developers.facebook.com/docs/reference/api/)

## 分享这篇文章