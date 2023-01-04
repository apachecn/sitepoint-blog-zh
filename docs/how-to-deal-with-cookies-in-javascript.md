# 如何在 JavaScript 中处理 Cookies

> 原文：<https://www.sitepoint.com/how-to-deal-with-cookies-in-javascript/>

多年来，许多 web 开发人员需要在客户端存储数据。在 HTML5 及其新机制出现之前，每个开发人员都使用 *cookies* 来实现这个目标。不幸的是，在 JavaScript 中使用 cookies 会带来很多麻烦。本文讨论了什么是 cookies，以及如何构建函数来创建、检索和删除它们。


## 什么是饼干？

cookie 是从网站发送的一段数据，由用户的浏览器存储在本地。需要 Cookies 是因为 <abbr title="Hypertext Transfer Protocol">HTTP</abbr> 是*无状态的*。这意味着 HTTP 本身无法跟踪用户以前的活动。创建状态的一种方法是使用 cookies。

## 使用或不使用 cookie

今天，世界上大多数网站都使用 cookies。然而，cookies 是相当有限的，因为它们最多只能存储 4KB 的数据。此外，许多开发人员声称，由于 cookiess 随每个 HTTP 请求一起发送到服务器，大的 cookie 会消耗相当大的网络带宽，[损害性能](https://www.sitepoint.com/seven-mistakes-that-make-websites-slow/)。Remy Sharp 在合著的《T2》一书中对 cookies 提出了另一个重要的批评。这对移动连接来说可能是灾难性的。另一个要记住的要点是，如果你有欧洲访问者，那么你的网站必须遵守 2012 年 5 月 26 日起的欧盟电子隐私指令。如果你从未听说过这个，看看[为什么你的网站现在在欧洲是非法的](https://www.sitepoint.com/europe-website-cookie-privacy-law/)。

近年来，人们花了很多心思寻找饼干的替代品。由于 HTML5，一些新技术出现了。第一个是 [Web 存储 API](https://www.w3.org/TR/webstorage/) ，它有存储名称-值对的方法。对于 Web 存储 API 的概述，我建议你阅读[Web 存储 API 概述](https://www.sitepoint.com/an-overview-of-the-web-storage-api/)。第二种选择是 [Web SQL 数据库 API](http://dev.w3.org/html5/webdatabase/) ，它将数据存储在可以使用 SQL 变体查询的数据库中。虽然 Web SQL 得到了很好的支持，但这个标准不再被积极维护。最后，同样重要的是[索引数据库 API](http://dvcs.w3.org/hg/IndexedDB/raw-file/tip/Overview.html) ，它定义了一个包含简单值和层次对象的记录数据库。你可以在[中阅读更多关于 IndexedDB 的内容，并使用 HTML5 IndexedDB](https://www.sitepoint.com/up-close-and-personal-with-html5-indexeddb/) 进行个性化设置。不幸的是，IndexedDB [没有得到广泛的支持](http://caniuse.com/#feat=indexeddb)，所以你可能不应该依赖它。

不管你的偏好是什么，有几个要点需要理解。虽然所有这些 API 都提供了类似于 cookies 的本地存储，但是它们并不将数据发送回服务器。因此，在大多数情况下，您将同时使用 cookies 和一个存储 API。从技术上来说，使用 <abbr title="Asynchronous JavaScript and XML">AJAX</abbr> 可以达到同样的效果，但是这使任务变得复杂，并且需要额外的代码。

## 饼干是如何制作的

cookie 的结构非常简单。无非就是几个键值对。对由分号分隔，而等号字符将键与其值分隔开。一个 cookie 可以选择有一个过期日期，过期后它就会被删除。如果没有提供截止日期，cookie 将持续到会话或浏览器关闭。如果您设置了过去的到期日期，浏览器将删除 cookie。请注意日期的格式很重要，因为它必须是 UTC/GMT。此外，您可以指定一个可以读写 cookie 的域和路径。默认情况下，路径值为“/”，这意味着 cookie 对给定域中的所有路径都是可见的。如果不指定域，它将属于设置 cookie 的页面。设置这些数据的方式也很重要。顺序应该是:

`key-value;expiration_date;path;domain;`。

以下示例显示了一个可在域的所有路径中访问的 cookie，并且只有一个键-值对。

```
visits=3; path=/;

```

以下示例显示了一个在域的所有路径中都可以访问的 cookie(默认情况下)，该 cookie 将在 2012 年 10 月 31 日上午 11 点到期..

```
last-visit=Mon, 15 Oct 2012 19:36:00 GMT; expires=Wed, 31 Oct 2012 11:00:00 GMT;

```

## 编写 Cookies 脚本

到目前为止，我已经解释了什么是 cookies，以及它们的一些优点和缺点。现在是时候看看 JavaScript 公开哪些函数来使用它们了。不幸的是，我要说的第一件事是 JavaScript 没有本地方法来轻松地使用 cookies。JavaScript 可以使用`document.cookie`属性创建、检索和删除 cookies，但是使用起来并不愉快。每次你都被迫处理`split()`、`substring()`和`for`循环。请注意，虽然您可以将`document.cookie`视为一个字符串变量，但实际上它远不止如此。例如，采用以下脚本:

```
document.cookie = "visits=3; path=/;";
document.cookie = "last-visit=Mon, 15 Oct 2012 19:36:00 GMT; expires=Wed, 31 Oct 2012 11:00:00 GMT;";
```

如果您随后打印`document.cookie`，您将得到如下所示的意外结果:

```
console.log(document.cookie);
// print visits=3; last-visit=Mon, 15 Oct 2012 19:36:00
```

到目前为止，您已经看到了用 JavaScript 处理 cookies 的艰辛。所以，是时候创建我们自己的函数来轻松管理它们了。下面的函数将帮助你创建一个 cookie。请注意，expires 参数可以是一个`Date`对象的实例，也可以是一个表示天数的数字。后者可以是负数，将到期日期设置为过去。

```
function createCookie(name, value, expires, path, domain) {
  var cookie = name + "=" + escape(value) + ";";

  if (expires) {
    // If it's a date
    if(expires instanceof Date) {
      // If it isn't a valid date
      if (isNaN(expires.getTime()))
       expires = new Date();
    }
    else
      expires = new Date(new Date().getTime() + parseInt(expires) * 1000 * 60 * 60 * 24);

    cookie += "expires=" + expires.toGMTString() + ";";
  }

  if (path)
    cookie += "path=" + path + ";";
  if (domain)
    cookie += "domain=" + domain + ";";

  document.cookie = cookie;
}
```

您可以调用这个函数，如下所示。

```
createCookie("website", "audero.it", new Date(new Date().getTime() + 10000));
createCookie("author", "aurelio", 30);
```

既然已经设置了 cookie，就需要能够检索它们。您将使用一个给定的键和下面的`getCookie()`函数来完成它。如果找到了，它返回键值，否则返回`null`。

```
function getCookie(name) {
  var regexp = new RegExp("(?:^" + name + "|;\s*"+ name + ")=(.*?)(?:;|$)", "g");
  var result = regexp.exec(document.cookie);
  return (result === null) ? null : result[1];
}
```

使用`getCookie()`非常简单。您只需将密钥作为参数传递，如下所示。

```
console.log(getCookie("author")); // print aurelio
console.log(getCookie("nothing")); // print null
```

现在我们需要最后一个函数来删除一个 cookie。所示的函数非常简单，因为它依赖于`getCookie()`来测试给定的名称是否已设置，并依赖于`createCookie()`来设置过去的到期日期。

```
function deleteCookie(name, path, domain) {
  // If the cookie exists
  if (getCookie(name))
    createCookie(name, "", -1, path, domain);
}
```

给定该函数，要删除 cookie，您只需编写:

```
deleteCookie("author");
console.log(getCookie("author")); // now print null
```

使用所示的功能，您将能够轻松地管理 cookies。网络上还有很多其他的 cookie 处理函数。在你能找到的众多函数中，我想展示一下前端开发大师彼得-保罗·科赫(Peter-Paul Koch)在[quirksmode.com](http://www.quirksmode.org/js/cookies.html)上写的函数。我要感谢他允许我将它们包含在本文中。P.P.K 创建 cookie 的功能如下所示。

```
function createCookie(name,value,days) {
  if (days) {
    var date = new Date();
    date.setTime(date.getTime()+(days*24*60*60*1000));
    var expires = "; expires="+date.toGMTString();
  }
  else var expires = "";
  document.cookie = name+"="+value+expires+"; path=/";
}
```

类似地，要检索 cookie，使用下面的函数。

```
function readCookie(name) {
  var nameEQ = name + "=";
  var ca = document.cookie.split(';');
  for(var i=0;i < ca.length;i++) {
    var c = ca[i];
    while (c.charAt(0)==' ') c = c.substring(1,c.length);
    if (c.indexOf(nameEQ) == 0) return c.substring(nameEQ.length,c.length);
  }
  return null;
}
```

这是删除 cookie 的功能。

```
function eraseCookie(name) {
  createCookie(name,"",-1);
}
```

## 结论

通过这篇文章，你了解了什么是 cookies，它们是如何制作的，以及它们的优缺点。您还看到了如何使用自定义函数处理 cookies。正如我在上一篇文章中指出的，JavaScript 缺少一些基本的实用函数。幸运的是，你可以很容易地建立自己的或搜索网络来解决你的问题。

## 分享这篇文章