# 用 Wufoo 创建简单的表单:一个开发者的视角

> 原文：<https://www.sitepoint.com/simple-form-creation-wufoo-developers-perspective/>

本文由 [Wufoo](http://www.wufoo.com) 赞助。感谢您对使 SitePoint 成为可能的赞助商的支持！

表格。这是当今网络世界中不可避免的邪恶。自从万维网出现以来，收集重要的数据，这些网站组件即使对经验丰富的开发人员来说也是令人沮丧的。从动态 JavaScript 动作到安全连接，web 表单通常比看起来要复杂得多。

这就是我个人经验的来源。最近，一个客户联系我，让我创建一个相对简单的网站，我面对一个令人不安的发现。在这个相当小的项目的所有独立部分中，最耗时和最困难的部分是硬塞一个动态而优雅的表单来收集关键信息。一定有更好的方法。

输入 [Wufoo](http://www.wufoo.com/?utm_medium=ppc&utm_source=sitepoint&utm_campaign=sitepoint) 。一个表单创建服务，面向非开发者，希望在没有开发者帮助的情况下，在他们的网站中包含高度功能化的表单。虽然对于像我这样的开发人员来说，使用一个以无开发人员使用为荣的服务似乎有些违背直觉，但我还是决定去看看。我有什么可失去的，对吗？

## 外行人的五福

对于那些没有听说过 Wufoo 的人来说，它本质上是一个创建复杂 web 表单的漂亮界面。Wufoo 销售的是一种服务，而不是一种特定的软件，因此，登陆该公司网站的用户将被引导创建一个 Wufoo 帐户，该帐户将用于利用创建工具。面向非技术人群，所有创建的表单都与他们收集的数据一起存储在 Wufoo 自己的服务器上。

这种基于服务的方法有明显的优势。首先，安全数据收集的复杂性是自动处理的。这包括数据库、后端代码和表单脚本。其次，创建的所有表单都可以在一个位置轻松管理，并且可以根据需要多次重用。

虽然这些功能对于 Wufoo 的目标受众来说无疑是一个强大的卖点，但这些服务有一个稍微不同的方面吸引了像我这样具有开发意识的人的注意。也就是有用的 API 和代码导出功能。

## 手头的任务

在我们进入 Wufoo 的形式创造的本质之前，让我展示一下我摆在我面前的任务。客户想要一个客户反馈表来记录对各种问题的回答。该表单将以多页的方式进行布局，包含动态和静态字段。当然，这是一个易于管理的请求，使用任意数量的在线表单生成器都可以轻松快速地完成。对我来说不幸的是，客户正在使用一个定制的 web 框架，并且需要他们的调查问卷与他们自己设计的外观和感觉无缝集成。

有了这些指导方针，我一头扎进了 Wufoo 的 GUI 魔法中，坚信这个工具可以让我在很短的时间内制作出一个功能完整的集成表单。

第一次登录该服务时，我看到一个空的[表单管理器](http://www.wufoo.com/form-builder/?utm_medium=ppc&utm_source=sitepoint&utm_campaign=sitepoint)，上面用粗体字显示了表单创建工具的路径。

等待字段的空表单:

![A form awaiting fields](img/99926d64e6a8b56d7b764d51acc81f7a.png)

向右跳，在 1980 年，我开始将字段拖放到一个空的画布上.第一个页面是一个简单的联系人信息页面，包含姓名和地址的文本字段，以及捕捉用户生日的日期组件。这里点几下，那里拖几下，在我意识到之前，我已经完成了第一部分。

第一部分完成:

![First section complete](img/ffe13c4d16d29f769f38aca37b102b50.png)

## 容易并不意味着有限

在这一点上，我的内部开发人员开始泄露对 GUI 加载的 web 工具的典型批评。其中最大的问题是我还没有写一行代码。我真的不需要。每当我将一个元素拖到表单中时，所有的标记和脚本都会在幕后创建并添加到表单中。

充分了解到我需要包含客户正在使用的现有样式的类名，我找到了一些更高级的选项。在左侧窗格中，列出了所有整洁的表单元素，我发现快速单击我创建的表单的任何部分都会打开一个上下文相关的菜单，如下所示:

![context sensitive menu](img/a296b66893722eca1d3c1ddfb110e8bf.png)

您会注意到这个菜单包含了您选择的任何表单元素共有的便利选项。在这种情况下，它是“电子邮件”字段。虽然通过点击一个按钮来切换权限和属性确实很方便，但是“CSS 布局关键字”最让我兴奋。有了这个功能，我可以根据我目前对客户 CSS 的了解，简单地向任何表单元素添加 class 关键字。当我的表单完成后，我只需要导出相应的代码，并将其放在网站框架中，而不必手动应用任何样式。

## 出口过程

说到导出，我甚至在完成表单之前就冒昧地测试了 Wufoo 对于像我这样的开发人员的真正用途。我想我也可以看看这个解决方案是否真的足够灵活，可以无缝集成到定制的网站设计中。

导航到我的 Wufoo 帐户的“代码管理器”会弹出一个菜单，其中有几个选项用于发布我新创建的表单:

![newly created form](img/74c184d56d9a2132e67a18ca433521c2.png)

在 Wufoo 托管表单的简单链接中，我选择了完整的 HTML/CSS 代码下载。这个选项给了我一个整洁的表单代码 ZIP 文件，包括脚本、图像和 CSS 文件夹。在里面，我发现实际的 HTML 代码驻留在 ZIP 存档的根级 index.html 文件中。

index.html 文件包含了您所期望的一切，包括所有布局良好的表单元素，并嵌套在任何指定的 CSS 类中。这相当于大约 500 行代码，而我一行也没写。下面是 Wufoo 生成的 HTML 代码示例:

```
<!DOCTYPE html>
<html>
<head>

<title>
Customer Feedback Form
</title>

<!-- Meta Tags -->
 <meta charset="utf-8">
 <meta name="generator" content="Wufoo">
 <meta name="robots" content="index, follow">

 <!-- CSS -->
 <link href="css/structure.css" rel="stylesheet">
 <link href="css/form.css" rel="stylesheet">

 <!-- JavaScript -->
 <script src="scripts/wufoo.js"></script>

 <!-- [if lt IE 10]>
 <script src="https://html5shiv.googlecode.com/svn/trunk/html5.js"></script>
 <![endif] -->
 </head>

 <body id="public">
 <div id="container" class="ltr">

 <h1 id="logo">
 <a href="http://www.wufoo.com" title="Powered by Wufoo">Wufoo</a>
 </h1>

 <form id="form3" name="form3" class="wufoo topLabel page" autocomplete="off" 
    enctype="multipart/form-data" method="post" novalidate 
    action="https://joewhewitson.wufoo.com/forms/q10npi4x0ghrcjv/#public">

 <header id="header" class="info">
 <h2>Customer Feedback Form</h2>
```

这里没有什么太花哨的东西，尽管你会注意到一些 html5 遗留浏览器支持以及必须的 Wufoo 品牌。您还会看到，该表单默认设置为向我的 Wufoo 帐户发送数据，这显然对客户端没有太大好处。由于我从事的项目需要将数据存储在客户自己的服务器上，所以我需要在这里手动设置一个 PHP 类来处理数据，并替换对 Wufoo 服务器的动作调用。至于样式，我只是简单地链接了客户已经创建的所需样式表，表单显示了与它周围的站点相同的主题。

## Wufoo API

在进一步研究 Wufoo 站点上的代码导出区域后，我找到了一个有趣的部分。标签为“ [API 信息](http://www.wufoo.com/docs/api/v3/?utm_medium=ppc&utm_source=sitepoint&utm_campaign=sitepoint)”，我忍不住高兴地点击，期待进一步定制我刚刚创建的表单。在 API Information 部分，我得到了一个 API 键，作为检索我的表单收集的数据的密码。在键的下面是一个列表，列出了我用惟一 ID 添加到表单中的所有表单元素。

立即打开文档，我发现表单检索的所有数据都可以通过这个 API 提取和操作。API 的使用可能性相当广泛。用户可以创建简单的脚本来备份表单信息，基于原始数据创建报告，甚至在将数据提交回 Wufoo 的服务器之前添加自定义的表单处理。对于拥有多个表单甚至多个子用户的人来说，详细的管理统计数据可以通过在本地服务器上使用的定制脚本来处理。

我认为这个 API 提供的最大好处是对 Wufoo 框架的透明访问。这允许在不牺牲数据可用性或灵活性的情况下使用其托管服务。在我的项目中，我能够说服客户创建一个 Wufoo 帐户，并将他们的数据存储在 Wufoo 服务器上，因为他们知道他们将保留对收集的所有信息的完全访问权。在利用 Wufoo 作为服务时，客户不再需要担心支持数据收集结构。

相反，客户端可以使用如下 GET 请求从其帐户中获取数据:

`https://client1.wufoo.com/api/v3/forms/q10nfwi89/fields.xml?pretty=true&system=true`

表单数据可以以 JSON 或 XML 格式导出和导入。只需传入表单标识符和格式首选项，数据就会被检索出来。cURL 也是受支持的，它将采用如下调用形式:

`curl -u [Unique API Key]:footastic [GET URL of desired content such as example above]`

通过拦截动作调用，用户可以在发送到 Wufoo 之前修改数据服务器端:

```
// index.html code
require_once('APIBootstrap.php');
$dataHandler = new APIBootstrap();
$functionName = $_GET['functionName'];
$dataHandler->$functionName();

// dataHandler code (submitForm function)
foreach ($_POST as $key => $value) {
    $params[$key] = $value;
} /*
* Do neat stuff with form values here
*/
try {
    $curl = new WufooCurl();
    $response = $curl->post(
        $params,

'https://client1.wufoo.com/api/v3/forms/apisubmitexample/entries.json',
        self::API_KEY);
} catch (Exception $e) {
    print_r($e);
}
```

很方便吧？

## 概括起来

总的来说，Web 开发在最近几年取得了长足的进步。曾经由技术型程序员主导的工作已经向内心更具创造力的人敞开了大门。这种变化可以归功于像 Wufoo 这样的功能性 GUI 工具。尽管这些工具被打上了可定制性有限的烙印，但是有了高级 API，这些先入为主的观念就不再站得住脚了。

这并不是说像 Wufoo 这样的服务没有任何限制。一如既往，在第三方服务上存储数据意味着您将受制于他们服务器的可靠性和安全性。这意味着收集机密信息的表单需要宿主进行更多的审查。

然而，对于那些需求不太严格的人来说，表单创建和托管解决方案可以减少开发和支持时间。哦，我有没有提到你仍然可以自由地访问数据，并根据自己的意愿定制结果代码？

这是一篇赞助文章。赞助它的公司付钱给 SitePoint 来撰写和发表它。由 SitePoint 独立撰写的赞助文章允许我们开发和展示对读者最有用和最相关的内容。

## 分享这篇文章