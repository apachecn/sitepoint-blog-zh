# apple“photocasting”Mac 仅使用无效的 RSS

> 原文：<https://www.sitepoint.com/apple-photocasting-mac-only-uses-invalid-rss/>

(via [Dave Winer](http://scripting.wordpress.com/2006/01/11/welcome-to-the-wonderful-wacky-world-of-apple-rss/) )苹果新发布的 [iPhoto 6](http://www.apple.com/ilife/iphoto/) 做了一件令人钦佩的工作，让用户可以方便地从他们的桌面发布他们的照片。当史蒂夫·乔布斯宣布该产品时，他引用了“行业标准”RSS 技术的使用使这成为可能。

这一切听起来都很棒，直到你尝试在 Safari 之外的浏览器中访问 photocasting 提要，或者使用 NetNewsWire 之外的提要阅读器订阅。该网站检测到您没有使用支持 photocast 的客户端，并显示一个 HTML 页面，要求您使用 Safari。

有问题的错误页面还提供了到 photocast RSS 源的直接链接。如果你了解你的 XML，看看这个[浏览器可见的样本](http://web.mac.com/mrakes/iPhoto/photocast_test/index.rss)，以及指出它包含的一些错误的[验证报告](http://feedvalidator.org/check?url=http://static2.podcatch.com/blogs/gems/snedit/rss.xml)。~~提要验证器遗漏了更多与没有用`apple-wallpapers:`名称空间前缀标记的非标准标签相关的错误。~~ <ins datetime="2006-01-18T02:43:47+00:00">**更新:**验证器已经更新，可以捕捉这些错误。</ins>

总之，苹果新推出的 iPhoto 6 会产生无效的 RSS，大多数阅读器都无法处理。为了掩盖这一事实，苹果公司推出了一个相当于“互联网浏览器最佳体验”的页面，将自己的无能转化为营销机会。

如果我之前有所怀疑，我接受指正。苹果是新的微软。

## 分享这篇文章