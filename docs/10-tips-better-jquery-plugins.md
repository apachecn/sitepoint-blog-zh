# 开发更好的 jQuery 插件的 10 个技巧

> 原文：<https://www.sitepoint.com/10-tips-better-jquery-plugins/>

有一些很棒的 [jQuery 插件](http://www.jqueryplugins.com/)。更好的被全世界成千上万的 web 开发者所采用。不幸的是，更糟糕的失败了，消失在默默无闻中。这里有一些确保你的插件发挥全部潜力的技巧…

## 1.不要弄断链条

除非你的插件返回值，否则你的插件函数的最后一行必须是:

```
 return this; 
```

`this`确保方法调用可以链接，例如

```
 $("div#myid").yourPlugin().anotherPlugin().yetAnotherPlugin(); 
```

## 2.使其易于使用

在大多数情况下，你的插件应该简单地工作，而不需要开发者费力地阅读文档，设置选项或者编辑你的插件代码。

如果它是一个可视化的小部件，开发者不需要编辑任何 JavaScript。您可以简单地为 HTML 提供一个类/ID，它将自动启动您的代码，例如

```
 <section class="myjqWidget">
<p>My content</p>
</section> 
```

你的插件可以自己初始化，例如

```
 $(function() {
		$("section.myjqWidget").myjqWidget();
}); 
```

## 3.使用合适的命名和版本控制号

有很多 jQuery 插件。如果你正在考虑为你的标签处理插件命名为“标签”,很有可能它已经被使用过了。这可能并不总是重要的，但避免使用不明确或可能冲突的名称。

版本编号也很有用。当开发人员报告问题时，这变得尤其重要。

## 4.使用闭包

永远不要依赖引用 jQuery 的' $ '。如果开发人员安装了另一个库，它可能在 jQuery 加载之前就已经获取了这个库。解决这个问题最简单的方法是将 jQuery 作为匿名自启动函数的' $ '参数传递，例如

```
 (function($) {
	// code here can use $ to reference jQuery
})(jQuery); 
```

## 5.设置默认参数

大多数插件使用 JavaScript 对象文字符号来设置参数，例如

```
 $("#select").MyPlugin({opt1: 1, opt2: 2, opt3: "three"}); 
```

这有几个优点:参数易于阅读，可以以任何方式排序并完全省略。然而，你应该在你的插件代码中设置默认值，并相应地覆盖它们，例如

```
 $.fn.PlugIn = function(opts) {
	// default configuration
	var config = $.extend({}, {
		opt1: 1,
		opt2: 2,
		opt3: 3,
		opt4: 4,
		opt5: 5
	}, opts); 
```

然后你的插件可以使用代码引用参数，比如`config.opt1`。

## 6.支持 HTML 参数

理想情况下，HTML 小部件应该能够在开发人员不需要修改 JavaScript 代码的情况下设置参数。您可以考虑 HTML5 数据属性，例如

```
 <section class="myjqWidget" data-opt1="1" data-opt2="two">
<p>My content</p>
</section> 
```

这些可以通过 jQuery 的数据方法:`.data("opt1")`来访问。

## 7.记录您的代码

在你的插件顶部添加简洁的注释，描述:

*   插件名称和版本
*   插件做什么
*   示例用途
*   参数
*   联系和支持链接

如果特别复杂，可以考虑单独的自述文件。

## 8.彻底测试你的插件

测试一下。然后再测试一次。在所有浏览器中。

可能有些问题您没有准备好解决，例如 IE6 兼容性问题。这很好，但是要确保在你的文档中提到它。

## 9.使用好的模板

以下是我在创建新插件时使用的模板代码:

```
 /*!
 * jQuery plugin
 * What does it do
 */
(function($) {

	$.fn.PlugInName = function(opts) {

		// default configuration
		var config = $.extend({}, {
			opt1: null
		}, opts);

		// main function
		function DoSomething(e) {
		}

		// initialize every element
		this.each(function() {
			DoSomething($(this));
		});

		return this;
	};

	// start
	$(function() {
		$("#select").PlugInName();
	});

})(jQuery); 
```

它提供了一个很好的起点:

*   插件被包装在一个附件中。
*   它设置被插件参数覆盖的默认选项。
*   每个选中的元素都作为 jQuery 对象传递给 DoSomething 函数。
*   `return this;`包含在内。
*   结尾提供了自动启动代码。

## 10.把话传出去

如果你想让开发者使用你的插件，把它上传到诸如 [GitHub](http://github.com/) 、 [Google Code](http://code.google.com/) 和 jQuery 插件目录这样的库。创建演示页面，在文章中宣传它，并不停地在推特上谈论它。

然后准备好支持插件，必要时更新。你会收到愚蠢的问题和奇怪的功能请求，但这都是成为一个成功的插件作者的一部分。

对于有效的 jQuery 插件开发，你有什么高招吗？

## 分享这篇文章