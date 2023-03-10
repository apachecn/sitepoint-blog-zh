# Java 小程序在 Mac Firefox 中获得新生

> 原文：<https://www.sitepoint.com/java-applets-to-gain-new-life-in-mac-firefox/>

(Via[MozillaZine](http://www.mozillazine.org/))[OS X Mac 版火狐 1.5 将内置 Java 嵌入插件](http://www.mozillazine.org/talkback.html?article=7230)，允许浏览器利用该平台上最新版本的 Java。

随着技术的发展，Java 小程序经历了一段艰难的时期。这项技术刚出现时被大肆宣传，但由于微软不断重新发布过时且停滞不前的 Java 虚拟机(VM ),对 Java 的破坏，这项技术遭到了压制。

今天，Java 小程序是一项非常强大的技术。这是一个构建许多富互联网应用程序的理想平台，每个人似乎都在谈论它，但它最大的缺点仍然是当前版本的技术在 Web 浏览器中的可用性有限。

然而，这种情况正在发生变化，像戴尔和惠普这样的知名个人电脑制造商在出厂时已经预装了 Java。目前，最麻烦的抵制者之一是 Mac OS X 上的 Firefox，具有讽刺意味的是，由于苹果通过将 Java 内置到其操作系统中来支持 Java，因此在 Java 开发者中重新受到了冷落。虽然 Mac OS X 内置了 Java 1.4.2，并对 Java 5 提供了实验性的支持，但苹果针对 Firefox 等第三方网络浏览器的 API 仍停留在现已过时的 Java 1.3.1，这迫使希望获得全面跨浏览器支持的 applet 开发者回避新版本提供的功能(如 XML 支持)。

Steven Michaud 领导的开源项目 [Java Embedding Plugin](http://javaplugin.sourceforge.net/) 通过覆盖 Firefox 等浏览器中的内置 Java 支持并利用 Mac OS X 中最新的 Java 版本来挽救局面。到目前为止，最终用户必须先安装该插件，然后需要 Java 1.4 或更高版本的小程序才能在 Mac 上的 Firefox 中运行。

现在，随着 Firefox 1.5 [定于 11 月](http://developer.mozilla.org/devnews/index.php/2005/08/30/9-days-untill-15-beta/)发布，Java 开发人员将离跨所有主流浏览器和平台无缝部署小程序更近了一步。

完整披露:SitePoint 销售 [Editize](http://www.editize.com/) ，这是一款依靠 Java applet 技术的产品。

## 分享这篇文章