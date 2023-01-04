# 介绍雪碧工厂宝石

> 原文：<https://www.sitepoint.com/introducing-the-sprite-factory-gem/>

对于 web 页面来说，最大限度地减少对图像、javascript 文件和样式表等资源的 HTTP 请求数量是一个公认的性能最佳实践。事实上，在史蒂夫·索德斯[高性能网站](http://www.amazon.com/gp/product/0596529309?ie=UTF8&tag=stevsoud-20&linkCode=as2&camp=1789&creative=9325&creativeASIN=0596529309)上，它的[规则#1](http://stevesouders.com/hpws/rule-min-http.php) 。

在最新版本的 Rails 中，这些实践正通过[资产管道](http://ryanbigg.com/guides/asset_pipeline.html)正式用于 javascript 和 css，使用[链轮](http://getsprockets.org/)和 [sass](http://sass-lang.com/) 库在生产模式下将多个较小的文件合并成一个统一的(和缩小的)资产文件。

但是对于图像，最佳实践是使用 [css sprites](http://www.alistapart.com/articles/sprites) ，Rails 还没有最佳实践解决方案。许多开发人员正在使用手动工具，这需要将文件上传到第三方网络服务，如 [Sprite Cow](http://www.spritecow.com/) 或 [SpriteMe](http://spriteme.org/) ，而将全自动系统内置到 Rake 任务或命令行脚本中会更好。

介绍[精灵工厂](https://github.com/jakesgordon/sprite-factory/)宝石。

sprite factory 是一个 ruby 库，可用于在任何 ruby 应用程序中轻松生成 CSS sprites。它将一个目录中的单个图像文件合并成一个统一的 sprite 图像，并创建一个合适的 CSS 样式表用于您的 web 应用程序。

该图书馆提供:

*   ruby API 和命令行脚本
*   许多可定制选项
*   支持任何样式表语法，包括 CSS 和 Sass。
*   支持任何图像库，包括 RMagick 和 ChunkyPNG。
*   支持不同的布局策略-水平，垂直或包装矩形。

你可以在 [github](https://github.com/jakesgordon/sprite-factory/) 上找到安装和使用说明

这个 gem 最初是作为 [LiquidPlanner](http://liquidplanner.com) 在线项目管理网络应用的一部分开发的。所以感谢他们允许我开放原始版本的源代码。

## 分享这篇文章