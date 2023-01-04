# 加速你的引导网站的 3 个技巧

> 原文：<https://www.sitepoint.com/3-tips-to-speed-up-your-bootstrap-website/>

网站优化是一组技术，包括服务器端和客户端，旨在加速网站的加载时间和在浏览器中的渲染，以获得更好的用户体验。

如今，访问者已经习惯了本地应用特有的即时性和反应性。他们期望在 1000 毫秒内加载一个网页。如果花费的时间比这长得多，他们可能会离开网站。

搜索引擎当然已经赶上了这一趋势，并且在许多方面都有所贡献。事实上，谷歌使用页面速度作为网站在桌面搜索中的排名因素已经有一段时间了。[谷歌宣布](https://webmasters.googleblog.com/2018/01/using-page-speed-in-mobile-search.html)从 2018 年 7 月起，移动搜索也将成为其搜索结果列表中的超级重要因素。

Bootstrap 经常被批评为给网站增加了不必要的臃肿，所以如果你在你的项目中使用这个流行的前端 UI 库，请确保你额外注意页面权重和页面速度。

在这篇文章中，我将通过三个**前端优化**步骤来确保你的基于 Bootstrap 的网站能够快速渲染和良好优化。

## 只下载你需要的引导包

如果你决定使用 Bootstrap 的[预编译下载包](http://getbootstrap.com/docs/4.0/getting-started/contents/#precompiled-bootstrap)，你应该认真考虑一下你真正需要的库的哪些部分。

下载文件夹包含完整的 CSS 库(`bootstrap.css`和`bootstrap.min.css`)和 JavaScript 组件库及其除 jQuery ( `bootstrap.bundle.js`和`bootstrap.bundle.min.js`)之外的所有依赖项，以及许多独立的 CSS 文件，其中包含这个流行的 UI 工具包的特定部分所需的代码。

如果您的项目只需要一个不错的 CSS 重置，只需使用`bootstrap-reboot.min.css`。如果你只需要一个灵活易用的网格系统，那么就选择`bootstrap-grid.min.css`。没有必要下载整个框架。另一方面，如果你知道你要使用库中的所有东西，至少要确保你包含了缩略版本。

JavaScript 代码也是如此。如果你知道你的项目中不会有下拉菜单、弹出菜单和工具提示，那么使用`bootstrap.min.js`而不是`bootstrap.bundle.min.js`，因为你不需要包含 [Popper.js](https://popper.js.org/) 。

## 选择源代码而不是预编译的下载包

尽管最新版本的 Bootstrap 允许您选择要包含在项目中的部分内容，但预编译文件可能仍然包含您可能实际上不需要的内容。

浏览器仍然必须下载和处理未使用的代码，这可能会影响网站性能，尤其是在慢速网络连接上。

更好的办法是下载[引导源代码](http://getbootstrap.com/docs/4.0/getting-started/contents/#bootstrap-source-code)，因为:

*   您将能够在您的项目中准确地包含您需要的组件
*   自定义库的任何部分都变得更干净、更高效，不需要一次又一次地覆盖样式
*   最终用于生产的样式表通常更精简。

## 利用成熟的客户端优化技术

除了上面提到的几点，优化构建在 Bootstrap 之上的网站的性能仍然需要像任何其他网站一样整合前端性能技术。

下面是一些关键因素，你可以注意到有效的网站前端优化。

### 编写精益 CSS 和 JavaScript

代码中的每个字符加起来就是网页的最终权重。编写干净简洁的 CSS 和 JavaScript 代码同时保持其可读性并不总是容易的。但是，应该是每个项目都要争取的东西。

良好的 CSS 实践包括去除未使用的选择器、重复代码和过度嵌套的规则。在项目开始时保持代码组织良好是很好的。例如，使用[风格指南](https://www.smashingmagazine.com/2012/10/why-coding-style-matters/)可以真正有益于您的开发过程和代码质量。

此外，有很棒的工具可以帮助你清理代码。像 [CSS Lint](http://csslint.net/) 和 [JSLint](http://www.jslint.com/) 这样的 linter 可以检查你的文档中的语法错误、低效的编码模式、未使用的代码等等。

### 缩小并连接 CSS 和 JavaScript 代码

一个重要的优化步骤是限制网站呈现内容所需的 HTTP 请求的数量。每次往返服务器和获取资源都需要时间，从而对用户体验产生负面影响。

**缩小**(即删除文档中的注释和空白)和**连接** CSS 和 JavaScript 文件现在已经成为一种统一的做法，旨在保持文件较小并减少 HTTP 请求的数量。

如果你想更深入地研究，加里·斯蒂文斯的《如何为更快的网站优化 CSS 和 JS》是一本很好的读物。

### 注意你的图像文件的大小

网页中最重要的部分通常由图像文件来表示，但是音频和视频文件也发挥了它们的作用。因此，优化视觉资产对网站性能至关重要。

这样做涉及两个方面:

*   确保您使用[正确的图像格式](https://www.sitepoint.com/how-to-select-the-perfect-image-format-to-optimize-your-website/)来完成手头的工作。
*   在上传资产用于生产之前，从资产中挤出多余的字节。有很棒的工具可以帮助你。查看在线工具，如光栅图像(PNG，JPG 等)的 TinyPNG 。)和 Jake Archibald 的用于 SVG 优化的 [SSVGOMG](https://jakearchibald.github.io/svgomg/) 。此外，考虑一下你可以在本地安装的工具，比如你最喜欢的[任务执行人](https://addyosmani.com/blog/image-optimization-tools/)(咕噜，大口，等等)。).

## 结论

快速渲染的网站是决定网站用户体验的核心因素。在评估移动设备上的网络用户体验时，这变得更加重要。

在本文中，我从前端开发的角度列出了一些在优化 Bootstrap 网站性能方面发挥作用的技术。

对于一个快速加载的 Bootstrap 网站，你有哪些前端优化技巧？请在评论中告诉我！

如果你已经掌握了引导程序的基本知识，但想知道如何将你的引导程序技能提升到一个新的水平，请查看我们的[用引导程序 4](https://www.sitepoint.com/premium/courses/building-your-first-website-with-bootstrap-4-2995) 建立你的第一个网站课程，快速而有趣地介绍引导程序的力量。

## 分享这篇文章