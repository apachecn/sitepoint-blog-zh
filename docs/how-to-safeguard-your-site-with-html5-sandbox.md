# 如何用 HTML5 沙盒保护你的网站

> 原文：<https://www.sitepoint.com/how-to-safeguard-your-site-with-html5-sandbox/>

今天的 web 应用程序是一种新体验的混搭。想想显示关于产品的最新推文的 Twitter 小部件。或者脸书评论讨论一篇文章。或者甚至只是通过一个`iframe`元素集成的网页。这些经历会增加网站的安全漏洞。

**不要紧张……有一个新的产品可以帮助你:HTML5 沙盒。**但在我开始之前，让我们快速回顾一下`iframe`元素问题。

## 一个黑盒子

嵌入带有`iframe`的内容就像在脸书公开宣布一个聚会。你认为你知道你邀请了谁，但实际上你不知道是谁传递的，谁会出现。

框定内容也是如此。你知道你所指的是什么，但是你不知道这个网站将来会如何发展。内容或功能(或两者)可以随时更改。在你不知情的情况下，也没有你的同意。

## 使用 Iframe 的安全性问题

浏览器处理使用`iframe`的页面就像处理任何其他网页一样。表单可用于检索用户输入，脚本可被执行，页面可在浏览器窗口中导航，浏览器插件可被执行。就像失控的派对不速之客一样，**你无法控制托管内容将会发生什么。**

默认情况下，有一种机制可以防止某些类型的攻击:跨域策略。

## 从另一个域重新托管内容

如果托管内容来自另一个域，则跨域策略开始起作用，它禁止“外来”内容访问父文档对象模型。

![](img/218d3357748c675f3650f5b9114dd540.png "Unique origin: iframe originating from xyz.com within an HTML5 page originating from mydomain.com")

因此，嵌入式页面无法读取托管域的 cookies 或浏览器的本地存储。但是仍然存在风险。

托管内容仍然可以在顶层重新导航。通过显示用户期望的内容，该站点可能会试图从用户那里骗取机密信息。或者，通过使用类似样式的表单，试图以这种方式恶意捕获用户信息。

这就是为什么即使有跨域策略，仍然存在很大的安全风险。

## 重新托管来自同一域的内容

从同一个域名重新托管内容的情况甚至更糟。

当内容来自同一个域时，没有默认的安全限制。嵌入的内容可以访问完整的浏览器 DOM 并操作一切。

同一个域上的内容应该是安全的，这有点道理。这里的风险主要源于在`iframe`中重新托管的用户生成的内容。

![](img/4c90476e1137d48c951d8da79883dea7.png "Same origin: iframe originating from mydomain.com within an HTML5 page originating from mydomain.com")

## 沙盒方法

这些有效的安全问题在很长一段时间内都没有得到标准机构的适当解决。如果没有一个清晰的 W3C 标准，就必须设法保护主机免受框架内容的影响。例如，微软在 Internet Explorer 8 中提供了`iframe`安全性的[专有实现](http://blogs.msdn.com/b/ie/archive/2008/01/18/using-frames-more-securely.aspx)。其他人拿起它，[讨论](https://bugzilla.mozilla.org/show_bug.cgi?id=341604)也把它作为他们浏览器的基线。但是自从 IE8 以来，标准已经非常成熟了。

现代浏览器包括 Chrome、Firefox、 [IE10 平台预览版](http://msdn.microsoft.com/en-us/ie/hh272905.aspx#_HTML5Sandbox)都是基于 [W3C iframe 沙箱属性](http://dev.w3.org/html5/spec-author-view/the-iframe-element.html#attr-iframe-sandbox)。

这是我们今天要用沙盒建立的东西。请看[这里的演示](http://kouder.nimg/demos/sandbox/index.html)。

![](img/bf8e0737bc5314dc5ec865f12f545045.png "HTML5 IFrame Sandbox Demo")

让我们从应用沙箱开始。只需将它作为空属性添加到`iframe`元素中:

```
<iframe sandbox src="http://somewebsite.com/default.html"></iframe>

```

就是这样！

现在，`iframe`沙盒内容在浏览器中重新托管，有以下限制:

*   **插件被禁用。**不会执行任何类型的 ActiveX、Flash 或 Silverlight 插件。
*   **表单被禁用。**托管内容不允许表单回发到任何目标。
*   **脚本被禁用。** JavaScript 被禁用，不会执行。
*   **到其他浏览上下文的链接被禁用。**以不同浏览器级别为目标的锚点标签不会执行。
*   **独特的原产地待遇。**所有内容都在唯一的来源下处理。内容无法遍历 DOM 或读取 cookie 信息。

这意味着，即使是来自同一域的内容也会受到跨域策略的处理，因为每个`iframe`内容都将被视为唯一的来源。

![](img/f01413e0b3c550ce150bcbed4cd2fb4d.png "Unique origin: iframe originating from mydomain.com with a sandbox attribute within an HTML5 page originating from mydomain.com")

**嵌入内容只允许显示信息。**在`iframe`内不能做任何其他可能危及托管网站或利用用户信任的行为。

## 检查沙盒属性

我们知道一个`iframe`是一扇开着的门。我们知道`sandbox`属性锁定了托管内容的安全性。**决定很明确:使用带有`sandbox`属性的`iframe`元素！**

您可以通过简单的 JavaScript 检查来确认浏览器是否支持`iframe` `sandbox`属性:

```
if("sandbox" in document.createElement("iframe")) {
  // render the iframe element ...
} else {
  // embed content through other ways,
  // as the browser does not support the sandbox
}

```

如果支持，就用`sandbox`属性。如果没有，尝试通过其他方式嵌入内容，或者通过信息鼓励用户升级到[现代浏览器](http://www.ietestdrive.com/)。

## 自定义沙盒

有些情况下，您需要对限制进行某种程度的定制，这是完全可能的。

几个属性值放宽了标准沙盒策略…

### 允许表单

如果希望在`iframe`元素中启用表单回发，只需为`sandbox`属性指定`allow-forms`值。

```
<iframe sandbox="allow-forms" src="xyz.html"></iframe>

```

如果该值存在，则允许嵌入的页面在框架内使用表单提交回发。

### 允许脚本

JavaScript 是一种强大的语言，通常用于在客户端进行动态交互，而无需向服务器重新发送信息。但是，当重新托管外国网页时，这种能力也带来了风险。

**因此，您应该仔细考虑是否真的想要在`iframe`场景中启用 JavaScript 尤其是当内容来自未知来源时。**

启用 JavaScript 是通过`allow-scripts`值完成的。

```
<iframe sandbox="allow-scripts" src="xyz.html"></iframe>

```

### 允许同源

默认情况下，来自同一域的`IFRAME`页面有可能访问父文档对象模型。

有了`sandbox`属性，页面将被视为不是来自同一个原点。此页面无法访问资源，即使来自同一个域。

要在沙盒场景中重新启用同源处理，您必须指定`allow-same-origin`属性。

```
<iframe sandbox="allow-same-origin" src="xyz.html"></iframe>

```

该值本身没有太大的帮助，因为您需要一些脚本功能来使用它。

例如，如果您想要访问当前域的本地存储，如下所示:

```
function loadFromStorage(key) {
  if(localStorage) {
    return localStorage.getItem(key);
  }
});

```

…您还需要`allow-scripts`值:

```
<iframe sandbox="allow-scripts allow-same-origin" src="xyz.html"></iframe>

```

现在 access 工作了！

但是要注意:允许多个脚本在同一个沙箱中会导致安全漏洞。例如，您的托管内容可以操作沙箱的属性并移除进一步的限制。

### 允许-顶部-导航

当您使用`sandbox`属性时，默认情况下，定位到其他浏览上下文的锚将被忽略并且不被执行。这可以保护托管`iframe`内容的网站不被托管内容所取代。

例如，此链接不会在默认沙箱中执行，因为目标将替换整个网页:

```
<a href="xyz.html" target="_top">Click me</a>

```

仅当您信任您托管的内容时，才建议放宽此策略。

```
<iframe sandbox="allow-top-navigation" src="xyz.html"></iframe>

```

### ms-允许弹出窗口

有时允许嵌入内容打开新的弹出窗口是有用的。一个完美的例子是像必应地图这样的地图服务。

嵌入 Bing 地图时，可以在弹出窗口中查找驾车路线或目的地详情等附加功能。但是由于`sandbox`禁止这样做，在 Internet Explorer 10 中有一个设置可以在不破坏沙盒的情况下启用弹出窗口。

以下代码显示了如何设置`ms-allow-popups`:

```
<iframe sandbox="ms-allow-popups" src="xyz.html"></iframe>

```

设置此值时，嵌入式网站能够在新窗口中显示信息。

```
<a href="xyz.html" target="_new">Show Info</a>

```

## 把所有的放在一起

您可以在一个沙箱中组合多个属性值。例如，如果您想启用表单回发、顶级导航和 JavaScript，只需指定:

```
<iframe sandbox="allow-forms allow-top-navigation allow-scripts" src="xyz.html"></iframe>

```

知道沙箱在分层情况下使用时的行为是正确的也很好，使用几个嵌套的具有不同`sandbox`属性值的`iframe`。最高层的`sandbox`总是统治着这个层级。

## 动手吧！

你可以在这个演示的[中摆弄 HTML `sandbox`。你可以从 GitHub 下载这个演示](http://kouder.net/storage/demos/sandbox/index.html)的[副本。要启用表单回发演示，只需在 WebMatrix 中打开项目文件夹，并从那里启动项目。](https://github.com/writeline/HTML5-Sandbox-Demo)

然后，下载一个现代的浏览器(比如 [Internet Explorer 10 平台预览版](http://ie.microsoft.com/testdrive/Default.html)，通过阅读 [IE 开发者指南](http://msdn.microsoft.com/de-de/ie/gg192966.aspx)来熟悉`sandbox`。**这一单一属性向更安全的网络迈出了一大步……现代浏览器终于准备好保护嵌入式内容了。**

## 分享这篇文章