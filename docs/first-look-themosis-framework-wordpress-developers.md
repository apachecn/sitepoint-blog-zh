# 首先看看 Themosis，一个面向 WordPress 开发者的框架

> 原文：<https://www.sitepoint.com/first-look-themosis-framework-wordpress-developers/>

我不喜欢 WordPress 不是秘密。我看不起它混乱的代码库，建议任何有技术知识的人不要使用它。但我不是为了抨击而抨击它，我抨击它是希望知道自己在做什么的人有一天会拿起接力棒，以更合理、更明智的速度继续这场受欢迎的竞赛。在我看来，任何试图解决 WP 日复一日带给我们的混乱局面的努力都是好的。

不久前，我们听说了一个名为 [Themosis](http://www.themosis.com/) 的新项目，这是一个“WordPress 开发者框架”。在这篇文章中，我们将尝试了解什么是 mosis，如何开始使用它，并且我们将看一看用它构建的一个示例项目——由 mosis 自己提供的[示例。](https://github.com/themosis/bookstore)

## 什么是月经

![](img/21efc81a4eeab2d1a2b6be1f0d4f66bc.png)

对于任何熟悉“框架”这个词的人来说，尤其是在 PHP 的上下文中，这可能听起来令人困惑。WP 不是已经塞满了我们不需要的东西吗？框架通常不是建立在一组极简的原则和分离的组件之上，而复杂的类似 WordPress 的应用程序建立在它们之上，而不是相反吗？嗯，是的。

众所周知，月球不是一个完整意义上的框架。相反，它是一个 API，在一定程度上绑定到 WP，使其更容易开发——但不一定使其更轻便。Themosis 是一套 API，你可以用它来创建现代 PHP 格式的 WordPress 组件，包括名称空间、类、匿名函数和编辑器支持。

从字面上看，mosis 是一个 MVC 驱动的类似 Laravel 的 WP 插件，它可以用来编写其他插件。它也有自己的路由器，所以你可以定义 Laravel 风格的路线，它自己的模板引擎——Scout——类似于我们在 Laravel 和 Phalcon 中使用的——还有一些额外的内置 WordPress 支持。例如，模板循环:

```
@loop(array('post_type' =&amp;gt; 'post', 'posts_per_page' =&amp;gt; -1))

    &amp;lt;h1&amp;gt;{{ Loop::title() }}&amp;lt;/h1&amp;gt;
    &amp;lt;div&amp;gt;
        {{ Loop::content() }}
    &amp;lt;/div&amp;gt;

@endloop
```

使用 WP API 来获得它需要的数据，而`Loop`类被设计成只与底层 WP 内容的循环一起使用。

### 安装

您可以通过使用 Composer 并安装 WP 命令行工具来安装 mosis。然后，当一个 Composer 项目为 mosis 创建时，最新版本的 WP 会随之而来，所以你所要做的就是运行 WP 的标准安装脚本。在那之后，你就可以开始发育了。

您可以按照他们的[安装说明](https://github.com/themosis/documentation/blob/master/installation.md)启动并运行第一个实例，尽管如果您首先在示例项目上进行试验可能是最好的(见下文)。

## 示例项目

Themosis 在他们的网站上提供了一个应用程序的例子——一个书店应用程序。

### 尝试一下

要试用它，你可以使用[这个快速提示](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)让一个家园改进箱启动并运行。然后只需注册一个新网站:

```
- map: bookstore.local
      to: /home/vagrant/Code/themosis_example
```

当然，不要忘记将`bookstore.local`别名添加到您的主机的`hosts`文件中，如[家园改进](https://www.sitepoint.com/quick-tip-get-homestead-vagrant-vm-running/)快速提示中所述。

使用`git clone https://github.com/themosis/bookstore themosis_example`克隆存储库，并在您的主机浏览器中访问`http://bookstore.local:8000`以获得安装过程。

浏览一遍，并遵循其余的说明[这里](https://github.com/themosis/bookstore#installation)。现在，您可以随心所欲地玩游戏，而不用担心会弄坏什么东西——如果出现问题，我们可以重建虚拟机并重复这一过程。

一旦按照说明安装完毕，你就可以试用这款应用了。

![](img/4ca25613d7d620d2adea571a7ddc00de.png)

在这一点上，这没有什么令人印象深刻的-任何 WP 网站都可以配置成这样的外观和工作方式。然而，我注意到两个关键点:

1.  我们让它运行得相当快，这是一个真正没有麻烦的工作。
2.  相当快。事实上，它的运行速度并没有我预期的那么慢，因为它需要额外的一两层才能到达底层的 WP API。

但是代码呢？那看起来像什么？

### 代码

你可以在书店的知识库上查看代码:[https://github.com/themosis/bookstore](https://github.com/themosis/bookstore)。让我们看看我们能辨别什么。就像我上面说的，Themosis 实际上是一个插件，所以我们需要在插件中寻找书店的代码:

![](img/8977c252faa2cb7cb3117236f30725a0.png)

打开它，我们可以找到三个主要的子文件夹。`app`包含了我们的基于 mosis 的 WP 层的逻辑，`src`包含了 mosis 本身的源代码(如果你浏览它们，你会注意到它们与其他更流行的框架的类名和文件夹结构有着惊人的相似之处)，我们都知道`vendor`——Composer-fetch 的所有东西都去的地方。

关于代码的实际质量，虽然它的某些方面肯定是现代的，符合今天的标准(名称空间、类),并且所有这些都用返回类型、参数和全部九个代码进行了漂亮的记录，但是也有一些原始的遗迹(我这样说是充分意识到我在吹毛求疵),比如用于防止直接访问 PHP 脚本的`<?php defined('DS') or die('No direct script access.');`行，或者在 PHP 文件的末尾关闭 PHP 标记(不必要的)。

然而，这并不是我们代码存在的唯一地方。mosis 被分割在两个位置——所有与路由和表示相关的东西都放在主题中，而所有与我们的应用程序逻辑相关的东西都放在插件的文件夹中。

![](img/7c8b69b684938e4f00fe2e98d3963043.png)

#### 手动音量调节

看 MVC 结构，可以说包装的很好。当然，如果它不是这样支离破碎(跨越两个父文件夹)就好了，但是结构就在那里。routes 文件夹在`app`文件夹中，就像在 Laravel 中一样，并且接受类似的路由语法——在引擎盖下，它只是一个增强的[条件标签](https://codex.wordpress.org/Conditional_Tags)。控制器就在那里，并以真正的 MVC 方式整齐地从它们的视图中分离出来，而模型是从控制器中使用的，它们的返回值被传递给视图。来自`home.controller.php`:

```
return View::make('pages.home')-&amp;gt;with(array(

			'promo' 	=&amp;gt; Books::getPromoBook($this-&amp;gt;page),
			'books' 	=&amp;gt; Books::getPopularBooks($this-&amp;gt;bookId),
			'news'		=&amp;gt; News::get(),
			'newspage'	=&amp;gt; get_page_by_path('news')

		));
```

很简单。视图以点符号遍历，以文件夹为单位，它们的后缀`scout`被忽略，但却是必需的，所以`pages.home`通向`app/views/pages/home.scout.php`——一切都很简单。

#### 带参数的路由

看一些路线，简单性是显而易见的。例如，让我们以搜索页面为例。这是一个非常简单的页面，它实际上只需要调用 WP 的底层搜索机制，所以不需要控制器:

```
// Search page
Route::is('search', function(){
	return View::make('search', array('search' =&amp;gt; $_GET['s']));
});
```

根视图`search`被呈现，搜索 GET 参数被传入(尽管它可能应该被过滤)，然后自动引用搜索函数返回的结果，并用上面提到的 Loop 类循环它们——这个循环是一个简化的、更容易接近的[循环](http://codex.wordpress.org/The_Loop)。

我喜欢这一点，不仅因为简单，还因为它抽象了 WP 的搜索功能，默认情况下，这个功能糟透了。这使得更换它更容易。

你可以在这里看到更多关于路由[的信息，或者只看例子。](https://github.com/themosis/documentation/blob/master/routing.md)

#### 用控制器+视图定义自定义页面

让我们看看创建一个带有视图控制器组合的定制页面是否像宣传的那样简单。

首先，我们将在`routes.php`中添加一条新路线:

```
Route::only('page', 'test', 'test@index');
```

然后，我们在`pages`下创建一个名为`test.scout.php`的新视图:

```
@include('header')

Hello World

@include('footer')
```

最后，我们在`controllers`文件夹中添加一个新的控制器:

```
&amp;lt;?php

class Test_Controller{

	/**
	 * Responsible of rendering the home page.
	 *
	 * @return object.
	*/
	public function index(){

		return View::make('pages.test');

	}

}
```

本质上，这将在任何框架中为`/test`路线呈现“hello world ”,但是由于 mosis 在后台依赖于 WP 的数据(毕竟它是一个用于扩展 WP 的 MVC API)，我们还需要做一件事。由于路由的工作方式，我们实际上需要将页面添加到系统中。

![](img/5de6fc1e6917e5d2e4fbcda7662190ee.png)

重新加载后，我们的 Hello World 消息呈现。

![](img/818fc845000dd7057b0f156c9c7a9574.png)

但是我们救了“有人在吗？”在内容中，不是 Hello World–Hello World 在视图中–给出了什么？为了在视图中包含页面的内容，我们需要访问控制器中的`$post`全局变量，并将它传递给视图，这样我们就可以使用它的属性。不幸的是，对于减轻 WP 因污染而闻名的问题，高温几乎无能为力。

如果我们改变我们的控制器来实际获取有问题的页面:

```
&amp;lt;?php

class Test_Controller{

	/**
	 * Responsible of rendering the home page.
	 *
	 * @return object.
	*/
	public function index(){

        global $post;

		return View::make('pages.test')-&amp;gt;with(array(
            'post' 	=&amp;gt; $post
        ));

	}

}
```

然后向视图添加必要的逻辑:

```
@include('header')

&amp;lt;h1&amp;gt;Page Content: {{ $post-&amp;gt;post_content;}} &amp;lt;/h1&amp;gt;

@include('footer')
```

…我们可以按预期呈现页面:

![](img/57fd74fb704c877235c93ffdacc70bce.png)

令人费解？可能有一点，但我想一个人会习惯的。这里重要的是，我们得到了强大的包装器来呈现所有通常可访问的 WP 内容，这为我们提供了新的结构和清晰度，给 WP 一些以前没有的刚性。

## 结论

把用 Themosis 开发看作是 Laravel 驱动的 WordPress 插件开发——如果 WP 的核心是没有希望的，那么插件就没有存在的必要了。mosis 为您可能已经习惯的混乱且不可维护的插件架构带来了急需的结构和封装。

mosis 仍处于婴儿期，目前是 0.8，但 1.0 版本即将到来，我们希望开发者在这一努力中好运和成功——好的设计实践需要像疫苗一样在 PHP 世界传播，如果一种方法是通过在不好的实践核心上的好的实践驱动的插件框架，那就这样吧。

## 分享这篇文章