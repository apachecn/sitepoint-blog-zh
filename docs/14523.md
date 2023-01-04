# HTML 会逃离浏览器

> 原文：<https://www.sitepoint.com/html-escapes-the-browser/>

苹果最近的[全球开发者大会](http://developer.apple.com/wwdc/)最热门的话题之一是[仪表板](http://www.apple.com/macosx/tiger/dashboard.html)的发布，这是即将发布的 [OS X 老虎](http://www.apple.com/macosx/tiger/)的新功能，它给用户的桌面增加了一层有用的迷你应用程序(又名“小工具”)。这本身并不是一个新的想法:Mac 早在 1984 年就有桌面装饰，而令人印象深刻的共享软件应用 [Konfabulator](http://www.konfabulator.com/) 在 OS X 提供仪表板风格的功能已经有一段时间了。作为一个题外话，为了很好地揭穿“仪表板撕裂 Konfabulator”迷因，只需看看[大胆的火球](http://daringfireball.net/2004/06/dashboard_vs_konfabulator)。

Dashboard 真正令人兴奋的是小部件的编写方式。与 Konfabulator 不同，Konfabulator 使用简单的 XML 方言定义小部件布局，Dashboard 小部件是用 HTML 和 CSS 编写的，并使用 Safari 的 WebKit 引擎呈现！它们本质上是迷你网页，从浏览器中解放出来。野生动物园团队的戴夫·海厄特有一系列条目( [1](http://weblogs.mozillazine.org/hyatt/archives/2004_06.html#005876) 、 [2](http://weblogs.mozillazine.org/hyatt/archives/2004_06.html#005887) 和 [3](http://weblogs.mozillazine.org/hyatt/archives/2004_07.html#005896) )更详细。

近年来，将源于 web 的技术扩展到其他应用程序开发领域已经成为一个反复出现的主题。Adobe 的 Photoshop 和 Macromedia 的 Dreamweaver 都允许使用 JavaScript 创建扩展，整个 Mozilla 应用程序套件的界面都是使用 CSS、JavaScript 和 Mozilla 类似 HTML 的 XUL 界面语言组合定义的。甚至微软也加入了进来，Windows 应用程序开发(XAML)的未来也是如此。甚至微软失败的“活动桌面”概念也可以被视为这一想法的早期迭代，允许 HTML 应用程序生活在用户的桌面下。

有一点是肯定的:掌握网络技术变得越来越有用。

## 分享这篇文章