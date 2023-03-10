# 帮助你掌握现代 CSS 的 5 个项目

> 原文：<https://www.sitepoint.com/master-modern-css-projects/>

许多人声称 CSS 不是一种编程语言。我同意— *这更难*。掌握 CSS 需要在设计、决心、创造性、经验以及编码方面的技能(特别是当使用像 [Sass](https://sass-lang.com/) 这样的预处理器时)。

CSS *向浏览器建议*布局和样式。浏览器可以选择任何方式解释这些建议，即使如此，用户或设备也可以忽略或覆盖任何属性。创建在所有设备和屏幕分辨率下都能很好工作的高性能代码是一个很少有人尝试或成功完成的挑战。然而，回报可能是令人振奋的。

从最简单的开始，下面的项目建议将帮助你使用在 [SitePoint Premium](https://www.sitepoint.com/premium/?utm_source=blog&utm_medium=articles) 上可获得的书籍来掌握 CSS。

## 1.使网站易于打印

访问您正在处理的站点，并尝试打印(或打印预览)页面。你对结果满意吗？

HTML 页面是一种连续的媒体，不一定能在印刷媒体上很好地工作。不适当的节、缩放、文本大小、列尺寸以及缺失或裁剪的内容都会导致难以理解的打印体验，很少开发人员会考虑这一点。

幸运的是，打印 CSS 可以在几个小时内开发出来。这通常是重置样式(白底黑字)，删除不必要的部分(菜单、英雄图像、表单、社交媒体小工具等)。)，使布局线性化，并减少对纸张和墨水的需求。

深入研究[基于浏览器的开发工具](https://www.sitepoint.com/premium/books/css-master-2nd-edition/read/2/jyqxmd9k?utm_source=blog&utm_medium=articles)(来自 [CSS 大师](https://www.sitepoint.com/premium/books/css-master-2nd-edition/?utm_source=blog&utm_medium=articles))和[浏览器开发工具秘密](https://www.sitepoint.com/premium/books/browser-devtool-secrets/)，发现如何在切换到打印渲染后检查和修改样式。

[有条件地应用 CSS](https://www.sitepoint.com/premium/books/css-master-2nd-edition/read/8/jyqxmeaa?utm_source=blog&utm_medium=articles)描述了如何定义包括`print`样式表在内的`@media`查询规则。

考虑你的[CSS 自定义属性的策略指南](https://www.sitepoint.com/premium/books/smashing-book-6-new-frontiers-in-web-design/read/4/jynr4gos?utm_source=blog&utm_medium=articles)(来自[网页设计新前沿](https://www.sitepoint.com/premium/books/smashing-book-6-new-frontiers-in-web-design/?utm_source=blog&utm_medium=articles))来决定 CSS 变量是否可以帮助打印属性。还要考虑[无障碍](https://www.sitepoint.com/premium/books/css-animation-101/read/19/jyqr3yvo?utm_source=blog&utm_medium=articles)(来自 [CSS 动画 101](https://www.sitepoint.com/premium/books/css-animation-101/?utm_source=blog&utm_medium=articles) )关闭动画或以最佳状态打印。

最后，[如何用 CSS](https://www.sitepoint.com/premium/books/css-tools-skills/read/6?utm_source=blog&utm_medium=articles) 创建打印机友好的页面(来自 [CSS 工具&技能](https://www.sitepoint.com/premium/books/css-tools-skills/?utm_source=blog&utm_medium=articles))提供了一个完整的打印优化教程，提供了节省墨水和纸张成本的技巧。

## 2.将现代 CSS 主题应用于现有网站

单一的配色很无聊！每个人都希望在他们的操作系统和应用程序中有一个黑暗模式选项，那么为什么不在你的网站上添加一个呢？

直到最近，主题转换器通常需要一组额外的带有 JavaScript 支持的切换控件的样式。然而，现代浏览器通过 CSS 自定义属性(变量)和 [`prefers-color-scheme`](https://developer.mozilla.org/docs/Web/CSS/@media/prefers-color-scheme) `@media`规则让生活变得更加简单。

[主题化策略](https://www.sitepoint.com/premium/books/smashing-book-6-new-frontiers-in-web-design/read/4/jynr4gsd?utm_source=blog&utm_medium=articles)(来自[网页设计新前沿](https://www.sitepoint.com/premium/books/smashing-book-6-new-frontiers-in-web-design/?utm_source=blog&utm_medium=articles))提供了一系列设计新主题时的想法和考虑。

[有条件应用 CSS](https://www.sitepoint.com/premium/books/css-master-2nd-edition/read/8/jyqxmeaa?utm_source=blog&utm_medium=articles)(来自 [CSS 大师](https://www.sitepoint.com/premium/books/css-master-2nd-edition/?utm_source=blog&utm_medium=articles))描述了如何定义包括`prefers-color-scheme`在内的`@media`查询规则。

最后，[现代 CSS:添加 CSS 黑暗主题](https://www.sitepoint.com/premium/books/modern-css/read/7/jyr01bqg?utm_source=blog&utm_medium=articles)(来自[现代 CSS](https://www.sitepoint.com/premium/books/modern-css/read/?utm_source=blog&utm_medium=articles) )提供了一个完整的黑暗主题启用教程。

## 3.返工表单布局

检查网站上的表单，例如查询或注册表单。除非它是最近才编码的，否则它很可能用容器 div 和基于浮动的布局来实现，这可能会在较小的屏幕上中断。旧的表单可能会使用不必要的 JavaScript，或者可访问性很差。

[一个注册表单](https://www.sitepoint.com/premium/books/form-design-patterns/read/1?utm_source=blog&utm_medium=articles)(来自[表单设计模式](https://www.sitepoint.com/premium/books/form-design-patterns/?utm_source=blog&utm_medium=articles))描述了使用 HTML5 设计、设计和编码表单的最佳方式。

CSS Grid 允许您消除不必要的标记，并在不求助于媒体查询的情况下制作防弹响应布局。下面的 CSS 网格教程将帮助您快速入门:

*   [生产就绪的 CSS 网格布局](https://www.sitepoint.com/premium/books/smashing-book-6-new-frontiers-in-web-design/read/3?utm_source=blog&utm_medium=articles)(来自[网页设计新前沿](https://www.sitepoint.com/premium/books/smashing-book-6-new-frontiers-in-web-design/?utm_source=blog&utm_medium=articles))
*   [用 CSS 网格](https://www.sitepoint.com/premium/books/css-master-2nd-edition/read/5/jyqxmdri?utm_source=blog&utm_medium=articles)创建布局(来自[主 CSS](https://www.sitepoint.com/premium/books/css-master-2nd-edition/read/5/jyqxmdri?utm_source=blog&utm_medium=articles) ，以及
*   [CSS 网格布局](https://www.sitepoint.com/premium/books/jump-start-responsive-web-design-2nd-edition/read/3/jznetjjy?utm_source=blog&utm_medium=articles)(从[跳转开始响应式网页设计](https://www.sitepoint.com/premium/books/jump-start-responsive-web-design-2nd-edition/?utm_source=blog&utm_medium=articles))

最后，[用 CSS 网格制作表单](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/read/5?utm_source=blog&utm_medium=articles)(来自 [CSS 网格布局](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/?utm_source=blog&utm_medium=articles))提供了一个完整的教程，为老版本的浏览器提供了一个基于网格的表单布局和浮动后备。

## 4.让你的网站更快

在 2020 年初，普通网页[需要 2MB 的下载量](https://httparchive.org/reports/page-weight)，这需要 20 秒才能完全出现在普通移动设备上。CSS 占 65KB，分布在七个文件中。这可能看起来不太重要，但是样式表属性会有所不同。

花几个小时检查你现有的网站，确定是否有可能用更有效的 CSS 替换或优化图像、字体和 JavaScript 效果。您的 CSS 代码可能会增长，但总体重量会下降，性能会显著提高。

[测试工具](https://www.sitepoint.com/premium/books/jump-start-web-performance/read/2?utm_source=blog&utm_medium=articles)(来自 [Jump Start Web Performance](https://www.sitepoint.com/premium/books/jump-start-web-performance/?utm_source=blog&utm_medium=articles) )和[UI 响应性调试](https://www.sitepoint.com/premium/books/css-master-2nd-edition/read/2/jyqxmdb1?utm_source=blog&utm_medium=articles)(来自 [CSS Master](https://www.sitepoint.com/premium/books/css-master-2nd-edition/?utm_source=blog&utm_medium=articles) )解释了如何使用现代浏览器开发工具来评估性能和发现优化目标。

[在 Web 上加载资源](https://www.sitepoint.com/premium/books/smashing-book-6-new-frontiers-in-web-design/read/6?utm_source=blog&utm_medium=articles)(来自[Web 设计新前沿](https://www.sitepoint.com/premium/books/smashing-book-6-new-frontiers-in-web-design/?utm_source=blog&utm_medium=articles))描述了如何使用关键 CSS 和渐进式 CSS 加载等技术来确保样式表被有效加载。[优化 CSS 性能的 20 个技巧](https://www.sitepoint.com/premium/books/modern-css/read/6?utm_source=blog&utm_medium=articles)(摘自[现代 CSS](https://www.sitepoint.com/premium/books/modern-css/?utm_source=blog&utm_medium=articles) )提供了一系列实用技巧。

最后， [Jump Start Web Performance](https://www.sitepoint.com/premium/books/jump-start-web-performance/?utm_source=blog&utm_medium=articles) 包含几十个快速、更密集、改变生活的开发建议，以确保您的网站对每个人来说都是快速的。

## 5.启动新项目或 CSS 组件

上述项目建议可用于改进现有站点，但当[从零开始一个新项目](https://www.sitepoint.com/start-new-projects-faster/)时，没有任何限制或约束。要考虑的选项:

1.  建立一份在线简历。确保其响应迅速、打印良好，并将所有资产编码成可通过电子邮件发送的单个 HTML 文件。(提示:避免添加 JavaScript，以确保它不会被电子邮件系统阻止。)
2.  **建立互动投资组合**。点击项目时，所有站点的图形列表以及附加信息。网格布局是理想的，但是，如果你真的想要挑战，试试[砖石布局](https://www.sitepoint.com/understanding-masonry-layout/)。(CSS Grid 还不能实现这一点，但是可以考虑如何使用 CSS 列或垂直有序的网格布局来实现。)
3.  **编码一个图形设计**。或许从 [Dribbble](https://dribbble.com/shots/following/web-design) 或类似的设计社区中选择一个有吸引力的想法，并编写 HTML5 和 CSS3。在没有框架或 JavaScript 的情况下创建它的加分！
4.  **创建纯 CSS 图像**。创建一组由伪元素和 CSS 形状驱动的有用图标，或者使用渐变和阴影构建图像。
5.  **试验 SVG 和 CSS 动画**。尝试创建吸引人的徽标、图表、进度条、活动微调器等等。

开发人员和浏览器工具:

*   [DOM、CSS 和动画](https://www.sitepoint.com/premium/books/browser-devtool-secrets/read/3?utm_source=blog&utm_medium=articles)(来自[浏览器开发工具秘密](https://www.sitepoint.com/premium/books/browser-devtool-secrets/?utm_source=blog&utm_medium=articles))
*   [如何使用 Gulp.js 自动化你的 CSS 任务](https://www.sitepoint.com/premium/books/css-tools-skills/read/1?utm_source=blog&utm_medium=articles)(来自 [CSS: Tools &技能](https://www.sitepoint.com/premium/books/css-tools-skills/?utm_source=blog&utm_medium=articles))
*   [跳转开始 Sass](https://www.sitepoint.com/premium/books/jump-start-sass/?utm_source=blog&utm_medium=articles)
*   [CSS 调试与优化:代码质量工具](https://www.sitepoint.com/premium/books/css-tools-skills/read/3?utm_source=blog&utm_medium=articles)(来自 [CSS:工具&技能](https://www.sitepoint.com/premium/books/css-tools-skills/?utm_source=blog&utm_medium=articles)
*   [CSS 调试优化:开发者工具](https://www.sitepoint.com/premium/books/css-tools-skills/read/4?utm_source=blog&utm_medium=articles)(来自 [CSS:工具&技能](https://www.sitepoint.com/premium/books/css-tools-skills/?utm_source=blog&utm_medium=articles)
*   [改变生活的饮食](https://www.sitepoint.com/premium/books/jump-start-web-performance/read/5?utm_source=blog&utm_medium=articles)(来自[跳跃式开始网络表演](https://www.sitepoint.com/premium/books/jump-start-web-performance/?utm_source=blog&utm_medium=articles))
*   [Visual Studio 代码:面向 Web 开发人员的端到端编辑和调试工具](https://www.sitepoint.com/premium/books/visual-studio-code-end-to-end-editing-and-debugging-tools-for-web-developers/?utm_source=blog&utm_medium=articles)

CSS 网格思想:

*   [重新设计网站使用 CSS 网格布局](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/read/1?utm_source=blog&utm_medium=articles)(来自 [CSS 网格布局](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/?utm_source=blog&utm_medium=articles))
*   [简单而灵敏的现代 CSS 网格布局](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/read/3?utm_source=blog&utm_medium=articles)(摘自 [CSS 网格布局](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/?utm_source=blog&utm_medium=articles))
*   [逐渐增强的 CSS 布局，从浮动到 Flexbox 再到网格](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/read/4?utm_source=blog&utm_medium=articles)(来自 [CSS 网格布局](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/?utm_source=blog&utm_medium=articles)
*   [用 CSS 网格](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/read/2?utm_source=blog&utm_medium=articles)重新设计基于卡片的 Tumblr 布局(来自 [CSS 网格布局](https://www.sitepoint.com/premium/books/css-grid-layout-5-practical-projects/?utm_source=blog&utm_medium=articles)
*   [具有网格系统的更好的响应结构](https://www.sitepoint.com/premium/books/jump-start-responsive-web-design-2nd-edition/read/3?utm_source=blog&utm_medium=articles)(来自 [Jump Start 响应式网页设计](https://www.sitepoint.com/premium/books/jump-start-responsive-web-design-2nd-edition/?utm_source=blog&utm_medium=articles))

响应式 CSS 技术:

*   [14 种基本的响应 CSS 技巧](https://www.sitepoint.com/premium/books/14-essential-responsive-css-techniques/?utm_source=blog&utm_medium=articles)
*   [响应式设计](https://www.sitepoint.com/premium/books/responsive-design/?utm_source=blog&utm_medium=articles)
*   [快速启动响应式网页设计](https://www.sitepoint.com/premium/books/jump-start-responsive-web-design-2nd-edition/?utm_source=blog&utm_medium=articles)

CSS 过渡和动画:

*   [CSS 动画 101](https://www.sitepoint.com/premium/books/css-animation-101/read?utm_source=blog&utm_medium=articles)
*   [过渡和动画](https://www.sitepoint.com/premium/books/css-master-2nd-edition/read/6?utm_source=blog&utm_medium=articles)(来自 [CSS 大师](https://www.sitepoint.com/premium/books/css-master-2nd-edition/?utm_source=blog&utm_medium=articles))
*   [在现实世界中使用 CSS 变换](https://www.sitepoint.com/premium/books/modern-css/read/1?utm_source=blog&utm_medium=articles)(来自[现代 CSS](https://www.sitepoint.com/premium/books/modern-css/?utm_source=blog&utm_medium=articles) )

将 CSS 与 SVG 结合起来:

*   [制作 SVG 动画](https://www.sitepoint.com/premium/books/practical-svg/read/7?utm_source=blog&utm_medium=articles)(来自[实用 SVG](https://www.sitepoint.com/premium/books/practical-svg/?utm_source=blog&utm_medium=articles) )
*   [使用带有 SVG 的 CSS](https://www.sitepoint.com/premium/books/css-master-2nd-edition/read/9?utm_source=blog&utm_medium=articles)(来自 [CSS Master](https://www.sitepoint.com/premium/books/css-master-2nd-edition/?utm_source=blog&utm_medium=articles)
*   使用 SVG 的 CSS 的真实世界(来自[现代 CSS](https://www.sitepoint.com/premium/books/modern-css/?utm_source=blog&utm_medium=articles) )

现在停止阅读，开始编码吧！

## 分享这篇文章