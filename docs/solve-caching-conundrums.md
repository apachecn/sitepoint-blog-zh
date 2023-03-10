# 如何解决缓存难题

> 原文：<https://www.sitepoint.com/solve-caching-conundrums/>

没有缓存，网络就无法运行。在您和服务器之间，有一个浏览器和任意数量的缓存响应的代理服务器。其中大部分是由应用程序透明处理的，这大大减少了互联网流量。然而，如果你不小心的话，它也可能是奇怪的 web 应用程序古怪的原因…

## 设置标题

![headers](img/6c9d5084ef8c6a5d004d1053b5296fed.png)

缓存由 HTTP 状态代码和每个请求返回的`Last-Modified`、`Etag`和`Cache-Control`头控制。对于对同一 URL 的后续请求，浏览器/代理将:

1.  从自己的缓存中检索以前的数据
2.  要求服务器验证数据是否已更改，或者
3.  提出新的要求。

`Cache-Control`标题主要决定该动作。它最多可以设置三个逗号分隔的值:

**无存储**或**无缓存**
`no-store`停止浏览器和所有代理服务器缓存返回的数据。因此，每个请求都要返回服务器一次。

备选方案是`no-cache`。浏览器/代理将发出服务器请求，并传回报头中的`Last-Modified`(日期/时间)和/或`Etag`(响应散列/校验和)。这些会出现在后续请求中，如果响应没有改变，服务器会返回一个`304 Not Modified`状态，指示浏览器/代理使用自己的缓存数据。否则，新数据会以`200 OK`状态传回。

**public** 或 **private**
将`Cache-Control`设置为`public`意味着对每个人的响应都是一样的，数据可以缓存在浏览器或代理商店中。这是默认行为，所以没有必要设置。

`private`响应针对单个用户。例如，URL `https://myapp.com/messages`返回一组对每个登录用户都唯一的消息，即使他们使用相同的 URL。因此，浏览器可以缓存响应，但不允许代理服务器缓存。

**max-age**这指定了响应保持有效的最长时间(秒)。例如，`max-age=60`表示浏览器/代理可以在发出新请求之前缓存数据一分钟。

你的服务器、语言和框架经常控制这些设置，所以你很少需要修改——但是你可以。假设您想缓存单个用户对 Ajax 请求的 JSON 响应 30 秒。在 PHP 中:

```
header('Cache-Control: private,max-age=30');
echo json_encode($data);
```

或者 Node.js/Express 路由器:

```
res
    .set('Cache-Control', 'private,max-age=30')
    .json(data);
```

## 区分页面和 Ajax 数据 URL

设置 HTTP 头可能还不够，因为当您点击后退按钮时，浏览器的工作方式略有不同。

在 Firefox 和 Safari 中，点击**返回**将试图显示上一页的最后状态——假设 URL 已经用更新的`#hash`改变，或者通过用[历史 API 事件](https://www.sitepoint.com/javascript-history-pushstate/)拦截动作。

在 Chrome 和 Edge 中，按下 **back** 会显示初始状态下的前一页——尽管您的 JavaScript 会初始化，并可能在必要时更改 DOM。

实际上，使用哪种浏览器并不重要，但是有一些奇怪的情况。假设您的应用程序提供了一个分页的记录表，用户可以搜索并单击页面导航按钮。我们是优秀的开发人员，所以我们将使用渐进式改进来确保系统在所有浏览器中都能工作:

1.  用户在`http://myapp.com/list/`进入页面
2.  提交表单以更改过滤器或导航到新页面将会更改 URL 并提出新的请求—例如，`http://myapp.com/list/?search=bob&page=42`。该系统可以在任何有或没有 JavaScript 的浏览器中运行。
3.  我们引入了 JavaScript 增强功能，因此不需要刷新整个页面。该代码拦截表单提交和历史后退/前进事件，因此，尽管 URL 仍在变化，应用程序仍在后台执行 Ajax 请求。
4.  Ajax 请求调用相同的 URL，比如`http://myapp.com/list/?search=bob&page=42`——但是将`X-Requested-With` HTTP 头设置为`XMLHttpRequest`(由 jQuery 和所有优秀的 Ajax 库完成)。服务器识别这个头——因此，它返回 JSON 编码的记录数据，而不是返回整个页面 HTML。我们的 JavaScript 用这个来更新 DOM。

总之，我们的服务器可以为同一个 URL 返回 HTML 或 JSON，这取决于请求的`X-Requested-With`头的状态。不幸的是，这可能会导致 Chrome 和 Edge 出现问题，因为 HTML 或 JSON 都可能被缓存。

假设您随机浏览记录列表，并在`http://myapp.com/list/?search=bob&page=42`点按另一个(非列表)页面的链接，然后按浏览器后退按钮返回。Chrome 查看其缓存，看到该 URL 的 JSON 数据，并将其呈现给用户！点击 refresh 将会修复这个问题，因为将会发出一个没有`X-Requested-With`头的请求。更奇怪的是，Firefox 像预期的那样工作，并恢复了实际页面的状态。

解决方法:确保你的页面和数据 URL 永远不要相同。当导航到`http://myapp.com/list/?search=bob&page=42`时，Ajax 调用应该使用不同的 URL:它可以像`http://myapp.com/list/?search=bob&page=42&ajax=1`一样简单。这确保了 Chrome 可以分别缓存 HTML 和 JSON 请求，但 JSON 永远不会出现，因为 Ajax URL 永远不会出现在浏览器地址栏中。

*(注意:请不要用这个例子作为避免[渐进增强](https://www.sitepoint.com/javascript-dependency-backlash-myth-busting-progressive-enhancement/)的理由！它会影响依赖于 JavaScript 的单页面应用程序，尤其是那些提供外部 URL 链接的应用程序。)*

不幸的是，还有更复杂的情况…

## 当心自签名 SSL 证书

![secure](img/093a4030340a11e9039e4fbd99d7e180.png)

理想情况下，您的应用程序使用加密的 HTTPS 协议。然而，没有必要为您团队的所有 57 名成员购买 SSL 证书，因为您可以使用一个伪造的自签名证书，并在浏览器抱怨时点击**继续**。

请注意，当遇到假证书时，Chrome(可能还有大多数基于 Blink 的浏览器)会拒绝缓存页面数据。这类似于在每次请求时将`Cache-Control`设置为`no-store`。

您的测试站点将完全按照预期工作，并且您将永远不会遇到上述相同的页面/数据 URL 问题。从不使用缓存，所有请求都返回到服务器。具有真实 SSL 证书的实时服务器上的相同应用程序将缓存数据。您的用户可能会报告看到来自 Chrome 的奇怪的 JSON 响应— *，您将无法在本地再现这些响应*。

这些都是继续困扰 web 开发的噩梦般的挑战！我希望这个概述对您有所帮助。欢迎在评论中分享你自己的噩梦。我们都在一起…

## 分享这篇文章