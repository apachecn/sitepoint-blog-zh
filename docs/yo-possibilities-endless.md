# Yo API:可能性是无限的

> 原文：<https://www.sitepoint.com/yo-possibilities-endless/>

最近，Yo 受到了相当多的关注。如果你之前没听说过 Yo，概念很简单。你下载应用程序，添加你的朋友，点击他们的用户名，向他们发送一个通知，说“哟”(完成声音效果)。就是这样。该应用被称为[无用的](http://gizmodo.com/colbert-eviscerates-yo-for-being-useless-and-dumb-1593703049)、[不安全的](http://www.androidheadlines.com/page/2)、[辉煌的](http://www.newyorker.com/online/blogs/culture/2014/06/a-yo-is-lovely-to-receive.html)、[不可思议的多才多艺的](http://www.theguardian.com/technology/2014/jul/02/yo-app-windows-phone-iftt)。

## API

无论你选择相信资金雄厚的、最近“流行”的应用程序，它们的 API 不仅仅允许你和朋友之间的单向交流。

[宣布 Yo API](https://medium.com/@YoAppStatus/yo-developers-api-e7f2f0ec5c3c) 的 Medium post 强调了一些示例用例。

> 每当有新文章发表，博客就能吸引读者。想象一下从 PRODUCTHUNT 得到一个 Yo。每当有新产品推出，网上商店就能吸引顾客。想象一下从 JENNASHOPIFY 得到一个 Yo。每当球队触地得分时，足球俱乐部就可以向球迷欢呼。想象一下从 49 人队得到一个 Yo。当冰淇淋车经过街角时，它可以载着孩子们。…想象一下从卡车上得到一枚硬币。

这个 API 非常简单。它有一个端点，[http://api.justyo.co/yoall/](http://api.justyo.co/yoall/)。向带有[你的 API 令牌](http://yoapi.justyo.co/)的 URL 发送一个 POST 请求将会向每个添加了你的应用程序用户名的人发送一个 Yo 通知。[Yo 团队已经在 GitHub 上发布了一个包含这个例子](https://github.com/YoApp/NFL/blob/master/yo-49ers.py)的 Python 演示。

下面是使用 jQuery 向 Yo 发送 POST 请求的方法:

```
function yoAll(apitokenvalue) {
  $.post('http://api.justyo.co/yoall/', { 'api_token': apitokenvalue });
}

yoAll(your API token); 
```

## 用 Yo 做更多事情

注册令牌时，Yo 会请求一个回调 URL。这使 Yo 能够在用户向您的应用程序用户名发送 Yo 通知时向您的站点发送 GET 请求。GET 请求将包含发送通知的人的用户名。只要您有用户名，您就可以使用中介将 Yo 通知连接到其他应用程序。

这将如何工作？假设你想让一个用户通过发送一个 Yo 通知到你的 Yo 应用用户名来登录你的应用(当然假设这是安全的)。要启用此功能，您需要用户向您提供他们的 Yo 用户名，并让他们将您的应用程序用户名添加到他们的 Yo 联系人列表中。

然后，您可以存储用户的 Yo 凭证，监听回调 url 上的 Yo 通知，并在用户的 Yo 凭证与收到的 Yo 通知匹配时让用户登录到您的应用程序。

IFTTT 使用这种方法将 Yo 连接到其他应用程序，并包含几个副本，使您可以关掉灯，调整恒温器，甚至通过向他们发送 Yo 来挽救你的糟糕约会！

这个概念很有趣，有多少应用和服务就有多少潜在用途。你会用 Yo API 做什么？你能在你的应用中看到它的位置吗？

## 分享这篇文章