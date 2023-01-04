# JavaScript: 2015 年回顾

> 原文：<https://www.sitepoint.com/javascript-2015-review/>

JavaScript 经历了非凡的一年。尽管在五月达到了 20 岁，新闻，项目和对语言的兴趣继续呈指数增长。也许是我进入了一个奇怪的圈子，但我想不出还有哪种技术能以类似的速度前进。这变得越来越难跟上，所以我希望这个总结有所帮助…

## ECMAScript 翻转

对 ECMAScript Harmony 或 ECMAScript 6.0 长达七年的等待终于在 2015 年 6 月以正式完成的规范而告终。

ES6 很快被重新命名为 ES2015，尽管我不知道有谁这么叫它。改名的前提是好的；2015 年是规范完成的一年。JavaScript 引擎现在可以声称他们完全符合 ES2015，每个人都明白。不幸的是，营销类型的人一点也不喜欢它；当听起来过时时，没有人会声称 ES2015 兼容 2016 年 1 月 1 日。

ES2015 对开发者意味着什么？准备好享受快乐吧，比如:

*   班
*   增强的对象文字
*   让和 const
*   箭头功能
*   模板字符串
*   迭代程序
*   发电机
*   解构
*   委托书
*   weakmap 和 weakset
*   标志
*   承诺
*   反射

大部分是语法上的糖。例如，JavaScript 将保留原型继承，但为那些从其他语言迁移过来的~~抱怨者~~开发者提供经典的类继承`class`结构。

ES2015 支持仍然不完整，但可以使用 transpiler 如 [Babel](https://babeljs.io/) 将大多数代码转换成 ES5。它工作得很好，但是额外的步骤会使测试和调试更加复杂。就我个人而言，我是一个 JavaScript Luddite，在支持得到改善之前，我更愿意坚持使用老方法。

但是，现在忘记 ES2015 吧，让我们转向 ES7/2016！

## JavaScript 复仇者联盟

随着 WebAssembly 的发布，宣传在 6 月达到了白热化；一种低级的二进制压缩的类似汇编的 web 语言！

将大型应用程序交付给浏览器并不总是可行的。一个游戏或复杂的程序可能需要许多兆字节的源代码，这些源代码被下载、编译成字节码并最终作为机器码运行。WebAssembly 通过将源代码编译成更简单、处理速度更快、与 JavaScript 引擎兼容的字节码，并将其打包在一个紧凑的二进制文件中，使这个过程更加高效。因此，浏览器代码将加载和启动得更快。它不一定比普通的 JavaScript 运行得更快，但是优化是可能的。

我们以前参观过二元世界。Flash、Silverlight、Java 和 Google Native Client (NaCl)都做出了类似的承诺，但 WebAssembly 应该有更好的机会:

1.  需要对 JavaScript 引擎进行相对简单的更改
2.  不需要浏览器插件，而且
3.  得到了谷歌、微软、Mozilla 和苹果的支持。

激动吗？你最好温习一下你的 C/C++技能，因为这是第一批 WebAssembly 编译器的目标。

## Node.js 分叉

Node.js 从 2014 年相对小众的运行时发展到 2015 年的必备技术。Node.js 无处不在；您的桌面、您的服务器、您的 Raspberry Pi、您的嵌入式设备等等。甚至其他信仰的开发者也采用 Node.js 来使用 Grunt 和 Gulp 等构建工具。

随着 [io.js](https://iojs.org/) fork 的合并，2015 年见证了围绕节点的[政治争论的结束。这意味着 Node.js v0.12 可能会在 9 月份被 Node.js v4.0 取代。没错，那是整整三个版本的跳跃，因为 io.js 已经到了 3.0 版本，无法倒退。](http://readwrite.com/2014/12/04/node-js-fork-io-js)

当然，版本号没有任何意义…除了那些现在认为 Node.js 4+比 Ruby 2 和 Python 3 更先进的人。鉴于 v5 晚到了一个月，它也被超越了。NET 4，并正在迅速赶上 PHP 7！

## 框架疲劳

2015 年推出了 2269 个新的 JavaScript 框架。我在完全没有研究的情况下发明了这个数字，但很少有人会怀疑。很难保持跟踪…

*   AngularJS 仍然很受欢迎，但由于 2016 年推出时没有升级到 2.0 版本的途径，其使用可能会停滞不前
*   反应引起了强烈的兴趣
*   [流星](https://www.meteor.com/)承诺全栈同构 JavaScript 应用平台
*   Vue.js 达到 1.0 版本， [Aurelia](http://aurelia.io/) 上线
*   [余烬](http://emberjs.com/)、[淘汰赛](http://knockoutjs.com/)和[中坚力量. js](http://backbonejs.org/) 依然强劲
*   有些人，如 [Rendr](http://rendrjs.github.io/) ，看起来好像他们[会变得受欢迎](https://www.sitepoint.com/3-javascript-libraries-keep-eye-2015/)，但并没有。

JavaScript 开发人员不可能满怀信心地支持特定的代码库。有太多的碎片化和整体一体化[框架可能会导致与它们解决的问题一样多的问题](http://www.quirksmode.org/blog/archives/2015/05/tools_dont_solv.html)。依赖一个抽象底层技术的框架可能是危险的，根据这位智者的观点，[前端 JavaScript 依赖是完全错误的](https://www.sitepoint.com/javascript-dependency-backlash-myth-busting-progressive-enhancement/)。

如果你必须的话，使用一个框架，但是永远不要假设它是长期可行的。在项目进行到一半之前，更新更好的替代方案就会出现。不要忘记框架是一种选择，你不需要使用框架。具有最小依赖性的更小、更灵活的项目是更安全的选择。

## 加工潮汐波

你还记得那些无忧无虑的日子吗？那时 web 开发只需要一个浏览器、一个文本编辑器和一个 FTP 客户端。今天，你需要 Node.js、Gulp/Grunt、git、静态 HTML 生成器、Sass 编译器、Autoprefixer、minifiers、uglifiers、linters、BrowserSync 和一系列其他构建工具来创建一个基本的*“Hello World”*页面。

大部分是好的。我们正在开发越来越复杂的 web 应用程序，这些工具允许我们自动化平凡的过程，并专注于更有趣的棘手部分。2015 年是伟大的一年，因为:

*   GitHub 的 Node.js 支持的黑客编辑器 Atom 1.0 于 6 月发布
*   快速、模块化的 CSS 处理器获得了我的年度工具奖
*   包括 [Edge](https://www.sitepoint.com/microsoft-edge-review/) 和 [Vivaldi](https://www.sitepoint.com/vivaldi-operas-spiritual-successor/) 在内的一系列新浏览器以及众多开发者工具增强功能。

然而，尽管有这些免费的优化工具，平均网页重量超过 2MB，由 100 个独立的文件组成。这是进步吗？如果你读了克林·芬利的《T2》，也许就不会了。我整整一周没用 JavaScript，这很棒。这不是技术的问题，而是实现的问题。关闭 JavaScript 会屏蔽广告、弹出窗口、模态对话框、无限滚动的页面、社交注册提示、时事通讯和所有其他我们认为对网页至关重要的恼人的膨胀。

一些公司希望用诸如脸书即时和 T2 谷歌加速移动网页来阻止网页腐烂。我不相信一个隔离的非标准网络是答案。

## 最后

官方: [Node.js 比 PHP](https://www.sitepoint.com/sitepoint-smackdown-php-vs-node-js/) 好！诚然，这篇文章并没有做出这样的声明，但它说明了 Node.js 已经走了多远。鉴于版本 7 速度的提高，也许 PHP 现在会赢，但是最终，使用你觉得舒服的任何东西。有争议的是，Automattic 最近将他们的[WordPress.com](https://wordpress.com/)前端从 [PHP 转换为 Node.js](https://developer.wordpress.com/2015/11/23/the-story-behind-the-new-wordpress-com/) ，但我怀疑这更多的是关于 Calypso 项目的状态而不是语言。

2015 年的技术流行语是*“同构”*。SitePoint 已经[讨论过这个问题](https://www.sitepoint.com/isomorphic-javascript-applications/)并且[提供了示例代码](https://www.sitepoint.com/url-parsing-isomorphic-javascript/)，但是，本质上，这意味着你写一次 JavaScript 代码就可以在任何地方工作——在浏览器或服务器上。对于小型的、自包含的实用程序来说，这相对简单，但是很快就变得复杂得令人麻木。

jQuery 3.0 的第一个 alpha 版本于 7 月发布。承诺推出两个新版本；一个用于现代浏览器，另一个*兼容*版本支持 IE8。该团队预计很少有突破性的变化，这是一个相当大的成就。

12 月，Mozilla 宣布他们将[停止销售 JavaScript 驱动的 Firefox OS 智能手机](http://techcrunch.com/2015/12/08/mozilla-will-stop-developing-and-selling-firefox-os-smartphones/)。火狐操作系统还没有结束——开发仍在继续，并被用于一些智能电视中——但迹象并不乐观。也许更好的选择是不依赖于操作系统的可安装的离线网络应用程序？

2015 年最大的企业 JavaScript 惊喜来自微软:

*   Visual Studio Code ，一个新的跨平台可扩展的、崇高的文本/类原子编辑器
*   开源远程调试和测试工具 vorlon.js
*   Chakra，Edge 的 JavaScript 引擎，[开源了](https://blogs.windows.com/msedgedev/2015/12/05/open-source-chakra-core/)。

鉴于最近对网络的开放，也许我们应该称该公司为“新微软”？

## 2016 年展望

对 JavaScript 的态度已经改变。几年前，它被(不公平地)嘲笑为一种难以理解的、有缺陷的脚本语言，被真正的开发者所回避。今天，你无法回避语言，工具、框架和项目的增长将在 2016 年继续。

跟上步伐并不容易，但支持 JavaScript，你不会错得太多。

## 分享这篇文章