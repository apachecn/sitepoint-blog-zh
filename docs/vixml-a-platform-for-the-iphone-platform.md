# VixML:iPhone 平台的平台

> 原文：<https://www.sitepoint.com/vixml-a-platform-for-the-iphone-platform/>

总部位于马萨诸塞州剑桥的社交应用开发商 [Viximo](http://www.viximo.com/) 今天发布了一个 iPhone 平台，允许开发人员在不接触任何物体的情况下创建原生 iPhone 应用程序。该平台名为 [VixML](http://vixml.com/) ，允许开发人员创建 iPhone 应用程序，通过单个 xML 文件和一些动画精灵访问手机的多点触摸功能和其他输入方法(即语音)、加速度计和动画。

TechCrunch 报道了 VixML 的发布，并称其为“革命性的”，它表示，目前使用该平台创建的应用程序只能发布到 Viximo 即将推出的[true flight](http://trueflirt.com/)应用程序，该应用程序允许用户创建(使用 VixML)，发送给朋友，并查看“调情”动画和 iPhone 的迷你应用程序。

开发人员可以使用 VixML 来创建应用程序，然后发布这些应用程序以供 true flight 使用，这些应用程序可以在应用程序本身中使用，也可以作为通过 App Store 销售的 true flight 应用程序的独立附加捆绑包的一部分。据报道，Viximo 计划与 VixML 应用程序的开发者分享收入，TechCrunch 指出，这些开发者面临双重批准程序——他们需要应用程序同时获得 Viximo 和苹果的批准。

平台中的平台方法很有趣，因为它降低了 iPhone 的开发门槛，使 web 开发人员更容易创建原生应用程序，他们可以通过使用 VixML 之类的东西跳过学习 Objective C。尽管 VixML 在方法上是独一无二的，但它并不是我们所说的革命性的。我们在 9 月份报道了另外两个 iPhone 开发平台，这两个平台大大降低了开发者的准入门槛，并且以我们认为更聪明的方式做到了这一点。

[Big Five](http://www.big5apps.com/) ( [iTunes](http://itunes.apple.com/WebObjects/MZStore.woa/wa/viewSoftware?id=288374598&mt=8) )是 iPhone 和 iPod Touch 的网络应用程序网关，它提供了一个 API，允许开发人员通过 JavaScript 访问原生的 iPhone 功能，如加速度计、GPS 和摄像头。应用程序被发布到网络上，并通过用户从应用商店购买的五大浏览器进行访问。当我们在 9 月份写下他们的时候，Big Five 已经被 App Store 拒绝了，但是它已经被批准了，并且以 14.95 美元的价格出售。

我们几个月前提到的另一个 iPhone 开发平台是 [PhoneGap](http://phonegap.com/) ，它类似于 iPhone 的 Adobe AIR。PhoneGap 允许 web 应用程序开发人员将其 web 应用程序编译为原生 iPhone 应用程序，然后提交到应用程序商店进行收录。该平台本质上是一个在 iPhone 上运行的 web 应用程序的容器，允许它们访问加速度计、GPS、sqLite、摄像头和振动控制。PhoneGap 还使用 JavaScript API 来访问原生 iPhone 功能，然后创建一个指向特定 web 应用程序 URL 的地址栏免费 web 浏览器，该浏览器可以包装为一个独立的应用程序并提交给苹果。App Store 中的一些 iPhone 应用程序正在使用 PhoneGap，例如免费的“二进制时钟”应用程序( [iTunes](http://itunes.apple.com/WebObjects/MZStore.woa/wa/viewSoftware?id=294486524&mt=8) )。

所有这三个选项都允许 web 开发人员使用他们已知的技术(如 XML 和 JavaScript)来创建访问原生功能的应用程序，从而大大降低了创建 iPhone 应用程序的门槛。我们是 PhoneGap 方法的忠实粉丝，因为这是三种方法中唯一一种允许开发人员创建可以通过 App Store 直接提供给用户的应用程序，而不必通过中间应用程序(例如，用于 VixML 的 TrueFlirt 和用于 Big Five 的 Big Five 浏览器)。然而，所有这些平台都使 iPhone 开发变得更加容易，这对开发者来说是一件好事。

## 分享这篇文章