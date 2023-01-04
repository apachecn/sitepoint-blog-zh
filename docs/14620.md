# 整洁的 HTML

> 原文：<https://www.sitepoint.com/tidy-html/>

John Coggeshall 在这里发布了这张来自国际 PHP 大会[的幻灯片。](http://talks.coggeshall.org/show.php/tidy_ints2004/)

乍看之下，整洁对于迂腐的人来说似乎只不过是一个不错的工具。乍一看，我开始认为 Tidy 可能是 PHP 长期以来最大的新功能；我们会一次又一次感谢约翰。

看一看[这张幻灯片](http://talks.coggeshall.org/show.php/tidy_ints2004/10)例如——Tidy 足够智能，能够提取遗留的 HTML“样式”标签并将其转换为 CSS。也许这并不奇怪，因为底层的 C 库最初是从[戴夫·拉格特](https://www.w3.org/People/Raggett/)(HTML 之父)开始的

<form>tag, among many other things).

还有其他一些不错的功能，如 Word 2000 模式，这是一个为现实世界编写的工具。

对于 PHP，IMO 来说，Tidy 更令人兴奋的地方是它能够将 HTML 转换成 XML 解析格式。在某种程度上，这意味着 PHP 脚本生成的 HTML 看起来像什么几乎无关紧要——Tidy 可以将其转换(参见 [ob_tidyhandler](http://www.php.net/ob_tidyhandler) )为 XHTML，然后可以用 XSLT 进一步转换。

注意这里还有一个关于 Tidy @ Zend [的教程。](http://www.zend.com/php5/articles/php5-tidy.php)

</form>

## 分享这篇文章