# 如何为 Web 应用程序构建推送通知

> 原文：<https://www.sitepoint.com/how-to-use-push-notifications-for-web-applications/>

*本文最初发表于 [Sencha](https://www.sencha.com/blog/using-push-notifications-for-web-applications/) 。感谢您对使 SitePoint 成为可能的合作伙伴的支持。*

渐进式 Web 应用程序已经成为本地应用程序的一种可行的替代方案:它们可以被安装，用于脱机工作，并且可以访问设备硬件。在本文中，我将向您展示如何通过添加类似本机的通知来使您现有的 web 应用程序比以前更好。我将解释 web 推送通知的概念，并向您展示如何将它们添加到您现有的 Web 应用程序中。最后，我将介绍规范和浏览器支持的状态。

## 网络推送通知协议

Web 推送通知协议相对较新。它使 web 应用程序能够充当本机应用程序，并随时接收从服务器推送给它们的消息，即使 web 应用程序不活动或当前未在浏览器中加载。这可以让你在用户不使用你的应用程序的时候用紧急和相关的通知吸引他们，并促使他们回到应用程序。

![Web Push Message](img/91f19c3010829a57710a2cd386aaf6f4.png)

它明确了在您的 web 应用程序中实现 Web 推送通知的商业价值。它增加了用户的参与度。您的应用程序的整体价值也增加了，因为推送通知使您的应用程序对您的用户更有用。这提高了 web 应用程序的可用性，并使我们更接近于为所有平台开发一个 web 应用程序，而不是为每个平台开发一个本地应用程序。

## Web 推送与 Web 套接字

在详细介绍这项技术之前，我想先谈谈 Web 推送和 Web 套接字之间的区别。首先，这是他们的共同点。Web Push 和 Web Sockets 旨在实现 Web 应用程序和应用程序服务器之间的实时通信，并将实时数据和更新从应用程序服务器发送到您的 Web 应用程序。

以下是不同之处:

*   Web 套接字只能在网页加载并处于活动状态时使用。但 Web 推送通知则相反，它可以在任何时候使用，包括应用程序活动、不活动或未加载时，以及浏览器不活动甚至关闭时。
*   使用网络推送发送的数据必须加密，并且每条消息有大小限制(不得大于 4Kb)。对于允许发送的邮件数量也有一个计数限制(确切的限制值取决于浏览器)。一些浏览器(例如 Chrome)可能还要求每次收到消息时向用户显示一条通知。当您使用 Web 套接字时，您没有这些限制:您可以发送任意数量、任意大小的未加密消息，并按照您的意愿处理它们；您可能会显示一个通知，或者悄悄地更新应用程序中的数据，甚至什么也不做。
*   一般规则是，当用户与应用程序交互时，使用 web 套接字向您的 Web 应用程序发送普通数据更新。使用 Web 推送通知向用户发送必须立即接收的紧急和重要消息，无论用户当时是否正在使用您的应用程序。

## 技术概念

让我们转到技术的技术细节。我想用一个有特殊规则、玩家和回合的游戏来解释细节。我先从描述游戏的玩家开始。在这个名为 Web 推送通知的游戏中有 5 个参与者:

*   网络应用
*   服务行业人员
*   浏览器
*   应用服务器
*   推送服务器

推送服务器是由浏览器供应商实现的服务；它是应用服务器和浏览器之间的沟通桥梁。它负责将消息从应用服务器传递到浏览器。

## 使用游戏演示 Web 推送通知

通过一个游戏，我将演示如何在应用程序中添加 web 推送通知。这个游戏的规则由万维网联盟和因特网工程任务组提供的几个规范来定义:

*   浏览器和 web 应用程序或与该应用程序相关联的服务工作者之间的通信在 Push API 规范中进行了描述。

*   通知 API 规范中描述了显示不同类型的通知以及通知处理。

*   应用服务器和推送服务器之间的通信在 Web 推送协议规范中定义。

*   还有描述推送消息加密和应用服务器标识的附加规范，让您的应用服务器证明它被允许向您的用户发送消息。

![Push API](img/56bde29c69b45220dc8af40dffde688b.png)

## 游戏回合

我将游戏分成了四轮，并将解释每一轮的概念和目标。然后，我将向您展示如何在应用程序中实现每一轮。

## 第一轮:服务人员注册

Web 推送通知需要服务人员来处理推送消息，因此第一轮将是注册您的服务人员。这一轮只涉及你的 web 应用和浏览器。这一轮发生在页面加载时。

web 应用程序向浏览器发送注册服务工作器的请求，如果服务工作器注册成功，浏览器将使用 ServiceWorkerRegistration 对象进行回复。

![Register](img/a9839f5f8d16a3216eb3ea129bedabba.png)

要实现这一轮，您需要将以下代码添加到 web 应用程序中:

```
if (&#39;serviceWorker&#39; in navigator) {

  if (&#39;PushManager&#39; in window) {

    navigator.serviceWorker.register(&#39;ServiceWorker.js&#39;).then(function(registration) {

      //state initializing

    })

    .catch(function() {

      //error handling

    });

  } else {

    //error handling

  }

} else {

  //error handling

} 
```

首先，我们需要检查浏览器是否支持服务人员。然后，我们需要检查浏览器是否支持 web 推送通知。随着浏览器支持的增长，添加这两种检查总是一个好主意。

如果两者都支持，我们就注册我们的服务人员。对于这一步，我们调用 navigator . Service Worker . register()方法，并将路径作为参数传递给我们的服务工作者文件。在这个步骤之后，浏览器将下载这个文件并在服务人员环境中运行它。服务工作者文件是一个标准的 JavaScript 文件，但是浏览器将“允许它访问”服务工作者 API，包括 push。

如果一切运行正常并且没有错误，那么 register()返回的承诺将被解析。如果有任何类型的错误，承诺将被拒绝，我们需要处理这种情况以及浏览器不支持服务人员的情况。当 register()进行解析时，它返回一个 ServiceWorkerRegistration 对象，该对象将在下一轮中使用。

## 第二轮:订阅

第二轮处理 Web 推送通知的订阅。这是当你问用户他是否想从你的应用程序接收 Web 推送通知，如果他同意，你就订阅他。这一轮涉及更多的参与者——Web 应用、浏览器、应用服务器和推送服务器以及更多的通信。

这一轮应该在用户准备订阅时进行。只有当你确定这对他是相关的和有意义的时候，你才应该要求他订阅，因为你只有一次机会去要求。如果用户阻止了这个请求，那么浏览器就不允许你以后再问这个问题。解除阻止应用程序的唯一方法是在浏览器设置中进行更改。可以想象，几乎没有用户这样做。所以，你需要向用户解释他会收到什么样的通知，以及为什么。您还可以使用定制的页面内消息为用户订阅，一旦用户单击 yes，就向他显示系统浏览器请求。

这一轮会发生什么？您的 web 应用程序要求浏览器为用户订阅接收 Web 推送通知。如果用户同意，浏览器向推送服务器发送请求以生成推送订阅。推送服务器用推送订阅对象回复浏览器，浏览器将该对象转发给 web 应用程序。最后一步，您的 web 应用程序通过 Ajax 调用将这个推送订阅对象发送到您的应用服务器。您的应用服务器保存它以供下一轮使用。

![](img/c0c8f86c7c48edb84b2fe059d4f7c66c.png)

您的 web 应用程序是这一轮的主角，因此应该将以下代码添加到您的 web 应用程序中:

```
navigator.serviceWorker.ready.then(function(registration) {

  registration.pushManager.subscribe({

    userVisibleOnly: true,

    applicationServerKey: urlBase64ToUint8Array(&#39;...&#39;)

  })

  .then(function(subscription) {

    // The subscription was successful

    savePushSubscription(subscription);

  })

  .catch(function(e) {

    //error handling

  });

}); 
```

一旦服务工作者准备好了，我们就可以通过调用在前一轮中接收到的服务工作者注册对象的 registration . pushmanager . subscribe()方法来订阅用户。浏览器请求允许向用户显示通知。如果用户允许，承诺将通过订阅对象来解决。订阅对象包含向用户发送推送通知的必要信息。现在，您需要将这个对象保存在您的应用服务器上。

我不会深入这个过程——它可能只是一个对服务器的标准 Ajax 调用，或者您可以将这个对象作为 JSON 或字符串发送，然后服务器需要将这个对象保存在数据库中。我不会展示这段代码，因为这取决于你的应用服务器是如何实现的。如果用户拒绝许可或发生任何其他错误，那么承诺将被拒绝，您需要处理这个问题。

## 许可请求

如果您的 web 应用程序在调用 subscribe()时没有显示通知的权限，浏览器将为您请求权限。但是还有另一个选项——您可以通过直接调用 Notification.requestPermission 方法来手动请求权限。该方法将返回一个承诺，该承诺根据用户选择的权限进行解析。可能的值为 granted、denied 或 default。

```
Notification.requestPermission(function(result) {

  if (result!== &#39;granted&#39;) {

    //handle permissions deny

  }

}); 
```

## 订阅参数

让我们看一下传递给 subscribe 方法的参数对象:

```
reg.pushManagerpushManager.subscribe({

  userVisibleOnly: true,

  applicationServerKey: new Uint8Array([...])

}); 
```

*   userVisibleOnly:如果设置为 true，意味着每条推送消息都会向用户显示一条通知。在这种情况下，当用户没有得到通知时，不允许在后台发送静默推送消息。目前，Chrome 只允许这个选项设置为真，因为理论上静默推送可能被开发者用来做一些讨厌的事情，比如设置隐藏的地理跟踪。Firefox 支持有限数量的静默推送消息。关于此规则的规范可能会有变化。
*   applicationServerKey:它是标识应用服务器的公钥/私钥对的公共部分。浏览器将把这个应用服务器密钥发送到推送服务器，并且推送服务器将使用它来识别订阅用户的应用与正在向该用户发送消息的应用是同一应用。我将在下一轮详细解释识别过程。

## 订阅对象

让我们看看通过解析承诺返回的 PushSubscription 对象。这个 PushSubscription 对象与我们在前一轮中注册的服务工作者相关联。该推送订阅描述了推送消息的传递桥。

```
interface PushSubscription {

  readonly attribute endpoint;

  // &quot;https://{push\_server\_url}/{user\_identifier}&quot;,

  function getKey();

  //auth - authentication secret

  //p256dh - key for encrypting messages

}; 
```

它包含两个重要部分:

*   端点是一个推送服务器 URL，后跟一个唯一的用户标识符。应用服务器使用这个 URL 向推送服务器发送消息。
*   getKey 方法返回两个密钥:auth 是由浏览器生成的认证秘密，p256dh 是可用于加密推送消息的公共 P-256 ECDH 密钥。

应用服务器将使用这两个密钥向服务人员发送消息数据。端点定义将推送消息发送到哪里，密钥定义如何加密它们。您需要将这两个值发送到您的应用服务器，如何发送它们完全取决于您。

## 第 3 轮:推送消息

第三轮是发送推送消息。这一轮发生在用户需要知道的紧急和相关的事情发生时。例如，如果用户的航班延误，或者他在聊天中收到了新消息。

这一轮会玩游戏的哪些部分？应用服务器向推送服务器发送推送消息。当浏览器在线时，推送服务器将该消息转发给浏览器。浏览器唤醒与用于发送该消息的推送订阅相关联的服务工作者。服务人员处理该消息并向用户显示通知。

![](img/11175413f6eee157b4afcffb73824512.png)

这一轮的第一步由应用服务器完成。应用服务器通过使用来自端点的推送服务器 URL 和用户标识符向推送服务器发送 HTTP POST 请求来请求推送消息的传递，所述端点是在前一轮中从 web 应用接收的。加密推送消息的内容包含在请求的主体中。该请求必须使用 HTTPS 协议。该协议为订阅和推送消息提供来自外部方的机密性和完整性保护。

当推送服务器接收到该请求时，它会回复一个响应，指示推送消息已被接受，并对该消息进行排队。该消息将保持排队状态，直到浏览器上线，推送服务器发送该消息。

## 发布请求

```
POST /{user\_identifier} HTTP/1.1

Host: {push\_server\_url}

TTL: 15

Content-Type: text/plain;charset=utf8

Content-Length: 36

{encrypted\_message} 
```

应用服务器可以向该请求添加一些额外的报头，以告诉推送服务器如何处理该消息:

*   prefere:应用服务器可以在 prefere 头字段中包含“respond-async”首选项，以便在浏览器发送和确认推送消息时请求推送服务器的确认。

首选:响应-异步

*   生存时间:推送服务器可以通过将推送消息存储一段时间来提高推送消息传递的可靠性。某些推送消息一旦过了某个时间段就没用了。所以这样的信息根本不应该被传递。这就是为什么应用服务器必须在推送消息传递的请求中包含 TTL(生存时间)报头字段。TTL 报头字段包含以秒为单位的值，该值建议推送服务器应该将推送消息排队多长时间。一旦 TTL 周期结束，推送服务器不得试图将推送消息传送给浏览器。

TTL: 15

*   紧急性:对于电池供电的设备，避免消耗资源来接收琐碎的消息通常是至关重要的，因此如果应用服务器可以定义消息的紧急性，并且如果浏览器可以请求只发送特定紧急性的消息，这将是很有帮助的。应用服务器可以在推送消息传递的请求中包括消息紧急度。可能的值是“非常低”/“低”/“正常”/“高”。

紧急程度:高

*   主题:已经被推送服务器排队的推送消息可以被新的内容替换。如果在发送推送消息期间浏览器离线，更新推送消息可以避免向浏览器发送过时的消息。只能替换已分配主题的推送消息。具有主题的推送消息用相同的主题替换任何未完成的推送消息。

主题:更新

## 消息加密

推送消息可能根本不包括任何数据。在这种情况下，POST 请求将有一个空的主体，服务工作者可能需要在向用户显示通知之前获取一些必要的数据。或者，应用程序服务器可以随推送消息一起发送一些称为有效负载的数据，这样服务人员就可以避免发出额外的请求。但是，所有有效载荷数据都必须加密。这是很重要的一点。HTTPS 提供了浏览器和应用服务器之间的安全通信，因为您信任您的应用服务器。

但是，浏览器会选择使用哪个推送服务器来传送数据，因此作为应用程序开发人员的您无法控制它。HTTPS 只能保证没有人可以阅读或修改传输到推送服务器的消息。但一旦推送服务器收到，理论上可以将数据重新传输给第三方，或者进行修改。为了防止这个问题，您必须加密数据，以确保推送服务器不能读取或修改有效载荷数据。

您必须在 P-256 曲线上使用椭圆曲线 Diffie-Hellman (ECDH)来加密推送消息。浏览器生成一个 ECDH 密钥对和身份验证机密，并将其与创建的每个订阅相关联。ECDH 公钥和认证秘密与推送订阅的其他细节一起被发送到应用服务器，并用于加密消息数据。

我强烈建议您使用一个库来加密有效负载数据，不要手动实现它。虽然您可以在 Web Push 协议中找到加密过程的详细描述，但是标准可能会改变，然后您需要调整和验证您的实现。

这里是 Google 为 Node.js 开发的[加密库。也有其他语言的库。](https://github.com/web-push-libs/web-push)

## 应用服务器的自愿识别

这就是我们在前一轮中发送给 subscribe 方法的 applicationServerKey 发挥作用的地方。应用服务器需要证明它被允许向用户发送通知，并且推送服务器需要验证这个步骤。

为此，应用服务器添加了一个授权头，其中包含用私钥签名的信息，以及在创建 PushSubscription 时与推送服务器共享的公钥 applicationServerKey。当推送服务器接收到该请求时，它验证该签名，检查该请求是否使用私钥进行了签名，该私钥与接收请求**的推送订阅相关联。**如果签名有效，则推送服务器知道这是来自授权应用服务器的有效推送消息。它提供安全保护，防止任何人向应用程序的用户发送消息。

```
POST /{user\_identifier} HTTP/1.1

Host: {push\_server\_url}

TTL: 15

Content-Length: 136

Authorization: Bearer

eyJ0eXAi...

Crypto-Key: p256ecdsa=BA1Hxzy...

{encrypted\_message} 
```

授权头包含一个 JSON Web 令牌(JWT)。这个令牌是向第三方发送消息的一种方式，因此接收方可以验证是谁发送的消息。

它包括以下权利要求:

*   令牌中的“aud”(受众)声明必须包括推送服务器 URL 来源的 unicode 序列化。这将令牌绑定到特定的推送服务器。
*   “exp”(到期)声明必须包含在令牌到期的时间中。这限制了令牌的有效时间。“过期”索赔不得超过申请时间的 24 小时。
*   JWT 的“次级”(主体)索赔。“sub”声明应该包括应用服务器的联系人 URI，可以是“mailto:”或“https:”URI。

还有一个额外的“Crypto-Key”头，其中包括 base64url 编码的 ApplicationServerKey。

## 处理推送消息

应用服务器向用户发送推送消息，推送服务器验证该消息并将其转发给浏览器。浏览器唤醒了服务人员，并向其发送了推送事件。服务工作者处理该事件并处理推送消息。

这一轮的最后一步由服务人员完成。应将此代码添加到您的服务人员中:

```
self.addEventListener(&#39;push&#39;, function(event) {

  var data = event.data.json();

  event.waitUntil(self.registration.showNotification(data.title, {

    body: data.body,

    icon: data.icon,

    tag: data.tag

  }));

}); 
```

服务工作器的 push 事件监听器函数接收一个事件对象作为参数。该事件对象包括作为属性的推送消息数据。处理程序通过调用 showNotification 方法显示基于该数据的通知。您需要将通知标题和通知配置对象传递给该方法。这些配置对象可能包含定义通知的不同参数:通知文本的正文、图标、图像、振动以及其他一些参数。

我想谈两个要点:

*   Firefox 允许在不显示通知的情况下向应用程序发送有限数量(配额)的推送消息，并且该限制在每次访问该站点时都会刷新。但是 Chrome 要求每条推送消息都要显示通知，如果你不显示通知，Chrome 会显示默认通知。
*   waitUntil 方法告诉浏览器保持服务工作器运行，直到传递给该方法的承诺得到解决。如果不添加此方法，浏览器将立即关闭服务工作程序，并且不会显示通知。

## 处理通知点击

服务人员需要处理通知点击。最常见的反应之一是关闭通知并打开特定 URL 的窗口/标签。我们可以使用 [clients.openWindow()](https://developer.mozilla.org/en-US/docs/Web/API/Clients/openWindow) API 来实现这一点。

```
self.addEventListener(&#39;notificationclick&#39;, function(event) {

  event.notification.close();

  event.waitUntil(clients.openWindow(&#39;http://mywebsite.com&#39;));

}); 
```

## 带操作的通知

通知还可以包含操作，即带有一些文本的按钮。您可以使用通知配置对象的 actions 参数来定义它们。然后在 notificationclick 处理程序中，您可以检查哪个按钮被单击了。

```
self.registration.showNotification(data.title, {

  body: data.body,

  actions: [

    { action: &#39;ok&#39;, title: &#39;Yes&#39; },

    { action: &#39;decline&#39;, title: &#39;No&#39; }

  ]

}); 
```

…

```
self.addEventListener(&#39;notificationclick&#39;, function(event) {

  if (event.action == &#39;ok&#39;) {

    // do something

  }

}); 
```

## 通知关闭

您必须处理的最后一件事是 notificationcloseevent。当用户没有点击任何动作按钮或通知本身，而是点击了关闭按钮时，就会发生这种情况。例如，这是执行一些日志记录的好地方。

```
self.addEventListener(&#39;notificationclose&#39;, function(event) {

  //do something

}); 
```

总是显示通知的规则有一个例外。当用户正在使用您的应用程序时，并不强制显示通知。但是，如果您仍然想让用户知道事件已经发生，该怎么办呢？一种方法是从服务人员向页面发送消息，这样网页可以向用户显示通知或更新，通知他事件。

为了处理这种情况，我们可以检查是否有任何窗口客户端被聚焦，并使用 postMessage 方法将消息发送给每个客户端。这种方法是组织服务人员和页面之间的通信通道的常用方法。如果没有客户端窗口被聚焦，我们可以显示一个普通的通知。

```
if (focused) {

  clients.forEach(function(client){

    client.postMessage({

      message: data.message

    });

  });

} else {

  return self.registration.showNotification(data.title, {

    body: data.message

  });

} 
```

第三轮是最重要的一轮。它需要最大的努力来实现。您需要管理从应用服务器到 ServiceWorker 的整个推送消息，并在 ServiceWorker 的实现中处理它们。

## 第 4 轮:退订

第四轮是取消订阅 Web 推送通知。当用户不想再从你的应用程序接收网络推送通知时，就会发生这种情况。这一轮与订阅轮涉及相同的参与者 Web 应用程序、浏览器、应用服务器和推送服务器。

整个过程类似于订阅过程。您的 web 应用程序要求浏览器取消用户接收 Web 推送通知的订阅。浏览器向推送服务器发送请求以移除推送订阅。推送服务器向浏览器回复确认信息，浏览器将确认信息转发给 web 应用程序。最后一步，您的 web 应用程序将确认发送到应用服务器。并且应用服务器从数据库或其他存储器中移除订阅记录。

![](img/9379fcda0e5fcd88aaeb654ff084fec3.png)

您的 web 应用程序是主要参与者，我将向您展示的代码应该添加到您的 Web 应用程序中:

```
registration.pushManager.getSubscription().then(function(subscription) {

  if (subscription) {

    return subscription.unsubscribe().then(function(successful) {

      removePushSubscription(subscription);

    }).catch(function(e) {

      //error handling

    });

  }

})

.catch(function(error) {

  //error handling

}) 
```

我们从 pushManager 请求订阅对象，当承诺被解析时，我们调用订阅对象的 unsubscribe 方法。如果订阅被成功地从推送服务器中删除，我们调用一个方法，向应用服务器发送一个请求来删除这个订阅。

## 附加回合:订阅到期

还有一个额外的回合——订阅到期。当订阅即将过期时(服务器过载，或者用户长时间离线)，就会发生这种情况。这高度依赖于服务器，因此很难预测确切的行为。

![](img/07737552ad9acde3c6be556234cfbcb7.png)

在任何情况下，您都可以通过观察服务工作器中的 [pushsubscriptionchange](https://developer.mozilla.org/en-US/docs/Web/Events/pushsubscriptionchange) 事件并提供一个事件处理程序来处理这种情况；此事件仅在这种特定情况下触发。

```
self.addEventListener(&#39;pushsubscriptionchange&#39;,

  function(registration, newSubscription, oldSubscription) {

    removePushSubscription(oldSubscription);

    savePushSubscription(newSubscription);

}); 
```

在事件处理程序中，您需要从应用服务器中删除作为第一个参数传递的旧订阅，并保存作为第二个参数传递的新订阅。

## 规格状态

这是描述所有这些回合规则的规范的当前状态。只有通知 API 和 Web 推送规范才具有生存状态和提议的标准。Push API、Push 消息加密和 VAPID 规范仍然是草案。

| 规格 | 状态 |
| --- | --- |
| 推送 API | 工作草案 |
| 通知 API | 生活标准 |
| HTTP 网页推送 | 提议的标准 |
| 推送消息加密协议 | 网络草稿 |
| 乏味的协议 | 网络草稿 |

## 浏览器支持

这是支持这一新功能的浏览器列表。

| 浏览器 | 桌面 | 移动的 |
| --- | --- | --- |
| 铬 | 是 | 是 |
| 火狐浏览器 | 是 | 是 |
| 边缘 | 贝塔 | 贝塔 |
| 旅行队 | 是 | 不 |
| 歌剧 | 是 | 是 |
| 工业管理学(Industrial Engineering) | 不 | 不 |

浏览器支持变化很快。只有 Chrome 和 Firefox 完全支持这一功能。Opera 最近开始支持了。Safari 在 Mac OS 上支持它，但方式不同。苹果的实现与官方规范不同。他们有相同的球员和相同的基本概念，但它的 API 和技术实现略有不同。例如，通知存储在 MacOS 通知中心。

在 Edge 中，桌面和平板电脑上的 Windows Insider 预览版中提供了 Web 推送通知。并且[微软宣布](https://blogs.windows.com/windowsexperience/2016/03/30/windows-10-anniversary-update-brings-new-experiences-and-developer-opportunity/)将在 EdgeHTML 14 中对所有微软 Edge 客户开放。IE 不支持网页推送通知，但我猜没人会想到这一点。

目前最大的缺失是 iOS。iOS 不支持推送通知，但在 2015 年 WebKit 贡献者会议上的 [5 年计划](https://trac.webkit.org/wiki/FiveYearPlanFall2015)中提到过。所以，希望有一天能得到支持。

## 结论

Web 推送通知技术即将被广泛应用。它帮助你以更有效的方式与用户交流，通过提供紧急和相关的通知来吸引用户，并使 web 应用程序在总体上变得更好。

在您的应用程序中尝试一下。Sencha Cmd 6.5 支持以下渐进式 web 应用程序功能:添加到主屏幕横幅和服务人员缓存。[阅读 Sencha Cmd 文档](http://docs.sencha.com/cmd/6.5.3/guides/progressive_web_apps.html?utm_source=sitepoint&utm_medium=tpe&utm_campaign=sncextjs&utm_content=032818-pt-sncextjs-wpn-tpe)以了解更多信息，或者[下载 Sencha Ext JS 的免费试用版](https://www.sencha.com/products/extjs/evaluate/?utm_source=sitepoint&utm_medium=tpe&utm_campaign=sncextjs&utm_content=032818-pt-sncextjs-wpn-tpe)，在那里您将获得超过 115 个组件和工具，包括 Sencha Cmd。

## 分享这篇文章