# 蓝调翻译之路

> 原文：<https://www.sitepoint.com/the-path-translated-blues/>

不知道是否有人能为我们在 WACT 遇到的一个问题提供一些帮助/提示。我可以想象其他人也有同样的问题。

基本上，我们需要一种方法来确定从 WACT 内部开始执行脚本的路径。

典型地，当*使用* WACT 时，你会开始一个脚本:

 `< ?php // myscript.php require_once '/path/to/wact/common.inc.php'; // etc... ?>`

然后，WACT 中的代码需要能够确定 myscript.php 在文件系统中的位置，例如，用户模板可以相对于 myscript . PHP 的路径定位。

这似乎是一个简单的问题，只需使用；

 `$myscript_path = dirname($_SERVER['SCRIPT_FILENAME']);`

不幸的是，这只适用于某些 PHP 配置——当 PHP 被用作 CGI 可执行文件时(对于 CLI 变体可能也是如此),包含了 PHP 可执行文件的路径，而不是开始执行的 PHP 脚本。

另一个短期看起来不错的选择是:

 `$myscript_path = dirname($_SERVER['PATH_TRANSLATED']);`

不过，看看 PHP bugs 列表，这个变量似乎有问题，与正在使用的 Apache 版本有关。更坏的消息是它已经完全从 PHP5 中消失了，这里提到的[是](http://www.php.net/manual/en/migration5.incompatible.php)。

对于如何可靠地确定脚本开始执行的路径，还有其他想法吗？我开始怀疑 PHP 是否需要一个预定义的常量，比如 __MAIN__ 来处理这个独立于 PHP 安装的问题。

杰夫在这里更详细地强调了这个问题[。](http://sourceforge.net/mailarchive/forum.php?thread_id=4785357&forum_id=35579)

## 分享这篇文章