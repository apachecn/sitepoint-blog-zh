# HTML5 Shim vs Internet Explorer 仿真模式

> 原文：<https://www.sitepoint.com/html5-shim-ie-emulation-mode/>

在这篇文章中，我将讨论优秀的 HTML 开发人员避免使用的技术。不是每个人都像 SitePoint 的读者一样尽职尽责；你可能遇到过这样的人，他们乐于接受快速解决方案，不管它会给未来带来什么样的痛苦。

当 IE7 发布时，它破坏了为 IE6 设计的网站。开发者依赖于 IE6 特有的技术、变通方法和黑客手段，这些在新版浏览器中都失败了。许多网站不得不被修复。开发人员理应受到指责，但 IE6 统治了多年，所以人们对这些变化感到恼火也就不足为奇了。微软受到了攻击，尽管他们只是在执行我们要求的标准。

为了避免 IE8 中类似的反弹，微软引入了文档兼容模式。如果你的站点在 IE7 中工作，但是在 IE8 中失败了，你可以在你的页面中添加下面的 meta 标签:

```
 <meta http-equiv="X-UA-Compatible" content="IE=EmulateIE7" /> 
```

或者发送一个 HTTP 头:

```
 X-UA-Compatible: IE=EmulateIE7 
```

IE8 将模仿 IE7 的行为，一切将神奇地再次工作。在撰写本文时，IE9 提供了至少八种文档兼容模式，包括有点奇怪的“IE5”设置，该设置使内容看起来好像是以类似于 IE5 的 IE7 古怪模式显示的。更多详情，请参考[在 MSDN](http://msdn.microsoft.com/en-us/library/cc288325%28v=vs.85%29.aspx) 定义文件兼容性。

文档兼容模式是 Internet Explorer 独有的。其他浏览器没有遭受同样的命运，因为:

*   IE 比它的大多数竞争对手存在的时间都长，并且是在 W3C 标准还处于婴儿期的时候发展起来的。其他供应商能够从一开始就采用一套更稳定的 HTML 特性。
*   微软向企业出售软件，并有长期合同承诺。其他供应商可以添加、修改或删除功能，而无需承担法律或财务后果。
*   与 Chrome、Firefox、Safari 和 Opera 的定期更新相比，微软两年的 IE 发布时间表不可避免地包含更多根本性的变化。

兼容模式旨在快速修复。如果你在 IE 发布后遇到问题，你可以在更新代码时暂时使用兼容模式。

会这样吗？不。环顾今天的网络，你会发现 IE = EmulateIE7 meta 标签无处不在。我甚至看到它在新发布的网站上使用——毕竟，在 IE7、8 和 9 中，针对一个渲染引擎比尝试跨浏览器开发更容易。

## 兼容模式和 IE9

使用 IE5、IE7、EmulateIE7、IE8 或 EmulateIE8 模式会导致 IE9 暂时失忆。浏览器忘记 HTML5。它失去了 CSS3 的效果，如圆角和框阴影。SVG 等功能消失。

但是，它也会导致意想不到的问题…

## iframes 和其他子对象

假设您已经创建了一个 HTML5 小部件。它可以在 IE7、8 和 9 中工作，因为您添加了 HTML5 垫片:

```
 <!--[if lt IE 9]>
<script src="http://html5shiv.googlecode.com/svn/trunk/html5.js"></script>
<![endif]--> 
```

另一个开发者现在使用 iframe 将您的小部件添加到他们的站点。不是最优雅的解决方案，但它快速简单。

问题:*如果父页面在 IE9 中打开但使用 IE=EmulateIE7，iframed 页面会使用什么渲染引擎？*

答:父页面和所有子对象——包括 iframe——将以 IE7 模式呈现。尽管您的小部件是 IE7/8 兼容的，但是您的小部件会因为以下原因而损坏:

1.  IE7、8 和 9 以 IE7 模式呈现 iframe，但是
2.  HTML5 填充程序只在 IE7 和 ie8 中加载。

结果是:IE7/8 工作正常，但是 IE9 失败了。

## 解决方案

给你的小部件的 HTML `head`添加一个兼容模式覆盖是很诱人的，例如

```
 <meta http-equiv="X-UA-Compatible" content="IE=edge" /> 
```

没用的。父页面具有总体控制权；它设置渲染模式一次，并保持这种方式，直到所有资源都已加载。

据我所知，只有一个修复方法——在 IE 的所有版本中加载 HTML5 shim:

```
 <!--[if IE]>
<script src="http://html5shiv.googlecode.com/svn/trunk/html5.js"></script>
<![endif]--> 
```

虽然 IE9 和所有将来的版本都不需要 shim，但是您不能保证您的小部件不会出现在设置了兼容模式的页面中。

呃——我觉得不干净。Web 开发是一件麻烦的事情。

## 分享这篇文章