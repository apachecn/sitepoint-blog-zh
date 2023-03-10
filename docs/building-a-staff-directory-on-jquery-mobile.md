# 在 jQuery Mobile 上构建员工目录

> 原文：<https://www.sitepoint.com/building-a-staff-directory-on-jquery-mobile/>

今天，我们使用 jQuery Mobile 设计了一个公司员工目录。这基本上是一个公司网站，员工可以根据自己的部门获取其他员工的联系方式。

这只是一个初学者教程，我们将使用 Jquery Mobile 创建移动站点和应用程序。注意，这只是一个使用 HTML5、CSS 和 jQuery Mobile 创建的静态站点。

## 这是我们正在做的

[https://dl.dropbox.com/u/28591673/jqm/index.html](https://dl.dropbox.com/u/28591673/jqm/index.html)

## 什么是 JQM

根据 JQM 网站的说法，“jQery Mobile 是一个统一的用户界面系统，可以在所有流行的移动设备平台上无缝工作，建立在坚如磐石的 jQuery 和 jQuery UI 基础上。专注于基于渐进增强的轻量级代码库，具有灵活、易于主题化的设计。”总结一下，jQM 是一个移动框架，它使人们能够设计一个单独的、定制的 web 应用程序，该应用程序可以在当前所有流行的智能手机和平板电脑平台上工作。

目前 JQM 在以下平台上受支持:

*   ios
*   机器人
*   黑莓
*   崔盛希
*   windows 手机
*   Palm WebOs
*   智能移动终端操作系统
*   Meego

因此，jQM 驱动的应用程序可以在所有这些平台上运行。从而允许您编写更少的代码，做更多的事情。

## jQM 基础

我不会在这篇文章中讨论 Jquery Mobile 的基础知识，要了解基础知识，请阅读 [Hello jQuery Mobile](https://www.sitepoint.com/hello-jquery-mobile/) 。

让我们回顾一下 JQM 驱动的站点的基本结构。

```
<!DOCTYPE html>
<html>
	<head>
	<title>NuttyTech - Staff Directory</title>
	<meta name="viewport" content="width=device-width, initial-scale=1">
	<link rel="stylesheet" href="https://code.jquery.com/mobile/1.0/jquery.mobile-1.0.min.css" />
	<script type="text/javascript" src="https://code.jquery.com/jquery-1.6.4.min.js"></script>
	<script type="text/javascript" src="https://code.jquery.com/mobile/1.0/jquery.mobile-1.0.min.js"></script>
</head>
<body>

<div data-role="page">

	<div data-role="header">
		<h1>My Title</h1>
	</div><!-- /header -->

	<div data-role="content">
		<p>Hello world</p>
	</div><!-- /content -->

	<div data-role="footer">
		<h1>My Title</h1>
	</div><!-- /footer -->

</div><!-- /page -->

</body>
</html>
```

你首先看到的是 HTML5 Doctype `，然后在 head section 部分我们链接到 jQM css 文件，之后链接到 jQuery 文件，之后链接到 jQM javascript 文件。`

 `要在 jQM 中创建一个新页面，而不是链接到不同的 html 文件，只需创建一个新的数据角色为 page 的 div，并为它分配一个 id。要链接到新页面，您只需将 id 的链接传递给 href 属性。

然而，建议将您的站点创建为一系列独立的页面，因为它更干净、更轻量级，并且在没有 JavaScript 的情况下工作得更好。

为了创建页眉，我们使用“页眉”的数据角色，而对于内容，我们使用“内容”的数据角色，最后对于页脚，我们使用“页脚”的数据角色。

要为每个链接的标题创建标题，只需将 data-title= " "放入每个 data-role="page "。

## 创建主页。

公司员工目录的主页将只包含部门列表。

源代码如下所示。

```
<!DOCTYPE html>
		<html>
			<head>
			<title>NuttyTech - Staff Directory</title>
			<meta name="viewport" content="width=device-width, initial-scale=1">
			<link rel="stylesheet" href="styles/jquery.mobile-1.0.min.css" />
			<link rel="stylesheet" href="styles/style.css" />
			<script type="text/javascript" src="js/jquery.js"></script>
			<script type="text/javascript" src="js/jquery.mobile-1.0.min.js"></script>
		</head>
		<body>

		<div data-role="page">
			<div data-role="header" data-theme="b">
				<h1>Home</h1>
			</div><!-- /header -->

			<div data-role="content" id="home">
				<ul data-role="listview" data-theme="f" data-count-theme="e">
					<li><a href="accounts.html"><h2>Accounts Department</h2><p>We Manage the accounts for the company</p></a><p class="ui-li-count">2 staff</p></li>
					<li><a href="health.html"><h2>Health Department</h2><p>For all your health issues.</p></a><p class="ui-li-count">1 staffs</p></li>
					<li><a href="tech.html"><h2>I.T Department</h2><p>For all your I.T related enquires.</p></a><p class="ui-li-count">1 staffs</p></li>
				</ul>
			</div><!-- /content -->

			<div data-role="footer"data-theme="b">
				<h1>&amp;copy Copyright BuildMobile</h1>
			</div><!-- /footer -->

		</div><!-- /page -->
		</body>
		</html>
```

## 说明

在 head 部分，我们设置了一个 meta 标签，然后我们链接到所需的文件，jQM CSS 文件，jQuery JavaScript 文件和 jQM JavaScript 文件。style.css 文件包含了我自己的个人风格。

要在 jQM 中创建一个列表，通常的做法是定义一个 ul、ol 或 dl 标签，然后将 li 放入其中。唯一的区别是您在 ul 标签中添加了一个属性`data-role="listview"`,这样 jQM 就会知道这是一个列表。

要创建计数气泡，只需将 ul-li-count 类添加到您希望用作气泡的标签中。

## 部门页面

点击主页上的任何一个部门都会进入部门页面，在这里你可以看到每个部门的所有员工。

顶部有一个搜索过滤器，使搜索更容易实现。要在你的 jQM 支持的站点中做到这一点，你需要做的就是将`data-filter="true"`添加到 ul 中

完整的例子在
下面

 `这里，我们向 ul 添加了一个 data-filter="true ",这将显示一个搜索框。

部门(会计部门)的源代码

```
<!DOCTYPE html>
		<html>
			<head>
			<title>NuttyTech - Staff Directory</title>
			<meta name="viewport" content="width=device-width, initial-scale=1">
			<link rel="stylesheet" href="styles/jquery.mobile-1.0.min.css" />
			<link rel="stylesheet" href="styles/style.css" />
			<script type="text/javascript" src="js/jquery.js"></script>
			<script type="text/javascript" src="js/jquery.mobile-1.0.min.js"></script>
		</head>
		<body>

		<div data-role="page">

			<div data-role="header"data-theme="b">
				<h1>Accounts Department</h1>
				<a href="index.html" data-icon="home" data-theme="a">Home</a>
			</div><!-- /header -->

			<div data-role="content">
				<ul data-role="listview" data-theme="c" data-filter="true" data-filter-placeholder="Search for Staff" data-divider-theme="e">
					<li data-role="list-divider" data-theme="d">A</li>
					<li><a href="staffs/ade.html"><img  class="img" src="images/logo.jpg"/><h3>Ade Smith</h3></a></li>
					<li data-role="list-divider" data-theme="d">B</li>
					<li><a href="staffs/benita"><img class="img" src="images/logo.jpg"/><h3>Benita Bright</h3></a></li>
				</ul>
			</div><!-- /content -->

			<div data-role="footer" data-theme="b">
				<h1>&amp;copy Copyright BuildMobile</h1>
			</div><!-- /footer -->

		</div><!-- /page -->

		</body>
		</html>
```

## 说明

要创建搜索过滤器，您只需将 data-filter="true "添加到 ul 即可。将此添加到 ul 将在顶部创建一个搜索框。

搜索框的默认占位符文本是“Filter items…”要将其更改为您自己的文本，您只需将属性 data-Filter-placeholder = " Your Own Text "添加到 ul 标签中。

要划分你的列表，你需要做的就是给一个 li 添加一个 data-role="list-divider "的属性。

## 员工页面

选择人员时，您将被引导至员工页面。在这里，您将获得该员工的全部详细信息。下面是会计部门一名员工的员工页面。

```
<!DOCTYPE html>
		<html>
			<head>
			<title>NuttyTech - Staff Directory</title>
			<meta name="viewport" content="width=device-width, initial-scale=1">
			<link rel="stylesheet" href="../styles/jquery.mobile-1.0.min.css" />
			<link rel="stylesheet" href="../styles/style.css" />
			<script type="text/javascript" src="../js/jquery.js"></script>
			<script type="text/javascript" src="../js/jquery.mobile-1.0.min.js"></script>
		</head>
		<body>

		<div data-role="page" data-add-back-btn="true">

			<div data-role="header" data-theme="b">
				<h1>Ciara Michael - Accounts Department</h1>
				<a data-rel="back" data-icon="back" data-theme="a">Back</a>
			</div><!-- /header -->

			<div data-role="content">
				<img class="img" src="img/logo.jpg"/>
				<h4>Name:</h4><p>Ciara Michael</p>
				<h4>Address:</h4><p>No 12 Port Smith Avenue London</p>
				<h4>Email:</h4><p>ciara.michael@nuttytech.com</p>
				<h4>Mobile:</h4><p>123456789</p>
				<a href="tel:123456789" data-role="button" data-inline="true">Call Ciara Michael</a>
				<a href="sms:123456789" data-role="button" data-inline="true">SMS Ciara Michael</a>
				<a href="mailto:ciara.michael@nuttytech.com" data-role="button" data-inline="true">Mail Ciara Michael</a>
			</div><!-- /content -->

			<div data-role="footer" data-theme="b">
				<h1>&amp;copy Copyright BuildMobile</h1>
			</div><!-- /footer -->

		</div><!-- /page -->

		</body>
		</html>
```

## 说明

在这里，在员工视图页面中，我们使用了一些 HTML5 标签 sms 和 tel，使您能够发送 sms 或呼叫员工。

## 分享这篇文章``