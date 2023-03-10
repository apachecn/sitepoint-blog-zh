# 如何设置具有九重功能的连续部署

> 原文：<https://www.sitepoint.com/set-continuous-deployment-ninefold/>

*本帖由[九倍](https://ninefold.com/)赞助。感谢您对使 SitePoint 成为可能的赞助商的支持！*

我记得大约九年前，部署还是一个手动过程。有时，部署哪怕是最简单的站点也需要 99 分钟。在单调和沮丧的尺度上，部署是一个坚实的“9”。

谢天谢地，那些日子已经过去了。随着[持续集成](http://en.wikipedia.org/wiki/Continuous_integration)已经成为一种普遍的、出乎意料的实践，事情怎么会变得更好呢？答案？持续部署。现在，让我们把术语搞清楚，因为连续部署有大约 999 种定义方式，所以我将选择我喜欢的一种。

> 持续部署是自动部署每个通过自动化测试的代码变更的实践。

这与[连续交付](http://en.m.wikipedia.org/wiki/Continuous_delivery)不同，在连续交付中，您自动将代码变更交付到一个进行严格集成测试的阶段化环境中。连续部署(本文剩余部分的 CDep)意味着，您做出一个变更，将它推送到主存储库，测试运行并通过，代码被部署到生产中。嘣！(顺便提一下，[本帖](http://puppetlabs.com/blog/continuous-delivery-vs-continuous-deployment-whats-diff)和我都同意这些定义。)

CDep 不是为弱或弱测试覆盖。那些敢于尝试 CDep 的人对自动化测试覆盖应用程序的所有功能有很大的信心。这不是一个小壮举。我会说 99.999%的项目可能没有测试覆盖率来完成 CDep。但是对于那些付出努力并遵守纪律的人来说，他们的回报是巨大的:相信变化不会导致倒退，并且相信您不会浪费时间进行手动部署。CDep 劳动的果实是葡萄树上最甜的。

CDep 的日益普及直接归因于平台即服务(PaaS)的兴起。PaaS 有很多，今天我们就来盘点九倍。

![Ninefold site](img/98fcdbc6f5ac41535f35e8dea22f3dff.png)

Ninefold 是一个专门托管 Rails 应用程序的 PaaS。除了大多数 PaaS 产品提供的易于部署的特性，Ninefold 还为 Redis、PostgreSQL、SendGrid、New Relic、Memcached 等提供了许多插件。Ninefold 的一个关键优势是添加原始虚拟服务器的能力。如果你想利用的软件没有插件，只需启动一个虚拟机，自己托管它。

Ninefold 还集成了各种持续集成平台，如 [Codeship](http://codeship.io) 、 [TravisCI](http://travisci.org) 和 [CircleCI](http://circleci.com) 。

我今天的演示将向您展示如何使用 Ninefold 和 Codeship 为 Rails 应用程序轻松创建 CDep 工作流。

## 九件事

我们的应用程序是一个简单的 Rails 应用程序，服务于那些希望列出 9 的倍数的事物的人的关键需求。用户可以把一个东西和它的九倍增加到不断增长的九个东西列表中。这是/在此处插入当前热门的社交应用程序/-杀手。它看起来是这样的:

![Our NineThings app](img/ebe722982ef5b0493d34108e9b787ed4.png)

是不是很美？我不会详细说明它是如何设置的，因为这是一个只使用生成器的 Rails 应用程序，添加了`foundation-rails` gem 使它更简洁。使用`scaffold`生成器生成了`NineThings`模型-视图-控制器序列。哦，我用的是 PostgreSQL。这个设置没什么特别的。

源代码在[这个 Github 库](http://github.com/sitepoint-examples/ninefold-ninethings)中，这是我在向 Ninefold 注册应用程序时将使用的。请随意使用这个应用程序(或者选择您自己的 Rails 应用程序)并跟随。

## 报名九重

为了使用 Ninefold 作为我们的 PaaS 平台，您需要注册。前往[九重网站](http://ninefold.com)并填写注册表格。提交表格后，你会在[面板](http://portal.ninefold.com)上看到一条欢迎信息。

![NineFold welcome](img/5579a7a887b430100c0429af71101ebe.png)

关闭欢迎屏幕，点击“部署新应用”。Ninefold 需要的第一条信息是源代码库的位置。在我的例子中，是我之前提到的 Github 库。因此，我点击“登录 Github”，批准 Ninefold 想要做的事情，我的 Github 组织和存储库的列表就会出现在仪表板上。太酷了。

![Repos](img/34e300d3a2f68ce9c832767b17404de1.png)

在为 Ninefold 选择了要观察的存储库和分支之后，您需要决定是否希望 Ninefold 在每次推送至所选分支时进行部署。现在我们先说“是”,然后进入下一步，设置我们的部署环境。

![Environment](img/60289d251bd5c32e2f2fc4f25b128d1f.png)

Ninefold 根据您的应用程序的可伸缩性需求提供了各种基础设施选项。对于这个应用程序，我只是测试的东西，并希望保持它便宜/免费。我可以对最小的 PostgreSQL 数据库 web 服务器使用 1.5GB 的选项，并对后台工作人员说“不”(尽管 Ninefold 对此不收费)。这显示了每月 46.91 美元的费用，在当前的促销活动中，九倍[将免除我账单的前 50 美元](https://ninefold.com/promotional-terms/)，太完美了。下一步。求你了。

![Software stack](img/ad35c0ea5cb5b45899228b33f79bc53c.png)

最后的细节包括所需的 Ruby 版本、应用程序名、插件和任何要运行的定制部署计划。我的应用程序不会使用这些，所以我兴奋地点击“部署”。

![Deployment](img/915744331d15c68beb93203aaf963113.png)

部署需要几分钟，所以这是一个介绍[布鲁特斯的好时机，Ninefold 的剃牦牛](https://ninefold.com/blog/2013/11/07/brutus-ninefold-yak/)。Brutus 自带九重 CLI，可以安装九重 gem ( `gem install ninefold`或者添加到你的 **Gemfile** 和`bundle`)。打开终端，进入 Rails 应用程序项目目录，安装 gem。

很明显，CLI 是基于一个`ninefold`命令的。键入`ninefold`可以让您了解可用的选项:

![CLI](img/e46b647c6dd875eea2030a04d5c40502.png)

首先要做的是登录。键入`ninefold signin`并输入您的九个用户信息。布鲁特斯会跑去给你签名。登录后，CLI 允许您使用`ninefold app`命令与九重应用程序进行交互。`ninefold app list`列出了我的单个 ninethings 应用程序。(顺便说一句:还有`ninefold brutus`命令可以让我们最喜欢的牦牛咀嚼和说话。试试吧，很好玩的。)

我不会重复所有的 CLI 命令，因为它们是不言自明的。我将突出显示的是`ninefold app redeploy_command`，它列出了重新部署应用程序的 CLI 命令。就我而言:

```
AUTH_TOKEN=<my auth token> APP_ID=10906 ninefold app redeploy --robot --sure
```

这个命令很重要，因为这是获取您的身份验证令牌和应用程序 id 的最简单方法，如果您想在九重仪表板之外重新部署应用程序，这是必需的(在某些情况下)。我今天不会使用这个命令，但是如果你在九层，最好知道。

好了，现在，你的九重应用应该部署好了。九重应用仪表板看起来像:

![Application dashboard](img/c0177def1bc33350b4be8cb5a1fe1393.png)

“Overview”选项卡包含了您所期望的所有信息:应用程序名称、URL、存储库、分支等。“部署 URL”是我们稍后用来获取 CDep 的东西。请注意，“当我向我的代码推送更新时，自动重新部署我的应用程序”复选框已启用。

![Configuration tab](img/c32b1dec37edf1fcd68ce35445f622be.png)

“配置”选项卡允许环境变量、自定义部署命令和插件配置。其余的选项卡是不言自明的，因此您可以自己探索它们。

返回“概述”选项卡，单击“域”链接。你的应用程序将会打开，你可以添加/查看所有九样东西。我们在互联网上。来推一个微不足道的改变。

```
# app/views/layouts/application.html.erb

<body>
  <div class="row">
  <h1>All the NineThings!!</h1>
  <h2>Let's get over 9000 NineThings!</h2> <!-- I ADDED THIS -->
    <%= yield %>
  </div>
  <%= javascript_include_tag "application" %>
</body>
```

对我们布局的微小改动。提交更改并将其推送到 Github repo，确保您使用的是`master`分支。回到九重应用仪表板，等待几秒钟。左下角将弹出以下通知:

![Notification](img/c2540499f50fc1a2b86d301f674fa0df.png)

非常好。现在，我们处于*连续交付*的领域中(如果我们有好的测试)。我们想要更多，我们想要持续部署。这就是代码的用处。

## 代码船

Codeship 是一家提供持续集成和交付(以及今天的部署)即服务的公司。[我们在](https://www.sitepoint.com/continuous-delivery-right-way-deploy-software/)之前已经写过关于 Codeship 的文章，但是简单来说，Codeship 会观察你的源代码库的变化，为每一个变化运行你的测试。通过一点配置，Codeship 还可以通知您失败/成功，运行任何命令，和/或部署您的代码。最后一个是我们今天的重点。

去注册一个 Codeship 账户吧。当你第一次注册时，Codeship onboards 会为你提供一个向导来创建你的第一个项目。

![Codeship wizard](img/5a9d08bad7ced0509b2022c35defaed5.png)

你将被提示选择你的源代码提供者，Github 或者 Bitbucket。然后它会连接到那个提供者(当然，要经过您的同意)并列出您的库。选择九样东西回购。

![Codeship wizard](img/7774e0d5cf2ccfe4ac3bac5672a1ca4a.png)

此时，您需要指定您的设置和测试命令:

![Codeship wizard 2](img/8c75a741db7194d0751ca3e0e01e1c7f.png)

安装命令的运行使您的项目到达测试准备就绪的点。测试命令运行您的测试。在我们的例子中，我们安装我们选择的 Ruby 版本，捆绑包，设置 RAILS_ENV，并加载模式。默认情况下，Codeship 已经设置好了这一切，但是您可以根据需要更改或添加任何内容。

完成向导后，您将进入项目页面。在页面的顶部，Codeship 给出了一些关于下一步该做什么的建议:

![Codeship next](img/597b93804c9bffa19072d9b595782782.png)

哦，看那儿！好像他们能读懂我们的思想。第一个按钮是“设置持续部署”。我说我们点击它。

![Codeship CDep](img/1404ea1300b8fcb7586d298a4e72d398.png)

Codeship 提供了许多在成功构建后进行部署的选项。Ninefold 不在这些按钮上，但这是因为有 9999 种方法可以在 Ninefold 上重新部署您的应用程序。这可能是九大星系中最容易整合的服务。

[直接从 Codeship 博客](http://blog.codeship.io/2014/11/04/continuous-delivery-ninefold-codeship.html)开始，将部署设置到九重很容易，因为:

1.  单击“$script”按钮
2.  输入您的九倍“部署 URL”
3.  完成了。

您从哪里获得部署 URL？精明的读者记得它在九重应用仪表板“概述”页面上:

![Overview setup CDep](img/1aa9730273c6c0929a2e16df4eeefc17.png)

就在那里。另外，确保*取消选中*的“自动部署”复选框。否则，Ninefold 将在每次推送时部署您的应用程序，不管是否通过测试。

好的，返回 Codeship.io 项目页面，粘贴那个部署 URL。

![CS Deployment script](img/4dc2b0dc6878de2809c2a1072c7d1a95.png)

保存步骤，我们就完成了。真的。

## CDep 在房子里

如果你不相信我，我会证明给你看。让我们对我们的应用程序做另一个显著的改变:

```
# app/views/layouts/application.html.erb

<body>
  <div class="row">
  <h1>All the NineThings!!</h1>
  <h2>Let's get over 9000 NineThings!</h2>
  <h3>Over 9000!!</h3> <!-- I ADDED THIS -->
    <%= yield %>
  </div>
  <%= javascript_include_tag "application" %>
</body>
```

现在，在应用程序中引入一个测试失败，就像这样:

```
# test/models/nine_thing_test.rb

class NineThingTest < ActiveSupport::TestCase
  test "the truth" do
    assert false
  end
end
```

在您的本地应用程序目录中运行`rake`将显示失败。提交更改并将其推送到 GitHub 存储库中。如果您正在查看项目的 Codeship.io 项目页面，将会启动一个构建，如下所示:

![CS Build](img/8a35ce8bb80f5ba57a8d47eb90ad7382.png)

如你所见，它失败了。如果你重新加载九重托管应用，我们的`h3`不在那里。

接下来，打开两个浏览器窗口:一个在 Codeship 项目页面，另一个在九重应用仪表板。修复测试(将`false`更改为`true`)，提交并推送。

![It's fixed](img/78c095e010a992e20c90b5cdf517e672.png)

Codeship 运行构建，运行测试，测试通过，然后部署到九重。Ninefold 然后重新部署应用程序。我告诉过你我们结束了。从 1 到 9，我给整个过程打 9+！

重新加载九重应用程序 URL 将显示我们的更改已部署:

![The change](img/dc0cb9be25a3d9ad688109596fe47779.png)

瞧啊。我们要像 1999 年一样狂欢。

## 包扎

持续部署是让开发人员生活更轻松的圣杯。它让你有机会专注于代码，而 Ninefold 专注于基础设施，Codeship 专注于部署。希望你能受到启发，把它融入到你的工作流程中。我保证多 9 倍的自由时间！

## 分享这篇文章