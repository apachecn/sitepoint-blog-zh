# 火狐、国家安全局和中间人

> 原文：<https://www.sitepoint.com/firefox-nsa-man-middle/>

我们都听说过关于美国国家安全局使用 MITM(中间人)技术在互联网上进行大规模间谍活动的传言，或者如果有人没有读过，在谷歌搜索中很少能找到完整的故事。

本文面向 Mozilla 用户，不涉及 VPN 的使用，只是简单介绍如何在 Firefox 浏览器中配置一些设置，以在试图解密流量时提高安全性。

如果您对 MITM 攻击一无所知，请看下图，它很好地阐释了这类攻击的概念(请随意阅读更多信息，该主题在许多论坛、博客和文章中有广泛讨论)。

![man in the middle](img/7e1de19c4bb5481e1488dbfba6e03264.png)

我推荐的第一件事是为 Firefox 安装这个插件:

[https://addons . Mozilla . org/ro/Firefox/addon/calomel-SSL-validation/](https://addons.mozilla.org/ro/firefox/addon/calomel-ssl-validation/)

![calomel ssl validation](img/4ab035ebe1df823e1d030603ee8f9a19.png)

该附件非常有用，因为:

*   它让你以一种非常简单的方式看到所访问的网站上使用的加密技术有多强
*   它允许您使用所用算法的“点”来详细查看加密是如何完成的
*   它允许您更改有用的设置。

在我研究这篇文章的时候，我注意到包括脸书、谷歌、贝宝等主要网站都默认提供了一个非常弱的加密:RC4——128 位！关于 RC4 算法及其问题的更多细节[可以在这里找到](https://en.wikipedia.org/wiki/RC4#Security)。

## 让我们来看看 SSL/TLS

我不希望这篇文章变成一篇关于 SSL / TLS 的文章，但是我必须说一些基本的事情。

*   SSL / TLS 是一些必须满足的协议和规则，以便在您的计算机和您访问的站点之间实现安全连接。TLS 基本上是 SSL 的升级版。
*   SSL 版本 1.0、2.0 和 3.0 是旧的，不应该使用，尤其是因为已知它们有安全问题。TLS 1.0 是 SSL 3.0 的增强版本，TLS 1.1 和 TLS 1.2 是 TLS 的更新版本。

创建 SSL 连接的过程如下:

1.  TCP 连接已建立
2.  客户端(浏览器)向服务器发送“客户端你好”。还发送其 SSL / TLS 版本。即 TLS 1.0
3.  服务器以“Hello World”响应，并指定支持的加密版本(即 TLS 1.0)和 TLS 选择的密码套件(用于加密的算法)
4.  服务器发送证书以进行识别。明确地说:“看，这是我是“www.sitepoint.com”的证明。为了被浏览器认为是有效的，证书必须由该领域的权威机构签名:VeriSign 和其他被认为是合法签署证书的公司
5.  客户端验证收到的证书是否正确，如果一切正常，客户端和服务器交换密钥(握手)。

使用上面交换的密钥，使用对称加密算法(例如 AES)通过“密钥交换”中的变化来执行数据加密。大小取决于密码组密钥。(例如，在 128 位 AES 加密上，为了强行发送数据，需要尝试 2 个^ 128 密钥；但是如果算法是 RC4，那么针对该算法的已知攻击有很多，因此这种加密在更短的时间内被破解的机会要大得多。)

## 我们能做什么？

正如我所说的，默认情况下，浏览器发送完整的密码套件列表，服务器通常选择简单快速的算法，如 RC4，以提高页面加载速度并与旧浏览器兼容。我们可以简单地通过改变浏览器发送给服务器的密码组列表来迫使浏览器选择更强的密码，从而消除像 RC4 这样的“弱”密码。

为此，在 Firefox 中使用`about:config`(在 url 中)进入配置页面，然后搜索`security.ssl`。

![security.ssl](img/a4bdb0512aeb4380a2b0c7d40dfba2f0.png)

从这里我们可以禁用某些密码套件。我不能肯定地说哪些是安全的，哪些不是，但我可以推荐你选择 RSA(密钥交换)和 AES (256 位)。除了 RSA_AES_256_SHA 我都禁用了。

## 结果呢

关闭并重新打开浏览器，然后:

![calomel ssl validation v2](img/f7db99a38198c34d94218ab63517ec40.png)

我希望这些信息能帮助你安全地浏览互联网，至少避免 MTIM 的攻击。

## 资源

*   SSL/TLS–http://en.wikipedia.org/wiki/Transport_Layer_Security
*   http://en.wikipedia.org/wiki/Man-in-the-middle_attack MITM
*   calomel–https://addons . Mozilla . org/ro/Firefox/addon/calomel-SSL-validation/

## 分享这篇文章