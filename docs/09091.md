# jQuery 1.7 的新特性

> 原文：<https://www.sitepoint.com/jquery-17-whats-new/>

网络上最受欢迎的 JavaScript 库已经更新。jQuery 1.7 现在可以从 docs.jquery.com/Downloading_jQuery 下载。或者，如果您更喜欢 CDN，请使用以下任何 URL:

*   [https://code . jquery . com/jquery-1.7min . js](https://code.jquery.com/jquery-1.7.min.js)
*   [https://Ajax . Google APIs . com/Ajax/libs/jquery/1.7/jquery . min . js](https://ajax.googleapis.com/ajax/libs/jquery/1.7/jquery.min.js)
*   [http://Ajax . aspnet dn . com/Ajax/jquery/jquery-1.7min . js](http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.min.js)

那么，让我们来看看有什么新功能…

## 移除的功能

在我们看新的和闪亮的东西之前，你应该检查你现有的代码。您是否在使用:

*   event.layerX 或 event.layerY
*   jQuery.isNaN() *(未记载的实用函数)*
*   jQuery.event.proxy() *(未记录的方法)*

如果你是，jQuery 1.7 可能会破坏你的系统。

## 新的事件 API:on()和 off()

新的 on()和 off()方法统一了 jQuery 中的事件处理，因此不需要使用 bind()、delegate()或旧的 live()调用。语法:

```
 $(elements).on( events [, selector] [, data] , handler );
$(elements).off( [events] [, selector] [, handler] ); 
```

示例 1 —将点击事件绑定到链接:

```
 // onclick
$("a#mylink").on( "click", MyHandler );

// identical 
// to: $("a#mylink").bind( "click", MyHandler );
// or: $("a#mylink").click( MyHandler ); 
```

示例 2 —使用委托将点击事件绑定到无序列表中的所有项目:

```
 // onclick
$("ul#mylist").on( "click", "li", MyHandler );

// identical to: 
// $("ul#mylist").delegate( "li", "click", MyHandler ); 
```

现有的 bind()和 delegate()方法仍然存在，但是团队建议您对所有使用 jQuery 1.7 的新项目使用 on()和 off()。

## HTML5 支持 IE6、7 和 8

html()等方法现在支持 HTML5 标签，如`header`、`footer`和`article`。注意，在这些浏览器中你仍然需要 HTML5 shiv 。

## 新的 isNumeric()方法

正如您可能猜到的，如果值是一个数字，新的 [isNumeric()](http://api.jquery.com/jQuery.isNumeric/) 函数将返回 true，例如

```
 $.isNumeric(10);		// true
$.isNumeric("10");		// true
$.isNumeric(0);			// true
$.isNumeric("");		// false
$.isNumeric(true);		// false
$.isNumeric(Infinity);	// false 
```

## 新的回调()对象

jQuery。Callbacks() 对象返回一个用于管理回调列表的多用途对象。本质上，这是一种排队和触发一系列处理程序的通用方法。这是一个高级特性——在大多数情况下，更高级的 [jQuery。延迟对象](http://api.jquery.com/category/deferred-object/)将是首选。

## 杂项改进和错误修复

以下问题已得到解决:

*   在 1.7 中，代理事件的性能翻了一番。jQuery 团队发现委托在应用程序框架中变得越来越重要，并对核心代码做了进一步的优化。
*   使用 stop()终止排队效果后，包括 slideToggle()和 fadeToggle()在内的切换动画现在可以正常工作。
*   [is()方法](http://api.jquery.com/is/)现在可以正确地过滤位置选择器，比如:first、:last 和:甚至是针对文档——而不是传递给`is()`的 jQuery 对象。
*   [。removeData()方法](http://api.jquery.com/removeData/)现在接受数组或空格分隔的字符串中的一个或多个键。
*   [。stop()方法](http://api.jquery.com/stop/)可以被传递一个表示动画队列名称的可选字符串。
*   jQuery 现在支持[异步模块定义(AMD)](https://github.com/amdjs/amdjs-api/wiki/AMD) 来定义模块和依赖关系。

## 文档更新

有关所有新功能的更多详细信息，请参考 [1.7 文档页面](http://api.jquery.com/category/version/1.7/)。所有新特性都标有*“1.7 新特性”*。

## 该不该升级？

这是一个百万美元的问题。jQuery 团队努力减少兼容性问题，1.7 版不太可能破坏您代码。也就是说，如果你特别厌恶风险，推迟一两周更新。小错误通常会很快被发现、修复并发布。

## 分享这篇文章