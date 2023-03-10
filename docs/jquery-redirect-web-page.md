# jQuery 重定向网页

> 原文：<https://www.sitepoint.com/jquery-redirect-web-page/>

好的，由于某种原因，你想用 JavaScript 重定向一个网页。您可以使用 jQuery 或普通 JavaScript 来完成这项工作。如果需要即时重定向，可能是后者。

## 使用普通 JavaScript 重定向

jQuery 不是必需的，window.location.replace(…)最适合模拟 HTTP 重定向。

```
// simulates similar behavior as an HTTP redirect
window.location.replace("http://jquery4u.com");

// simulates similar behavior as clicking on a link
window.location.href = "http://jquery4u.com";
```

## 使用 jQuery 重定向

```
var url = "http://jquery4u.com";    
$(location).attr('href',url);
```

这比使用 window.location.href =要好，因为 replace()不会将原始页面放入会话历史中，这意味着用户不会陷入永无止境的后退按钮混乱中。如果你想模拟某人点击一个链接，使用 location.href(适用于所有浏览器)。如果您想模拟 HTTP 重定向，请使用 location.replace。

## 分享这篇文章