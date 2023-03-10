# 了解用户对 Raygun 的了解

> 原文：<https://www.sitepoint.com/know-what-your-users-know-with-raygun/>

![springtimesoft_raygun_1_raygun_1](img/317eff1d4623ba0901d60719ef329551.png)

*这篇文章由[汤姆·帕金](https://www.sitepoint.com/author/tparkin)进行了同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

我不确定你，但是我不喜欢错误。事实上，当我看到一个时，我通常会做这样的事情。

[https://www.youtube.com/embed/5oiMPKvxQzs](https://www.youtube.com/embed/5oiMPKvxQzs)

但是，如果您知道某个错误，您可以修复它。如果你不知道用户在使用你的应用程序浏览时遇到的问题，那就更糟了。一些研究表明，在大多数情况下，一旦某个操作导致了错误，用户就不愿意再次执行该操作。因此，了解错误并尽快做出响应至关重要。在这种情况下，今天的嘉宾是[ray gun](http://raygun.io)——一种方便且易于设置的错误跟踪服务。

在本文中，我将向您展示如何将 Raygun 集成到您的 Rails 应用程序中，并在十分钟内开始跟踪错误。我们还将设置一个特殊的“脉冲”服务，让我们可以查看有多少用户正在浏览网站，他们来自哪里，他们有多满意等等！

源代码可以在 [GitHub](https://github.com/bodrovis/Sitepoint-source/tree/master/Error_Reporting_with_Raygun) 上获得。

## Raygun 及其服务

Raygun 于 2013 年初发射。最初这家公司叫做 Mindscape——它为。NET 开发者(比如光速 ORM 和 Web Workbench)。几年来，创建新服务的想法在团队中反复出现，因为他们在寻找更好的方法来处理生产中的 bug。因此，他们决定开发自己的解决方案来解决这个问题。目前，Raygun 的团队包括大约 20 人，但随着公司在美国的扩张，团队在逐渐壮大。

Raygun 提供两大服务:崩溃报告和 Pulse。

[崩溃报告](https://raygun.io/products/crash-reporting)为来自 Rails 和。NET 移植到 Android 和 Windows Phone 上(该团队正在努力增加对更多平台的支持)。它带有一个漂亮且易于使用的仪表板，您可以在其中浏览捕获的错误，查看详细信息，设置错误的状态等。此外，崩溃报告可以与团队聊天平台(Hipchat 或 Slack)、项目管理工具(JIRA 和朋友)以及问题跟踪器(GitHub、Bitbucket 等)集成。

另一项服务，用于用户监控的 [Pulse](https://raygun.io/products/real-user-monitoring) ，经过一年的开发，于大约一周前推出。它提供了关于性能的详细信息(有一个很好的时间表，确实有助于找到瓶颈)、使用的软件、用户的位置和设备。

请注意，这两项服务都是付费的，但是您可以免费试用一个月。崩溃报告的定价可在[此处](https://raygun.io/products/crash-reporting#crash-reporting-pricing)获得。Pulse 的定价可以在[这里](https://raygun.io/products/real-user-monitoring)找到。该公司正在考虑增加更多的定价方案(例如，针对个人开发者)。

## 准备

我们的准备会很快。创建一个没有默认测试套件的新 Rails 应用程序:

```
$ rails new Raygunner -T 
```

在这个演示中，我将使用 Rails 4，但是您可以继续使用 Rails 3。Raygun 适配器不支持 Rails 2。

现在只需创建一个控制器和一个简单的视图:

*pages_controller.rb*

```
class PagesController < ApplicationController
end 
```

*views/pages/index . html . erb*

```
Hi! 
```

现在我们要为应用程序设置根路由:

*config/routes.rb*

```
[...]
root to: 'pages#index'
[...] 
```

就这样！我们准备好开始整合雷根了！

## 积分射线枪

首先将以下 gem 添加到您的 gem 文件中:

*Gemfile*

```
[...]
gem 'raygun4ruby'
[...] 
```

奔跑

```
$ bundle install 
```

raygun4ruby 是一个简单易用的适配器，支持 Rails、Sinatra 和 plain Ruby。

你需要一个雷根的账户。通过提供姓名、电子邮件和密码，在此获取 30 天免费试用版[。接下来，导航到 app.raygun.io/s/createapp 的](https://app.raygun.io/signup)[创建一个新的应用程序。完成后，打开应用程序的设置页面并找到 API 键字段。这把钥匙将用于向 Raygun 传输数据。](https://app.raygun.io/s/createapp)

使用以下命令生成 Raygun 的初始化文件:

```
$ rails g raygun:install YOUR_APP_KEY_HERE 
```

一个新的 **raygun.rb** 文件将在**初始化器**目录下创建。它包含一些非常基本的配置，我们将根据需要对其进行更改。默认情况下，在开发模式下，没有数据被发送到 Raygun 服务器，但是出于演示的目的，我们希望这样做。因此将`config.enable_reporting`设置为`true`。

*config/initializer/ray gun . Rb*

```
[...]
config.enable_reporting = true
[...] 
```

现在快跑

```
$ rake raygun:test 
```

生成用于测试目的的异常。等待几秒钟，打开应用程序的仪表板进行崩溃报告——您应该会看到类似“Woohoo！你的 Raygun Ruby 连接设置正确。

顺便说一下，仪表板中的任何错误都有以下状态之一:

*   活跃的
*   断然的
*   版本中已解决
*   忽略
*   永久忽略

例如，如果你收到一个关于错误的通知，你~~解雇负责人~~，要求开发人员修复它，然后将其状态设置为“已解决”。为此，请打开该错误并单击右上角的下拉列表。

太好了，现在 Raygun 不知疲倦地关注着我们的申请！

## 引发错误

好吧，我现在心情很不好，想从我的控制器的动作中调用一个不存在的方法:

*pages_controller.rb*

```
[...]
def index
    destroy_everything
end
[...] 
```

重新加载网站的主页，确实会出现一个错误页面。但是，更重要的是，在控制台中您会看到:

```
[Raygun] Tracking Exception... 
```

这基本上意味着错误被捕获并发送给 Raygun。目前，该产品没有独立版本。该公司正在考虑发布一个，允许你在自己的服务器上存储数据。

好的，导航到仪表板，等待错误出现(这可能需要一些时间)。注意，每个错误都有一组非常完整的信息:堆栈跟踪、服务器名和 HTTP 数据。

你可能担心敏感数据。例如，如果不想将 like 密码发送到 Raygun 服务器，该怎么办？默认情况下，Raygun 的适配器不会发送任何在`Rails.application.config.filter_parameters`中列出的参数，但是你也可以在初始化文件中改变它:

*config/initializer/ray gun . Rb*

```
config.filter_parameters = [ :password, :cvv ] 
```

## 自定义数据和标记

如果您想在跟踪错误时提供一些自定义数据，这是完全可能的。假设我们喜欢被零除:

*pages_controller.rb*

```
[...]
def index   
  begin
    1 / 0   
  rescue Exception => e
    Raygun.track_exception(e, custom_data: {my: 'I tried to do some zero division. How cool is that?'})
  end
end
[...] 
```

如您所见，`Raygun`有一个`track_exception`方法，它接受一个异常和一个带有定制数据的散列。您可以在跟踪错误时提供应用程序的版本。这是通过在初始化文件中设置`config.version`来实现的:

*config/initializer/ray gun . Rb*

```
config.version = "1.0.0.4" 
```

设置标签也很容易。只需在初始化式中使用`config.tags`:

*config/initializer/ray gun . Rb*

```
config.tags = ['heroku'] 
```

例如，如果您在开发模式下发送错误报告，将会自动设置`development`标签。

## 忽略错误

无知是福。有时候。如果你不想得到关于特定异常的通知，修改初始化文件如下:

*config/initializer/ray gun . Rb*

```
[...]
config.ignore << ['ZeroDivisionError']
[...] 
```

现在，如果您重新加载服务器并再次导航到根页面，您将看不到

```
[Raygun] Tracking Exception... 
```

在控制台中，这样错误就不会被跟踪。

请记住，默认情况下，Raygun [忽略](https://github.com/MindscapeHQ/raygun4ruby/blob/master/lib/raygun/configuration.rb#L51)以下异常:

*   `ActiveRecord::RecordNotFound`
*   `ActionController::RoutingError`
*   `ActionController::InvalidAuthenticityToken`
*   `ActionDispatch::ParamsParser::ParseError`
*   `CGI::Session::CookieStore::TamperedWithCookie`
*   `ActionController::UnknownAction`
*   `AbstractController::ActionNotFound`
*   `Mongoid::Errors::DocumentNotFound`

如果出于某种原因，您确实想跟踪它们，只需从散列中删除相应的项目:

*config/initializer/ray gun . Rb*

```
config.ignore.delete('ActionController::InvalidAuthenticityToken') 
```

## 受影响的用户

有多少用户受到错误的影响？雷根也可以告诉你！你所要做的就是在你的控制器中提供一个`current_user`方法(如你所知，默认情况下，像 Devise 或巫术这样的解决方案会提供这些方法),返回一个响应`id`、`email`或`username`方法的对象。

如果您的方法有其他名称，只需相应地覆盖`config.affected_user_method`:

*config/initializer/ray gun . Rb*

```
config.affected_user_method = :my_current_user 
```

另外，如果返回的对象不响应上面提到的方法，您可以很容易地添加您的方法名:

*config/initializer/ray gun . Rb*

```
config.affected_user_identifier_methods << :login 
```

希望你的用户保持匿名？创建一个这样的方法:

```
def raygun_user
  cookies.permanent[:raygun_user_identifier] ||= SecureRandom.uuid
end 
```

在`ApplicationController`和`config.affected_user_method`处拧动。

然后，您将能够在仪表板的“用户”部分观察受影响的用户(可能需要一些时间才会显示数据)。您还可以看到用户的设备、Gravatar 图像和脸书个人资料(当然，如果提供的电子邮件链接到任何一个的话)。

## 射线枪脉冲

让我们快速设置射线枪脉冲来监控我们的用户。目前，它仅支持 JavaScript，因此将以下代码放入应用程序的布局文件中:

*视图/布局/应用程序. html.erb*

```
<script>
!function(n,e,a,t,r,s,c){n.RaygunObject=r,n[r]=n[r]||function(){(n[r].o=n[r].o
                ||[]).push(arguments)},s=e.createElement(a),c=e.getElementsByTagName(a)[0],
        s.async=1,s.src=t,c.parentNode.insertBefore(s,c)}(window,document,"script",
        "//cdn.raygun.io/raygun4js/raygun.min.js","rg4js");

rg4js('apiKey', 'YOU_KEY_HERE');
rg4js('enablePulse', true);
rg4js('enableCrashReporting', false);
</script> 
```

Als，o 确保在您的页面上包含以下代码:

```
<script>
  rg4js('setUser', {
    identifier: 'USERS_ID_OR_EMAIL',
    isAnonymous: false,
    email: 'USERS_EMAIL',
    firstName: 'Name',
    fullName: 'Name Surname'
  });
</script> 
```

在你的仪表盘的 Pulse 部分，你将能够观察到关于用户的漂亮数据(包括实时监控显示有多少用户在浏览你的网站，他们来自哪里)。

很快，不是吗？

## 与 GitHub 集成

作为一个小奖励，我将指导您如何将 Raygun 的应用程序与 GitHub repo 集成。

首先，在 Raygun 网站上打开你的 app 的设置。导航到集成–您将看到 Raygun 当前支持的所有集成的列表。点击 GitHub，切换到设置页面。现在你必须登录 GitHub 并授予 Raygun 一些权限才能继续。

完成后，从下拉列表中选择一个回购。将为此回购创建新发行(或者您可以链接到现有发行)。勾选“启用”旁边的复选标记，然后单击“保存”。如果你愿意，你也可以选择“在 GitHub 中关闭时将链接错误标记为已解决”——这也是一个不错的功能。

现在导航到崩溃报告仪表板，并选择任何活动错误。在这里点击集成下拉菜单，并从列表中选择 GitHub。您可以创建新问题或链接到现有问题。现在观察所选 repo 的 GitHub 问题以查看变化。真方便！

## 结论

在本文中，我们快速浏览了 ray gun——一个适用于各种平台的优秀监控服务。当然，还有更多，所以我真的建议浏览它的[文档](https://raygun.io/docs)并更多地使用仪表板。我发现这项服务很强大，但同时也很容易使用，所以绝对值得一试。

您更喜欢哪种监控解决方案？你将来会考虑使用 Raygun 吗？在评论中分享你的经历，不要犹豫，把你的问题发给我。再见！

*本文是与工程学副教授、博士 Sergey Gusnin 合著的。*

## 分享这篇文章