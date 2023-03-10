# 使用 WURFL.js 对应用程序进行设备检测

> 原文：<https://www.sitepoint.com/device-detection-apps-wurfl-js/>

响应式 web 设计对于移动开发者来说并不是一个新话题，我们中的许多人都非常熟悉这个过程和最佳实践指南。其中一个最佳实践是根据用户的浏览器或设备能力以不同的方式显示页面元素。为了实现这一点，设计者传统上使用嵌入在 HTTP 头中的`User-Agent`字符串。这些通常只会告诉你使用的设备是 iPhone、Android 还是台式电脑，没有其他信息。

在很多情况下，开发人员需要知道比这更多的细节来创建一个完全响应的设计。这就是 WURFL.js(无线通用资源文件)可以帮忙的地方。

# WURFL.js 是什么？

WURFL.js 是一个不断增长的专有 API 集列表和一个 XML 配置文件，其中包含有关各种移动设备的设备功能和特性的信息。这为开发者提供了关于他们的客户用来访问他们的网站的设备类型的有价值的信息。它利用了您可能熟悉的相同的服务器端检测功能(使用服务器端语言),而无需您进行任何设置。

WURFL.js 背后的目的是使开发人员能够优化内容的呈现并提供更好的用户体验，同时也让开发人员了解用户的行为和使用模式。它试图以一种快速、容易实现且无成本的方式做到这一点。

如何使用 WURFL.js？让我们把手弄脏吧。

首先将这行 Javascript 代码添加到页面中:

```
<script type='text/javascript' src="//wurfl.io/wurfl.js"></script>
```

这里，我们导入了一个 JavaScript 文件，使我们能够用 JavaScript 访问 WURFL 对象。例如:

```
{ complete_device_name:"Google Nexus 4", form_factor:"Smartphone", is_mobile:true }
```

我在这里建立了一个示例页面，因此您可以看到 WURFL.js 的运行情况。源代码可以在 github [这里](https://github.com/sitepoint-editors/WURFL)找到。

请注意，只使用了三个属性:

*   其中给出了访问您网站的设备的品牌和型号。(但是，如果设备是台式计算机，它会给你一个通用的“台式计算机”响应)。

*   可以是桌面、应用程序、平板电脑、智能手机、功能手机、其他手机等任何一种。

*   `is_mobile`是一个布尔值，根据设备的不同可以为真或假。如果是手机或平板电脑，它将返回 true。

这些属性使您能够执行以下操作:

```
if(!WURFL.is_mobile) {

  $('#wurfl .is-mobile span').html("false");

}
```

## 广告

WURFL.js 可以帮助你有策略地展示广告。一些广告在移动设备上看起来更好，而另一些则更适合台式机或笔记本电脑。WURFL.js 可以帮助您控制广告如何显示以及在何种设备上显示。您可以通过使用`WURFL.is_mobile`功能来控制广告，或者如果您想要更多的控制，可以使用`form_factor`选项并根据设备类型来定向广告。

## WURFL.js 和 Modernizr

尽管 WURFL.js 所提供的与 Modernizr 所能实现的相似，但它们并不相同。Modernizr 提供了关于 web 浏览器功能的信息，而 WURFL.js 提供了关于硬件的见解。有些情况下，知道设备的品牌和型号比只知道设备的浏览器功能更好，因为虽然浏览器可能支持某个功能，但设备可能不支持。这并不意味着你应该完全摆脱 Modernizr。结合使用 WURFL.js 和 Modernizr，你可以更好地控制你的网站在任何设备上的工作和外观。一个很好的例子是 Modernizr 的 CSS 功能，它使您能够根据设备使用不同的样式。这个特性在 WURFL.js 中也可以使用，只需要一行 Javascript 代码就可以实现:

`document.documentElement.className += ' ' + (WURFL.is_mobile ? '' : 'no-') + "mobile";`

在这个简单的例子中，当设备移动时，`HTML`标签将被分配`is_mobile`类，否则该类将等于`no-is_mobile`。通过包含它，您可以根据指定的类创建不同的样式表。

## 结论

我知道获取访问您的应用程序的不同设备的信息有多困难，并且在此之前使用过设备描述库，这需要服务器端库。对我来说，WURFL.js 是一个伟大而简单的资源，尤其是免费的。

它可以让开发者控制他们的内容如何在众多设备上呈现，更好地定位广告，并更好地分析用户访问他们网站的方式。你对 WURFL 有什么经验吗？

## 分享这篇文章