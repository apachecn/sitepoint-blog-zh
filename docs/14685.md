# 操纵用户代理以获得准确的站点统计数据

> 原文：<https://www.sitepoint.com/site-stats-user-agent/>

如果你在你的网站上实现了任何类型的点击监控或跟踪，你可能不想包含任何你自己的点击。我每天都会检查我的每个网站，以确保它们都正常运行，并且没有出现任何奇怪的错误。然而，这严重扭曲了我使用的网站跟踪。

通常，您可以设置一个“自我特定的”cookie。然后，当调用跟踪脚本时，您可以简单地检查 cookie 是否存在，如果存在，就退出跟踪脚本。

然而，如果你像我一样，坦率地说害怕饼干，因为它们:

1.  很难测试，

3.  被称为愚蠢的事情

5.  不是 100%可靠吗

…并且您没有足够的运气在您的互联网连接上有一个静态 IP 地址，您可能更喜欢我们将要讨论的替代方法。

此外，许多网站管理员排除跟踪某些浏览器，已知不工作在他们的网站上。例如，许多网站屏蔽任何非 Internet Explorer 或 Netscape 浏览器，如 Mozilla 优秀的新 Firebird 和一大堆其他浏览器。能够操纵用户代理来欺骗网站以为我们在使用 Internet Explorer 6.0，而实际上我们运行的是 Firebird 0.7，这是非常方便的！

##### 用户代理

每个浏览器都通过一个叫做“用户代理”的东西来识别自己。简单地说，这只不过是包含各种信息的字符串。典型的用户代理可能如下所示:

```
Mozilla 4.0(compatible, MSIE6.0; Win NT 5.1)
```

这告诉我们，浏览器基于 Mozilla 4.0(大多数浏览器基于 Mozilla)，是 Internet Explorer 6，并且，计算机是 Windows NT 5.1(也称为 Windows XP)。Mozilla Firebird 浏览器的类似用户代理可能如下所示:

```
Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.5) Gecko/20031007 Firebird/0.7
```

服务器总是可以从浏览器获得这些信息。我们可以使用简单的 PHP 访问这些细节:

```
$agent=$_SERVER["HTTP_USER_AGENT"];
```

或者，在 ASP:

```
sUserAgent= Request.ServerVariables("HTTP_USER_AGENT")
```

我更喜欢使用服务器端语言来完成这个任务，而不是 Javascript，因为用户可以关闭 Javascript，这可能会导致整个脚本失败。有关特定语言实现的更多信息，请阅读相关手册或在 SitePoint 论坛上提问。

##### 修改计算机上的用户代理

我们操作用户代理的过程是特定于浏览器和操作系统的。我将描述 Windows XP 系统上更常见的浏览器的过程。这些方法对于其他基于 Windows 的系统来说几乎是相同的，对于 Linux 或 Mac 机器来说相当简单。

首先，我们需要决定将用户代理更改为什么。我们所需要做的就是在现有的用户代理中添加某种独特的字符串，用它我们可以识别自己。选择一些真正独特的、不太敏感的东西(例如，不要用一个单词作为密码)。我将使用“weirdbeardmt”——我在 SitePoint 论坛上的用户 ID。

***Mozilla 和 Mozilla 火鸟***

幸运的是，一些好心人已经编写了一个扩展，将这个功能直接构建到 Mozilla 和 Mozilla Firebird 中。可以在这里下载[。](http://chrispederick.myacen.com/work/firebird/useragentswitcher/)

该扩展安装了一个菜单项，允许您添加自定义用户代理并进行动态切换。这对于测试来说也是非常理想的(如果你需要改变你的用户代理来访问一个被屏蔽的网站，这也是非常容易的)。为了进行测试，您需要使用一个定制的用户代理。更新后的用户代理将看起来像这样:

```
Mozilla/5.0 (Windows; U; Windows NT 5.1; en-US; rv:1.5) Gecko/20031007 Firebird/0.7 StumbleUpon/1.901 weirdbeardmt
```

如果这个扩展不可思议地不可用，那么所做的就是修改 Mozilla 工作目录中的 prefs.js 文件。快速搜索“代理”应该能帮你找到。

***网络浏览器***

可惜在 IE 中修改用户代理需要注册表黑！如果您不习惯使用注册表，那么建议您不要继续使用这个示例，或者至少在开始之前做一个备份。导航至:

HKEY _ 当前 _ 用户软件微软视窗安全版本互联网设置

查找用户代理字符串，双击其名称进行调整。如果您使用的是自定义的 Internet Explorer“皮肤”，如 Netcaptor，那么也可以通过以下方式添加到您的用户代理:

HKEY _ 本地 _ 机器软件微软视窗安全版本互联网设置用户代理发布平台

然后，您可以在那里添加一个新字符串。您的新用户代理将如下所示:

```
Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1; Netcaptor 7.2.1; weirdbeardmt)
```

***歌剧 6/7***

切换用户代理的功能内置在 Opera 中(文件>首选项>网络)，但是 Opera 不允许指定自定义代理。不幸的是，这是无法回避的(正如 Opera 的 CTO 向我保证的那样！).

##### 使用老开关

现在我们有了新的定制用户代理，我们将如何使用它呢？我们真正需要做的是在用户代理中搜索标识符(weirdbeardmt)的存在。因此，对于我的跟踪脚本(包含在我的每个网站的每个页面上)，我只需添加以下内容:

```
<?php 

if(strpos($_SERVER["HTTP_USER_AGENT"],"weirdbeardmt")!=FALSE) { 

// continue with the tracking script 

} 

?>
```

在 ASP 中，这将如下所示:

```
<% 

If Not InStr(Request.ServerVariables("HTTP_USER_AGENT"),"weirdbeardmt") > 0 Then 

  'continue with your tracking script 

End If 

%>
```

就像他们说的那样。这种技术的唯一缺点是，您的定制用户代理将被发送到您访问的每个网页。这并没有很大的区别，除了网站管理员可以很容易地找到你的“秘密词”并使用它来识别你。然而，如果标识符不敏感，这只是一个小小的不便。

## 分享这篇文章