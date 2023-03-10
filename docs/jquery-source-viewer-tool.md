# 使用源代码查看器提高您的 jQuery 知识

> 原文：<https://www.sitepoint.com/jquery-source-viewer-tool/>

jQuery 是一个很棒的 JavaScript 框架。然而，与任何图书馆一样，有时有必要深入了解发生了什么。也许是因为您正在跟踪一个 bug，或者只是好奇 jQuery 如何实现特定的 UI 效果。

尽管 jQuery 压缩到 70Kb 以下，但未压缩的文件包含 6000 行 JavaScript 代码。您的文本编辑器或 IDE 可能会提供一个函数列表，但是有几十种方法需要仔细研究，而且要找到您需要的代码块并不总是那么容易。幸运的是，英国网络开发者 James Padolsey 提出了一个巧妙的解决方案——T2 jQuery 源代码查看器 T3。

![jQuery source viewer](img/0fc51a160e818409804cf9f417b3832d.png)

该工具将找到您输入的任何函数名的代码(注意名称是区分大小写的)。默认情况下，它将返回版本 1.4 代码，但版本 1.3.2 和 1.2.6 也是可用的。

其他 jQuery 方法被突出显示并可点击，因此很容易跳转到其他代码块。您还可以从 URL 中找到函数，例如

*   [http://james.padolsey.com/jquery/css](http://james.padolsey.com/jquery/css)
    展示了支持的最新版本 jQuery(1.4)的 css 方法
*   [http://james.padolsey.com/jquery/1.3.2/attr](http://james.padolsey.com/jquery/1.3.2/attr)
    展示了 jQuery 版本 1.3.2 的 attr 方法

这个工具是一个很棒的想法，在搜索 jQuery 源代码时肯定会节省时间。谢谢詹姆斯。我唯一的要求:一个不区分大小写的自动建议框将使它绝对完美。

## 分享这篇文章