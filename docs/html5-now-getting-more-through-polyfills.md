# HTML5 Now:通过聚合填充获得更多

> 原文：<https://www.sitepoint.com/html5-now-getting-more-through-polyfills/>

对 HTML5 最顽固的误解之一是，它实际上在未来几年都是不可用的。有了这个神话和对 HTML5 新功能的碎片化浏览器支持的不确定性，很容易找到一些人现在避免 HTML5 的理由。

虽然有些功能还没有准备好投入生产，但 HTML5 中的许多新 API 已经足够强大，可以在 Chrome、Firefox 4 和 Internet Explorer 9 等新浏览器中使用。事实上，即使是 Internet Explorer 6 也支持 HTML5 的“新”特性之一，`contentEditable`。更重要的是，HTML5 中的许多新功能足够引人注目，以至于再等十年才开始使用它们没有意义。

## 跨浏览器支持

向 HTML5 跨越的最大问题是，我们大多数人别无选择，只能支持各种旧浏览器，这些浏览器很少或根本不支持最有用的新 API。一想到采用新的网络技术，就会想起跨浏览器不一致、不可维护的分支代码、浏览器嗅探和一系列其他问题的噩梦。然而，有一项未被充分重视的技术可以完全缓解 HTML5 某些新特性的问题，并且仍然允许您针对新 API 进行开发，就好像所有用户都在一夜之间升级了他们的浏览器一样:polyfills。

Polyfilling 是 Remy Sharp 创造的一个术语[,用来描述以复制缺失 API 的方式填充缺失功能的方法。使用这种技术，您可以编写特定于应用程序的代码，而不必担心每个用户的浏览器是否在本地实现它。事实上，polyfills 并不是一种新技术，也不依赖于 HTML5。多年来，我们一直使用 json2.js、ie7-js 等聚合填充和各种备用填充在 Internet Explorer 中提供透明的 PNG 支持。不同的是 HTML5 聚合填充在去年激增。](http://remysharp.com/2010/10/08/what-is-a-polyfill/)

## 聚合填料是由什么组成的？

关于我所说的具体例子，请看 json2.js。具体来说，这是其`JSON.parse`实现中的第一行代码:

```
if (typeof JSON.parse !== 'function') {
    // Crockford’s JavaScript implementation of JSON.parse 
}
```

通过用`typeof`测试保护代码，如果浏览器有`JSON.parse`的原生实现，json2.js 不会试图干扰或重新定义它。如果本地 API 不可用，json2.js 会以一种与 JavaScript 的本地 JSON API 完全兼容的方式实现一个 JavaScript 版本的`JSON.parse`。最终，这意味着您可以在一个页面上包含 json2.js，并且有信心使用`JSON.parse`，而不用考虑您的代码运行在哪个浏览器中。

这显示了 polyfill 方法的优势——不仅提供了一个兼容层，而且以一种试图精确反映 poly fill 实现的标准 API 的方式提供了兼容层。因此，任何特定于站点的代码都不需要知道或关心兼容性层的存在。最终，这会产生更干净、更简单、特定于应用程序的代码，让您可以利用新的 API，同时仍然保持与旧浏览器的兼容性。

## HTML5 的新语义元素

HTML5 中最容易填充的新特性之一是添加的语义元素集，例如`<article>`、`<aside>`、`<header >`和`<time>`。这些元素中的大部分都和古老的`<div>`和`<span>`完全一样，但是它们赋予了更丰富、更具体的语义。

因为这些元素是有效的 SGML，所以好消息是，即使像 Internet Explorer 6 这样的旧浏览器今天也会显示它们。然而，Internet Explorer 的一个怪癖是，它只将 CSS 样式应用于它识别的元素。因此，即使旧版本的 Internet Explorer 确实呈现了 HTML5 的新语义元素的内容，但它们在这样做时会忽略任何用户定义的样式。

幸运的是，Sjoerd Visscher 发现了一个简单的 Internet Explorer 变通方法，他的方法因 John Resig 而变得流行。使用指定为参数的任意元素类型调用`document.createElement()`会导致 Internet Explorer 识别该类型的元素，并按预期对它们正确应用 CSS 样式。

例如，在下面显示的文档的`<head>`中添加一个对`document.createElement(‘article’)`的调用可以驯服 Internet Explorer，并强制它将 CSS 样式应用于`<article>`元素。

```
<html>
    <head> 
        <title>HTML5 Now?</title> 
        <style> 
           article { margin: 0 auto; width: 960px; } 
        </style> 
        <script> 
            document.createElement(‘article’); 
        </script> 
    </head> 
    <body> 
        <article> 
            <!-- TODO: Write article… --> 
        </article> 
    </body> 
</html>
```

**这个对`document.createElement`的调用改变了 Internet Explorer 应用 CSS 样式的方式。**

当然，没有人想为 HTML5 中添加的大量新语义元素手动添加`createElement`语句。去除这种乏味正是 polyfill 的亮点。在这种情况下，有一个名为 [html5shim](http://code.google.com/p/html5shim/) (也称为 html5shiv)的 polyfill，它自动初始化 Internet Explorer 与新语义元素的兼容性。

例如，上面的代码可以重构为使用 html5shim，如下所示。

```
<html> 
    <head> 
        <title>HTML5 Now!</title> 
        <style> 
            article { margin: 0 auto; width: 960px; } 
        </style> 
        <!--[if lt IE 9]> 
        <script src="http://html5shim.googlecode.com/svn/trunk/html5.js"></script> 
        <![endif]--> 
    </head> 
    <body> 
        <article> 
            <!-- TODO: Write article… --> 
        </article> 
    </body> 
</html>
```

**使用 html5shim polyfill** 
注意 html5shim 脚本引用周围的条件注释。这确保了 polyfill 只能在版本 9 之前的 Internet Explorer 版本中加载和执行。在已经为新元素提供适当支持的浏览器中，下载、解析和执行这些代码不会浪费时间。

## 另一个可以考虑的选择

如果您对 HTML5 足够感兴趣并正在阅读本文，那么您可能已经知道或正在使用 Modernizr。然而，您可能不知道的一点是，Modernizr 内置了 html5shim `createElement`功能。如果您正在使用 Modernizr 进行特性检测，那么您已经对 HTML5 的语义元素有了向后兼容性。

## 持久客户端存储

多年来，我们别无选择，只能将特定于供应商的 DOM 扩展和专有插件组合在一起，以解决在浏览器中保持长期状态的问题。这些解决方案包括 Firefox 的`globalStorage`、Internet Explorer 的`userData`、cookies 以及 Flash 或 Google Gears 等插件。虽然可行，但这些拼凑起来的解决方案是乏味的，难以维护，并且容易出错。

为了弥补这一点，HTML5 中最受欢迎的新增功能之一是基于标准的 API，用于在浏览器中持久存储数据:`localStorage`。这个存储 API 提供了一致的客户端-服务器键/值存储，可以为用户访问的每个网站存储高达 5 MB 的独立数据。你可以把`localStorage`想象成一个巨大的 cookie，它更容易使用，并且在每个 HTTP 请求期间不会在浏览器和服务器之间来回传输。`localStorage`功能非常适合需要特定浏览器数据的任务，比如记忆偏好和本地缓存远程数据。

每个 A 级浏览器都支持`localStorage` 功能，包括 Internet Explorer 8，但大多数浏览器的旧版本都没有这个功能。与此同时，出现了几种解决方案，将跨浏览器存储填充到那些较旧的浏览器中。它们从雷米夏普的[存储 polyfiller](https://gist.github.com/350433) 的简单性，到 [store.js](https://github.com/marcuswestin/store.js) 和 [PersistJS](http://pablotron.org/?cid=1557) 提供的全面向后兼容性，到 LawnChair 的全功能 API 和 [AmplifyJS 存储模块](http://amplifyjs.com/api/store/)。

例如，这就是您如何使用 AmplifyJS 存储模块在用户的浏览器中保存一些数据，而不求助于 cookie——即使该用户使用的是 Internet Explorer 6:

```
// Sets a localStorage variable 'Name' with my name in it.
amplify.store('name', 'Dave Ward');

var website = {
        name: 'Encosia',
        url: 'http://encosia.com'
}

// The library takes care of serializing objects automatically.
amplify.store('website', website);
```

以后提取这些数据变得非常容易:

```
// The values we stored before could then be used at a later time, even
// during a different session.
var $personLink = $('<a>', {
        text: amplify.store('name'),
        href: amplify.store('website').url
});

$personLink.appendTo('body');
```

同样，使用`localStorage`或基于`localStorage`的 API 的好处是，这些数据不需要保存在 cookies 中，然后随每个 HTTP 请求一起传输，也不需要调用 Flash 这样的重量级插件来存储一点数据。数据存储在一个真正的、隔离的本地存储机制中，这使得它非常适合本地缓存数据或开发对脱机使用有丰富支持的站点。

## 用什么？

Remy Sharp 的 Storage polyfiller 是唯一一款真正称得上 polyfill 的产品，因为其他产品并没有完全模仿 HTML5 `localStorage` API。然而，store.js 和 AmplifyJS 存储模块提供了对更广泛的回退方法的支持，以实现在旧浏览器中的兼容性。实际上，这是很难忽视的。

## 地理定位

`Geolocation`是另一个适合聚合填充的 HTML5 特性。如果浏览器和操作系统都支持`geolocation`，并且运行在带有 GPS 传感器的设备上，HTML5 提供了对`geolocation` API 的访问，该 API 允许 JavaScript 代码确定您的页面是从哪里被访问的。

移动设备是基于浏览器的最令人印象深刻的例子。通过将内置的 GPS 硬件与支持 HTML5 `geolocation` API 的现代浏览器相结合，Android 和 iOS 设备都支持原生 HTML5 `geolocation`，其准确性不亚于其原生应用。

在那些最佳环境中访问`geolocation`数据所需的 JavaScript 就像这样简单:

```
navigator.geolocation.getCurrentPosition(function(position) {
    var lat = position.coords.latitude;
    var long = position.coords.longitude;

    console.log('Current location: ', lat, log);
});
```

这对于移动应用来说很好，但桌面硬件通常不包含 GPS 传感器。然而，我们都习惯了位置感知广告，这种广告在桌面硬件上跟随我们在互联网上的时间比地理定位 API 存在的时间长得多，所以显然可以在桌面浏览环境中解决 GPS 的缺乏。

在 JavaScript 中，当前的解决方法是在已知 IP 位置的数据库中查找访问者的 IP 地址。与使用 GPS 设备相比，这种方法的准确性明显较低，但是这些数据库通常能够在正确的区域内定位 IP 地址，这对于许多应用来说是足够有用的。

您可能知道更精确的无 GPS 地理定位技术，它不仅仅依赖于 IP 地址查找。最常见的是，这些增强的估计是通过将可见的 Wi-Fi 热点标识符与热点的那些特定组合在过去已经被物理定位的数据库进行比较的新颖方法来实现的。

不幸的是，浏览器中运行的 JavaScript 代码目前并不知道来自操作系统的数据。因此，在可预见的未来，基于 Wi-Fi 的技术对 polyfills 来说是不可用的，这让我们只能用 IP 查找作为唯一的选择。

Paul Irish 编写了一个简单的地理定位聚合填充程序，在旧的浏览器和缺少 GPS 传感器的硬件上提供了某种程度的。它通过使用谷歌的`geolocation` API 将访问者的 IP 地址翻译成大致的物理位置来实现这一点。这是一个真正的聚合填充，因为它将其`geolocation`功能插入到`navigator.geolocation`对象中，但前提是浏览器本身不提供`geolocation` API。

## 浏览器历史和导航

随着肤浅的 DHTML 效果让位于更结构化的客户端功能，如基于 AJAX 的分页和单页界面，这些结构性变化开始与浏览器的内置导航和历史功能不同步。然后，当用户凭直觉试图使用后退按钮导航到上一页或应用程序状态时，事情就不妙了。搜索“禁用后退按钮”揭示了这个问题困扰现代 Web 开发的程度。

操纵浏览器位置的“散列”部分有助于解决问题的一个方面。由于哈希最初是为了在同一页面内的导航点之间跳转，因此更改 URL 的哈希不会像更改底层 URL 前缀那样触发页面刷新。利用哈希的这一特性，客户端更新可以使浏览器显示的地址与 JavaScript 驱动的变化保持同步，而无需传统的导航事件。

## onhashchange 事件

虽然操纵浏览器的哈希得到了很好的支持，甚至可以追溯到 Internet Explorer 6 之外，但直到最近，监控哈希变化的标准化方法仍然难以捉摸。当前的浏览器支持一个`onhashchange`事件，当地址的散列部分改变时触发该事件——非常适合于检测用户何时试图使用浏览器的导航控件浏览客户端状态变化。不幸的是，`onhashchange`事件只在相对较新的浏览器中实现，支持始于 Internet Explorer 8 和 Firefox 3.6 版本。

虽然在旧浏览器中不能使用`onhashchange`事件，但是有一些库在旧浏览器中提供了一个抽象层。这些兼容性垫片使用特定于浏览器的怪癖来复制标准的`onhashchange`事件，甚至求助于每秒几次监控`location.hash` ，并在没有替代方法的情况下对浏览器中的变化做出反应。这方面的一个可靠选择是 Ben Alman 的 [jQuery Hashchange](http://benalman.com/projects/jquery-hashchange-plugin/) 插件，它是从他流行的 jQuery BBQ 插件中提取出来的。Alman 的 jQuery Hashchange 公开了一个`hashchange`事件，具有显著的深度跨浏览器兼容性。我不太愿意称之为 polyfill，因为它需要 jQuery，并且不完全复制原生 API，但是如果您已经在页面上使用 jQuery，它会非常有用。

## 超越 HashState

操纵散列是解决客户端状态管理问题的良好开端，但它也有缺点。劫持合法的浏览器导航功能并不是最佳选择，因为基于哈希的 URL 结构会给用户造成困惑，并与现有的页面导航相冲突。

一个更基本的问题是，浏览器不在 HTTP 请求中包含被请求 URL 的散列部分。如果无法访问 URL 的这一部分，就不可能立即返回与用户加书签、通过电子邮件接收或通过社交共享发现的页面处于相同状态的页面。这导致网站别无选择，只能以默认的初始状态显示页面，然后自动触发一个不和谐的过渡到用户真正想要的状态。要找到这对可用性的影响的证据，你只需要看看对 Twitter 和 Gawker Media 的“hash bang”重新设计的广泛负面反应。

## 输入推送状态

幸运的是，HTML5 还引入了一对更高级的 API，显著改善了客户端历史管理的情况。通常简称为`pushState`,`window.history.pushState`方法和 `window.onpopstate`事件的组合提供了一种异步操作浏览器地址的整个路径部分的方式，同样，也对散列之外的导航事件做出反应。

在 GitHub 上浏览一个项目的源代码是目前使用`pushState`的最佳实例之一。由于使用`pushState`操作浏览器的地址不会像传统的地址更改那样导致整个页面刷新，GitHub 能够在每个代码“页面”之间提供动画转换，同时仍然保留没有使用哈希或查询字符串的用户友好的 URL。

更好的是，如果您将书签保存到这些 URL 之一，并在以后直接导航到它，GitHub 能够在第一次请求时立即为您提供正确的内容，因为客户端 URL 结构与它们在服务器上使用的结构相匹配。正如我前面提到的，当您使用基于散列的 URL 时，这样做是不可能的，因为您的 Web 服务器从来不知道请求的散列部分。

## 在您自己的代码中使用 onhashchange 和 pushState

不幸的是，在不支持的浏览器中真正填充功能是不可能的。没有抽象层可以改变在旧浏览器中修改 URL 会触发页面加载的事实。然而，您可以通过在实现它的浏览器中使用`pushState`,然后在旧浏览器中使用基于散列的方法，从而两全其美。

Benjamin Lupton 组装了一个很棒的跨浏览器库，可以消除维护客户端历史时出现的各种奇怪和不一致的地方。他的图书馆涵盖了从 Internet Explorer 6 到最新版本 Chrome 的所有浏览器。使用这个库很简单。它的语法非常接近 HTML5 自己的`pushState`语法:

```
// This changes the URL to /state1 in HTML5 browsers, and changes it to
// /#/state1 in older browsers.
History.pushState(null, 'State 1', 'state1');

// Same as before, but /state2 and /#/state2.
History.pushState(null, 'State 2', 'state2');
```

history.js 没有公开 HTML5 popstate 事件的精确副本，而是包含了各种适配器来处理这些库中的事件系统。例如，使用 jQuery 适配器，您可以将事件处理程序绑定到 history.js `statechange`事件，如下所示:

```
History.Adapter.bind(window, 'statechange', function() {
    // Get the new history state from history.js.
    var state = History.getState();

    // Write the URL we’ve navigated to on the console.
    console.log(state.url);
});
```

每当浏览器浏览通过 history.js `pushState`方法保存的历史点时，就会触发这个`statechange`事件处理程序。无论是在原生支持`pushState`的 HTML5 浏览器中，还是在只支持基于哈希的 URL 更改的旧浏览器中，监视这一单个事件可以捕捉任何活动。

在现实世界的应用中实现这一点很容易。您可能会想到将它与 AJAX 支持的网格分页和排序结合使用，或者甚至用于整个站点的导航(例如 Gmail 或 Twitter ),而不需要求助于那些普遍讨厌的散列 URL 和重定向。

## 带着剪刀跑步

使用`pushState`时要注意的一件事是，你必须注意你的服务器会正确响应你在客户端使用的每个 URL。因为很容易构建一个客户端 URL，服务器会用 404 或 500 错误(例如， `/undefined`)来响应它，所以最好确保您的服务器端路由或 URL 重写被配置为尽可能优雅地处理意外的 URL。例如，如果您在`/report`有一个多页报告，每个页面都有`pushState`驱动的 URL`/report/2`、`/report/3`等等，您应该确保您的服务器端代码能够优雅地响应像`/report/undefined`这样的 URL 请求。

一个不太理想的选择是在你的`pushState`地址更新中使用`querystring` URL 片段，像`/report?page=2`和`/report?page=3`。产生的 URL 看起来不太好，但它们至少不太可能导致 404 错误。

## 从这里去哪里

本文只是触及了 HTML5 polyfills 生态系统的表面。有一些活跃的项目为 SVG 和画布图形、HTML5 视频、ECMAScript 5 甚至 WebWorkers 等功能提供跨浏览器支持。如果你有兴趣了解更多关于这些项目的信息，Paul Irish 在这里收集了一个非常棒的资源，其中包含对它们的简要描述和链接:[https://github . com/Modernizr/Modernizr/wiki/html 5-Cross-browser-poly fills](https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-browser-Polyfills)。

## 分享这篇文章