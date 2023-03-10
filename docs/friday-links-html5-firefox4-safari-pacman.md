# 周五链接综述:定期 HTML5 问题和吃豆人揭秘

> 原文：<https://www.sitepoint.com/friday-links-html5-firefox4-safari-pacman/>

今天是周五，所以这里有一些在过去一周引起我注意的随机链接和故事…

## [html 5 周期表](https://joshduck.com/periodic-table.html)

这太棒了:这是一个包含所有 104 种 HTML5 元素的列表，显示为一个周期表。单击任何元素可获得简要说明、规范链接和其他资源。

您还可以输入一个 URL 来显示哪些元素用于创建页面— [看看对 SitePoint 主页的分析](https://joshduck.com/periodic-table.html?url=www.sitepoint.com)。

[![HTML5 periodic table](img/f915976d9050d003341b5be4e7b10e9a.png)](https://joshduck.com/periodic-table.html)

这张表格是由澳大利亚网络开发者乔希·达克制作的。很棒的东西。

## [火狐 4.0，beta 4](https://www.mozilla.com/en-US/firefox/beta/)

本周早些时候，Mozilla 发布了第四个火狐浏览器的第四个测试版。主要变化:改进的 HTML5 渲染，WebM 视频支持，更好的插件崩溃保护，集成的标签和书签同步，“全景”——你打开的标签的分组视觉概览，以及[许多其他功能](https://www.mozilla.com/en-US/firefox/beta/features/)。

该浏览器还包括 Windows 上的 Direct2D 图形加速——IE 团队已经吹嘘了一段时间了。Mozilla 在默认情况下禁用了它，但是为了获得完全加速的体验，请在 about:config 中设置以下配置选项:

*   **Mozilla . widget . render-mode**—更改为 6
*   **gfx . font _ rendering . direct write . enabled**—设置为真。

## [html 5 innerShiv](https://jdbartlett.github.com/innershiv/)

IE 有一个稍微奇特的问题(不都是)。如果包含以下 [JavaScript shiv](https://www.sitepoint.com/5-reasons-why-you-can-use-html5-today/) ，就可以在浏览器中使用 HTML5:

```
 <!--[if lt IE 9]>  
<script src="https://html5shiv.googlecode.com/svn/trunk/html5.js"></script>  
<![endif]--> 
```

然而，当使用 innerHTML 将 HTML5 元素添加到节点，然后追加到文档时，shiv 不工作，例如

```
 var s = document.createElement('div');
s.innerHTML = "<section>Hi!</section>";
document.body.appendChild(s); 
```

幸运的是，网络开发者 Joe Bartlett 已经用 HTML5 innerShiv 解决了这个问题——这个小功能让 IE 再次开心起来。

## [Safari 浏览器扩展](https://extensions.apple.com/)

你用 Safari 吗？我也是，但我们可能会被诱惑，现在浏览器在[版本 5.0.1](https://www.apple.com/safari/) 中启用了[扩展](https://extensions.apple.com/)。

现在有几十个扩展可用，但由于它们是使用 HTML、CSS 和 JavaScript 构建的，你可以预期许多 Chrome 插件会迁移到 Safari。如果你想自己建一个，你需要注册参加 [Safari 开发者计划](https://developer.apple.com/programs/safari/)。这是免费的，苹果公司提供了一个[综合开发指南](https://developer.apple.com/safari/library/documentation/Tools/Conceptual/SafariExtensionGuide/Introduction/Introduction.html)。

## [吃豆人档案](https://home.comcast.net/~jpittman2/pacman/pacmandossier.html)

[![](img/07c48c70bf110ca5a736863099260cb0.png)](https://home.comcast.net/~jpittman2/pacman/pacmandossier.html) 这个有点跑题，但是你*知道*吃豆人吗？杰米·皮特曼的《吃豆人》档案会让你重新思考！

该页面包含程序员和游戏爱好者感兴趣的事实。你知道有 256 个级别吗，虽然超过 21 的每个级别都是相同的，256 级是坏的。你知道每个鬼魂都有不同的行为模式吗？你有没有发现吃一个普通的点会让吃豆人慢 1/60 秒，这样鬼魂就能更快地追上来？也许你不在乎，但这是对 30 年前游戏发展的惊人洞察。

## 分享这篇文章