# 修复 IE

> 原文：<https://www.sitepoint.com/fixing-ie/>

任何尝试过 CSS 驱动的、符合标准的设计技术的人都知道，最大的挑战来自最广泛使用的浏览器:Internet Explorer for Windows。由于它不可靠的盒子模型，缺少对 CSS2 选择器的支持，缺少核心属性，如最小宽度和最大宽度，许多智能 CSS 技术都不值得使用。

这是一个有据可查的问题。过去曾有人试图弥补 IE 的一些弱点，通常是用 Javascript 复制一些缺失的功能。戴夫·谢伊在他最近的一篇关于侧滑的文章中整理了其中的一些。主要的例子包括使用表达式的[最大宽度支持:](http://www.svendtofte.com/code/max_width_in_ie/)和 [whatever:hover](http://www.xs4all.nl/~peterned/csshover.html) 用于添加:对非链接元素的悬停支持。

这些都是有价值的工具，但是如果有一个单一的解决方案能让 IE 达到其他现代浏览器的标准，那不是很好吗？多亏了狄恩·爱德华兹，才有了: [IE 7](http://dean.edwards.name/IE7/intro/) ，一个厚脸皮的标题为 IE 错误修复的包，它覆盖了缺失的 CSS2 选择器、最小/最大宽度和高度属性，甚至增加了对 <abbr>HTML 元素的支持。它实现为一个 28KB 的 CSS 文件，可以链接到任何页面的顶部。</abbr>

很难说这个实现方法是粗糙的，天才的一笔还是两者的结合。迪恩关于这一切如何运作的笔记当然值得一查。当然，即使是 27 KB 的压缩包，大小和功能之间的权衡也需要仔细考虑，尽管正确配置的 web 服务器应该会导致神奇的样式表被加载一次并缓存起来以供将来的页面访问。

无论如何，这样的事情早就应该发生了。

## 分享这篇文章