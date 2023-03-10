# 适用于 Mac OS X 的 Java 5

> 原文：<https://www.sitepoint.com/java-5-available-for-mac-os-x/>

同样在 Java 发布方面，苹果终于发布了用于 Mac OS X 的 Java 5.0。你需要首先升级到 Mac OS X 10.4 (Tiger)来获得这个——以前版本的操作系统不需要适用。

苹果对 Mac 版 Java 5.0 的推出持谨慎态度。这个新平台的第一个版本并没有取代系统上默认的 Java 版本(1.4.2 ),而是与它一起安装。

因此，默认情况下，Java 5.0 将只被捆绑为 Mac 应用程序的 Java 程序使用，这些程序包含指定 1.5+或 1.5*的 [JVMVersion](http://developer.apple.com/releasenotes/Java/Java50RN/) 的 [Info.plist 文件](http://developer.apple.com/documentation/Java/Conceptual/JavaPropVMInfoRef/Articles/JavaDictionaryInfo.plistKeys.html)。

或者，用户可以使用更新中包含的新的 [Java Preferences utility](http://docs.info.apple.com/article.html?artnum=301073) 将 Java 5.0 配置为系统上的默认 Java 版本。

简而言之，这意味着如果您的 Java 应用程序需要 Java 5.0 的功能，那么要在 Mac OS X 上运行它，您要么需要将其捆绑为 Mac 应用程序，并为尚未安装更新的用户提供说明，要么您必须要求您的用户修改他们系统上使用的默认 Java 版本。这两种选择都不是特别可行。

现在，抓住这个机会在最新版本的 Java for Mac 上测试您的 Java 应用程序，并等待苹果将新版本作为默认版本的那一天。

## 分享这篇文章