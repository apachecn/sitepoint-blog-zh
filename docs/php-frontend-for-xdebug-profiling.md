# 用于 Xdebug 分析的 PHP 前端？

> 原文：<https://www.sitepoint.com/php-frontend-for-xdebug-profiling/>

有人组装了一个 PHP 前端来查看 [Xdebug](http://www.xdebug.org/) 2.x 的分析输出吗？

Xdebug 1.x 有这个很好的`xdebug_dump_function_profile()`函数，它在你的输出中植入了一个包含分析统计信息的 HTML 表——很好而且很容易使用，忽略了一个小问题，即在你正在分析的代码的“带内”执行这个操作会使结果倾斜。在 Xdebug 2.x 中，它被放弃了，现在你需要一个像 [kcachegrind](http://kcachegrind.sourceforge.net/) 或 [wincachegrind](http://sourceforge.net/projects/wincachegrind/) 这样的工具，Xdebug 作为中间人生成一个输出文件。

配置文件输出文件的格式相当简单(事实上，wincachegrind 开发人员在安装自述文件中提供了一个很好的简短摘要)。也许更棘手的问题是如何处理大文件。这可能是使用 AJAX 的一个有效理由……事实上，尝试使用[wxpython](http://www.wxpython.org)([的书](http://www.manning.com/rappin/)已经出版——这很好)并组装一个跨平台前端是很诱人的，但那是另一回事了。

还是需要我亲自动手？；)

旁注:权威的 Xdebug 教程-[http://derickrethans.nl/files/phparch-xdebug-qa.pdf](http://derickrethans.nl/files/phparch-xdebug-qa.pdf)

## 分享这篇文章