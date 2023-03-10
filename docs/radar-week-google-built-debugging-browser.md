# 本周我们关注的话题有:谷歌、构建工具和在浏览器中调试

> 原文：<https://www.sitepoint.com/radar-week-google-built-debugging-browser/>

欢迎来到下一期的《在我们的雷达上》，这是一个关于新闻、趋势和其他来自 web 开发世界的很酷的东西的每周综述。

最近确实发生了很多事情:杰夫·贝索斯发布了亚马逊的第一款智能手机；T2 正式将 rebeccapurple 添加到 CSS Color Level 4 中；T4 宣布 HTML5 可能会在今年年底完成 W3C 的定稿工作。奇迹会停止吗？其他新闻，微软发布了一条可以为移动设备无线充电的裤子，世界杯有了自己的 API，谷歌开始测试域名注册服务。

## 谷歌

与公司座右铭“不作恶”的公司保持一致，谷歌本周[宣布了 OpenSSL](http://arstechnica.com/security/2014/06/google-unveils-independent-fork-of-openssl-called-boringssl/) 的独立“分支”，这是一个广泛使用的加密库，在 Heartbleed 漏洞之后引起了国际关注。他们还[发起了“用代码制造”](http://thenextweb.com/google/2014/06/19/google-launches-made-code-initiative-encourage-girls-code-backed-50m-pledge/)，一个鼓励更多女孩编程的倡议，然后用 5000 万美元的承诺把钱用在他们的嘴上！Go 编程语言(最初由三名谷歌工程师开发)的版本 [1.3 也发布了。它承诺更快的编译时间，目标是谷歌的实验性](http://blog.golang.org/go1.3) [NaCl 跨平台技术](http://en.wikipedia.org/wiki/Google_Native_Client)。

如果这还不够，这里还有更多谷歌的优点(包括两个基于围棋的教程):

*   [Go:用 Beego 构建 Web 应用](https://www.sitepoint.com/go-building-web-applications-beego/)
*   [Go，Colourspace，以及一幅图像中的所有 RGB 颜色](https://medium.com/@kapuramax/procedural-image-generation-in-go-7a57ff2e2e90)
*   [Web 基础知识和 Web 初学者工具包:现代 Web 开发资源](http://googledevelopers.blogspot.ca/2014/06/web-fundamentals-and-web-starter-kit.html)
*   谷歌 Chrome 霸权(如果你知道其中一个是什么，会加分)
*   [19 项隐藏的 Chrome 功能，让您的生活更轻松](http://www.pcmag.com/slideshow/story/323996/19-hidden-chrome-features-that-will-make-your-life-easier)

如果说这还不够的话，谷歌 I/O(谷歌年度开发者大会)于本周举行，在那里，他们[先于苹果](http://www.telegraph.co.uk/technology/google/10926554/Google-IO-2014-Google-reveals-Android-for-cars-TVs-and-watches.html)发布了新的智能手表和其他 Android Wear 产品。你可以在这里找到[发布的其他内容的详细摘要。](http://www.forbes.com/sites/roberthof/2014/06/25/heres-what-google-is-unveiling-right-now-at-its-io-conference/)

关于谷歌已经说够了吗？好了，我们继续吧…

## 学问

在这个快节奏的行业中，保持您的技能与时俱进是一个持续的挑战，因此这里有一些文章和教程可以帮助您做到这一点:

*   [赌错赢对](http://www.sandimetz.com/blog/2014/05/28/betting-on-wrong/)
*   [启用 PhpMyAdmin 的额外功能](https://www.sitepoint.com/enable-phpmyadmins-extra-features/)
*   [极简主义简讯订阅表](http://osvaldas.info/minimalist-newsletter-subscription-form)
*   [ember . js 和 Rails 有相同的术语，但概念不同](http://robots.thoughtbot.com/shared-terminology-yet-different-concepts-between-emberjs-and-rails)
*   [JavaScript 中的依赖注入](http://krasimirtsonev.com/blog/article/Dependency-injection-in-JavaScript)
*   [不起眼的按钮元素](http://demosthenes.info/blog/884/The-Humble-Button-Element)
*   [为什么每种语言都需要下划线](http://hackflow.com/blog/2014/06/22/why-every-language-needs-its-underscore/)

如果所有关于“依赖注入”和“开放/封闭原则”的讨论在你脑海中呼啸而过，不要担心！这里有一个[奖励视频](https://vimeo.com/96425312)，它很好地解释了诸如“事件循环”、“回调”和“并发性”之类的东西。

## 本周流行语——构建工具

我们开发人员是一群懒人，会竭尽全力避免重复和任务(正如 xkcd 漫画所示)。为了简化我们的开发工作流程，我们更喜欢(或者应该更喜欢)使用构建工具:旨在自动化诸如编译预处理 CSS、缩小 JavaScript、运行单元测试或重新加载浏览器等日常活动的程序。

有许多构建工具，有时很难知道使用哪一种。不要害怕！下面是一些精选的文章，希望能让你更容易做出决定:

*   对于那些认为咕哝这种事情既奇怪又困难的人来说
*   5 项你不想错过的繁重任务！
*   [构建你的第一个 Grunt 插件](http://flippinawesome.org/2014/05/29/building-your-first-grunt-plugin/)
*   [有大口的建筑](http://www.smashingmagazine.com/2014/06/11/building-with-gulp/)
*   [浏览器入门](https://www.sitepoint.com/getting-started-browserify/)
*   jQuery:只使用你需要的东西

最后一个技巧是:jQuery 核心团队使用[这个简单的任务](https://github.com/rwaldron/grunt-compare-size)来查看每个变化对文件大小的影响。

## 浏览器工具

当我们谈到 web 开发工具时，让我们把注意力转向内置在浏览器中的工具。无论是处理布局问题、编辑音频，还是构建一个完整的 web 应用程序，我们都能满足您的需求。

*   [检查元素&在浏览器中排除 CSS 故障](https://learnable.com/hub/play/65)
*   [浏览器开发者工具的秘密](http://devtoolsecrets.com/)
*   [Mozilla 通过 WebIDE 在浏览器中引入了 Web 应用程序开发](http://www.infoworld.com/t/development-tools/mozilla-brings-web-app-dev-inside-the-browser-webide-244977)
*   [WebIDE 每晚登陆](https://hacks.mozilla.org/2014/06/webide-lands-in-nightly/)
*   [Firebug 2.0 的新特性](https://www.sitepoint.com/whats-new-firebug-2-0/)
*   [介绍 Firefox 开发工具中的网络音频编辑器](https://hacks.mozilla.org/2014/06/introducing-the-web-audio-editor-in-firefox-developer-tools/)

## 有趣的东西

在谈论了这么多工具和简化工作流程之后，让我们用一些有趣的随机网站的链接来解决问题，这些网站肯定会耗尽你新发现的生产力。

*   [互联网上最有用的 70 个网站](http://dailyzenlist.com/post/89263626837/70-of-the-most-useful-websites-on-the-internet)
*   因为电影院没有暂停按钮
*   [无用的网页](http://www.theuselessweb.com/)
*   [史蒂芬·科拜尔评论“Yo”智能手机应用](http://www.youtube.com/watch?v=b23LSEX6qzY)
*   如何侮辱你的老板而不让他们发现

这就是本周的全部内容。我将告诉你一个消息:期待已久的对 caniuse.com 的重新设计几乎已经完成，一个关于 T2 残疾不是二元的讨论，一个关于 T4 世界杯生日悖论的报告(这应该会让我那位有数学头脑的合著者感到高兴)。

那么，你对谷歌目前发生的事情有什么看法？你会使用 Grunt 或 Gulp 这样的构建工具吗？或者它们是在浪费时间吗？对于自己喜欢的浏览器工具，有没有什么小技巧或者窍门想分享一下？让我们知道，对话就可以开始了。

## 分享这篇文章