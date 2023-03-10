# FigDice 入门

> 原文：<https://www.sitepoint.com/getting-started-figdice/>

在众多的模板系统中，大多数都以几乎相同的方式工作；变量是用某种语法“注入”的，不管是花括号、百分号还是该库的约定。他们通常有基本的控制结构，比如`if...then`，当然还有迭代。

然而，FigDice 采取了完全不同的方法。受 [PHPTAL](http://phptal.org/) 的启发——这是未来文章的主题——它让视图层负责“拉入”所需的数据，而不是依赖控制器来组装和“推送”数据到模板中。

![Figs fruits](img/47575b64e9ac2e0c7581dd793dbe4fe7.png)

在这个由两部分组成的系列中，我将更详细地了解它，了解它是如何工作的以及如何使用它。

## 装置

你可以从[网站](http://www.figdice.org/en/download.html)、 [Github](https://github.com/gabrielzerbib/figdice) 或者更好的，Composer 那里获得代码:

```
"figdice/figdice": "dev-master" 
```

该库也可用作`.phar`；你可以下载[最新版本](https://github.com/gabrielzerbib/figdice/releases/download/2.0.4/figdice-2.0.4.phar)(直接链接)或者使用`figdice-make-phar.php`自己生成，可以在 [Github 库](https://github.com/gabrielzerbib/figdice)中获得。

## 基本用法

让我们首先看看 FigDice 最基本的功能——创建和渲染视图。

首先，创建视图的新实例:

```
$view = new \figdice\View();
```

显然，您需要确保库是可用的——使用 Composer 提供了一个合适的自动加载器。

接下来，你需要加载合适的模板，*相对于当前路径*:

```
$view->loadFile( '../templates/home.html' );
```

要渲染模板，调用`render()`:

```
$view->render(); // returns the HTML content
```

![figdice-arch-550](img/403f0d0be61e325b8793c0f53b700af6.png)

记住这一点，让我们开始构建一些模板。

## 框架应用程序

我创建了一个简单的应用程序，其中包含了本文中的所有代码，这也更好地说明了它们是如何组合在一起的。它使用了 Silex，但是你不需要特别透彻的知识就能理解发生了什么。

你可以在 Github 上找到它[，你也可以](https://github.com/sitepoint-examples/figdice-example-part-two)[在这里](http://figdice.demos.lukaswhite.com/)看到它的运行。请注意，该演示包括我们将在第二部分稍后添加的功能。

## 创建布局

首先要指出的是 FigDice 很大程度上是基于 XML 的。因此，它依赖于一些定制的 XML 元素。要求您的 HTML 标记正确关闭。

让我们从创建一个简单的两列页面布局开始。

```
<!-- file: layout.html -->
<html>
	<head>
		<title fig:slot="docTitle" />

		<link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap.min.css" />
		<link rel="stylesheet" href="//netdna.bootstrapcdn.com/bootstrap/3.1.1/css/bootstrap-theme.min.css" />
	</head> 
	<body>
		<div class="container">
			<header class="header">
			<fig:include file="menu.html" />
				<h3 class="text-muted">FigDice Example</h3>
			</header>

			<div class="row">

				<div class="col-sm-12 col-md-9 col-lg-9">

					<div class="content">
						<h1 fig:slot="pageTitle" />      
						<div fig:slot="pageContent" />
					</div>

				</div>

				<div class="col-sm-12 col-md-3 col-lg-3">

					<fig:include file="tweets.html" />

				</div>

			</div>

			<footer id="footer">
				<fig:include file="footer.html" />
			</footer>

		</div>

	</body>
</html>
```

让我们检查一些事情。

`<title fig:slot="docTitle" />`用于生成`<title>`标签，通过定义一个“slot”让值进入。我们很快就会看到这些数据是如何被“插入”到插槽中的。

注意`<link>`标签是如何结束的。这很重要；因为 FigDice 大量使用 XML，所以它必须相应地进行验证。

`<fig:include file="menu.html" />`不言自明——fig dice 可以轻松地将模板分割成更小的模板文件。我们对页脚也做了同样的事情—`footer.html`——还有一个 Tweets 块(`tweets.html`),我们将很快构建它。

以下是`menu.html`的内容:

```
<ul class="nav nav-pills pull-right">
	<li><a href="/">Home</a></li>
	<li><a href="/about">About</a></li>
</ul>
```

和`footer.html`:

```
<p>&copy; 2014 Someone or Other</p>
```

## 简单的主页

现在让我们为示例应用程序创建一个主页。在名为`index.html`的新文件中:

```
<xml fig:mute="true"> <!-- Mute because this tag should not end up in the HTML document. But the FigDice template must have an XML root node. -->

		<!-- Load the page layout -->
		<fig:include file="layout.html" />

		<!-- Set the <title> tag -->
		<title fig:plug="docTitle">Sample application homepage</title>

		<!-- Set the <h1> tag -->
		<h1 fig:plug="pageTitle">Homepage</h1>

		<!-- "Plug in" the page content -->
		<div fig:plug="pageContent">Lorem ipsum...</div>
	</xml>
```

这次我们的模板是纯 XML，并引入了更多的 FigDice 标签。

注意，开始的`<xml>`标签有一个名为`fig:mute`的属性，它被设置为 true。正如评论所指出的，这个属性告诉 FigDice 不要在最终的 HTML 中包含这个元素。

向 FigDice 介绍我们刚刚创建的布局。然而，这并不像 PHP `include`那样包含文件；这就是*插槽*和*插头*出现的地方。

`<div fig:plug="pageContent">Lorem ipsum...</div>`表示我们应该将内容“插入”到名为`pageContent`的“插槽”中，您可能还记得我们在`layout.html`中创建的插槽。

最后，让我们定义一个非常简单的路径来显示页面:

```
$app->get('/', function () use ($view) {

	$view->loadFile( '../templates/home.html' );

	return $view->render();
});
```

## 使用数据

在传统的 MVC 方法中，控制器检索、组装数据，然后将数据“注入”视图。这意味着控制器必须知道需要什么数据以及哪些视图对它感兴趣。

FigDice 采取了不同的方法。视图负责“拉入”它们需要的数据，而不是依赖控制器来处理数据。

为了做到这一点，FigDice 使用了**饲料**。提要是一个扩展了`figdice\Feed`的类，它必须包含一个`run()`方法来返回适当的数据。

为了演示这一点，让我们创建一个块，在每个页面上显示用户的最新 Tweets。

如果您回头看看布局，您会看到我们已经添加了以下行来包含它:

```
<fig:include file="tweets.html" />
```

实际上，从 [Twitter API](https://dev.twitter.com/) 获取推文相对简单，但超出了本文的范围——所以让我们假装一下。以下是提要:

```
<?php namespace Sitepoint\Feed;

	use figdice\Feed;

	class TwitterFeed extends Feed
	{

		public function run() {
			return array(
				array(
					'body' 			=> 'This is an example of a Tweet, though it isn\'t really a Tweet',				
				),
				array(
					'body' 			=> 'This is another example of a Tweet, though it isn\'t really a Tweet either',				
				),
				array(
					'body' 			=> 'Using REAL Tweets is left as an example, you see',				
				),
			);
		}
	}
```

为了简单起见，我们只返回推文的正文。

现在让我们创建一个模板，它从`TwitterFeed`类中“拉出”数据，并使用一个简单的无序列表来呈现它:

```
// file: twitter.html
	<?xml version="1.0" encoding="utf-8"?>
	<fig:template xmlns:fig="http://figdice.org/">

		<fig:feed class="TwitterFeed" target="tweets" />

		<ul>
			<li fig:walk="tweets" fig:text="body" />
		</ul>

	</fig:template>
```

`<fig:feed>`声明表明我们应该创建一个`TwitterFeed`的实例，并使用键`tweets`使其数据可用。

然后我们创建一个简单的无序列表。`<fig:walk>`的每次迭代都会创建一个`<li>`元素，因为它“遍历”了分配给`tweets`变量的数据。`fig:text`属性表示应该使用每条 Tweet 的`body`来填充`<li>`标签的文本内容。

结果如下:

```
<ul>
		<li>This is an example of a Tweet, though it isn't really a Tweet</li>
		<li>This is another example of a Tweet, though it isn't really a Tweet either</li>
		<li>Using REAL Tweets is left as an example, you see</li>
	</ul>
```

## 工厂

我们还没到那一步。为了使用饲料，你需要一个**工厂**。工厂必须实现`\figdice\FeedFactory`，这要求它包含一个负责实例化给定 feed 类的`create`方法。

这里有一个例子，非常简单明了:

```
<?php namespace Sitepoint\Factory;

	use Sitepoint\Feed\TwitterFeed;

	class FeedFactory implements \figdice\FeedFactory
	{
		/**
		 * In this simple example, we did not declare additional
		 * arguments for our factory. You can experiment by yourself.
		 */
		public function __construct() {
		}

		/**
		 * In this simple example, we did not pass attributes to
		 * the fig:feed invokation.
		 * Had we specify XML attributes (in addition to class and target),
		 * they would be interpreted by the Expression engine, and passed
		 * as the $attribute argument, as key/value pairs. 
		 * @see \figdice\FeedFactory::create()
		 */
		public function create($className, array $attributes) {

			if ($className == 'TwitterFeed') {			
				return new TwitterFeed();
			}

			// Explicitly return null when our Factory cannot handle
			// the requested class, so that more Factories can chain
			// the lookup.
			return null;

		} 
	}
```

通过这样做而不是做`return new $classname`，我们不需要用名称空间信息污染我们的视图代码。

因为我们使用存储在数组中的虚拟数据，所以这就是我们所需要的。然而，实际上您的工厂可能会做一些额外的初始化，比如注册数据库连接或传递一些配置参数。

最后，为了让它工作，您需要用您的视图注册它:

```
$view->registerFeedFactory(new FeedFactory());
```

如果你浏览应用程序的主页，你应该会看到一个 Tweets 列表。

## 摘要

我们的虚拟应用程序现在有了一个基本的结构，我们已经看到了通过实现一个简单的 Twitter 流来获取数据，尽管它使用了虚拟数据。

在下一部分中，我们将通过添加博客来构建我们的简单网站，这允许我们在 Figdice 中查看更多细节。我们还将提供一个语言选择器，检查 Figdice 的本地化特性。

## 分享这篇文章