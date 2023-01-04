# 使用闭包编译器 REST API 实现简单的 JavaScript 压缩

> 原文：<https://www.sitepoint.com/compress-javascript-closure-compiler-rest-api/>

小 JavaScript 文件的时代已经过去了。今天的整体式 web 应用程序包含长达数千行的客户端代码。浏览器和下载速度可能有所提高，但优化 JavaScript 变得越来越重要。专业开发人员采用的技术包括:

1.  将`script`标签放在 HTML `body`的末尾，这样内容会很快出现，不会因为代码加载和执行而延迟。
2.  通过将多个 JavaScript 文件合并成一个文件来减少 HTTP 请求。
3.  通过删除不必要的空格、缩短变量名和运行其他优化来减小 JavaScript 文件的大小。

有几个 JavaScript [压缩](http://developer.yahoo.com/yui/compressor/) [库](http://crockford.com/javascript/jsmin)和[在线](http://compressorrater.thruhere.net/) [工具](http://dean.edwards.name/packer/)帮助你收缩代码。然而，对于许多开发人员来说，步骤 2 和 3 通常仍然是一个手动过程。

如果你厌倦了手动 JavaScript 压缩，你会很高兴听到[谷歌的闭包编译器](http://closure-compiler.appspot.com/home)最近增加了一个 [REST API](http://code.google.com/closure/compiler/docs/api-tutorial1.html) 。现在，您可以调用 web 服务来压缩 JavaScript——无需安装或维护代码。

该 API 使开发人员可以轻松创建自己的 JavaScript 合并和压缩系统。例如，您可以:

*   生成突出显示代码错误和警告的报告。API 可以检查浏览器通常不会报告的问题，例如未使用的变量或数组中最后一项后的逗号。
*   向应用程序开发人员分发未压缩的代码
*   为测试人员和最终用户压缩代码
*   实现版本控制机制
*   更新实时系统时，将压缩集成到您的构建中。

不确定从哪里开始？阅读我的下一篇文章— [如何用 PHP 和闭包编译器开发自己的 JavaScript 压缩器](https://www.sitepoint.com/compress-javascript-with-php/) …

有关更多信息，请参考:

*   [闭包编译器主页](http://closure-compiler.appspot.com/home)
*   [API 入门](http://code.google.com/closure/compiler/docs/gettingstarted_api.html)
*   [闭包编译器服务 API 引用](http://code.google.com/closure/compiler/docs/api-ref.html)

## 分享这篇文章