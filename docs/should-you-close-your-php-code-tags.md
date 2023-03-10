# 应该关闭 PHP 代码标签吗？

> 原文：<https://www.sitepoint.com/should-you-close-your-php-code-tags/>

即使对 PHP 有一定了解的人也知道代码必须包含在特殊的标记中。

**note:** Alternative PHP tags

您可能还知道 PHP 代码可以用较少使用的标记来分隔。如果在 php.ini 中启用了 **short_open_tag** ，就可以使用<？然后呢。>尽管如果您在 XHTML 或 XML 中嵌入代码，应该避免使用它们。最后，如果在 php.ini 中设置了 **asp_tags** ，就可以使用 ASP 风格的< %和% >标记

但是，如果您的文件只包含 PHP，而没有转义的 HTML 代码，那么关闭？>标签完全是可选的。许多开发人员认为应该删除不必要的代码，但是还有一个原因可以让你考虑取消结束标签。假设我们有一个名为**library.php**的 PHP 函数库:

```
<?php// library functionsfunction DoStuff() {	// code}?> 
```

这个库包含在我们的主入口文件中，**index.php**:

```
<?phpinclude('library.php');// write a headerheader('X-Demo: Example');// set cookiesetcookie('TestCookie', 'Example');?><p>End of index.php file.</p>
```

不幸的是，加载此页面时，会出现两条带有相同消息的警告:

```
Warning: Cannot modify header information - headers already sent
```

或者更糟的是，如果您在一个实时环境中运行，警告已被禁用，没有消息出现。在这两种情况下，既没有设置头也没有设置 cookie，这可能会导致严重的应用程序问题。是什么导致了错误？看不出来，但是结尾后面有个空格字符？>在 library.php 文件中。当它被包含在 index.php 的顶部时，该空间作为页面内容被发送到浏览器——连同所有必要的 HTTP 头。一旦发送了第一块内容，就不可能设置额外的头或 cookies。

**note:** PHP output buffering

php 的现代版本在 php.ini 中设置了 **output_buffering** 标志。这将缓冲您的 HTML 输出，并在您的 PHP 代码已被处理或一旦缓冲达到限制(如 4096 字节)时发送它。您也可以使用 PHP 的`ob_start()`和`ob_end_flush()`来实现自己的缓冲功能。如果启用了输出缓冲，您可以在输出 HTML 后设置 HTTP 头和 cookies，因为返回的代码不会立即发送到浏览器。注意，旧版本的 PHP 和一些 ISP 不支持输出缓冲——这会影响服务器性能并需要更多内存。即使您确定总是启用缓冲，在发送页面内容之前设置 HTTP 头和 cookies 也是一个好习惯。

您的 PHP 应用程序可能包含许多库或类文件。可以想象，很难找到结尾后面的额外空格、回车符或任何其他字符？>.幸运的是，有一个简单的解决方法。如果你省略了结尾？>在所有 PHP 专用的代码文件中，这种错误不会发生——解析器会忽略空白。这是一个解决方案，但你会使用它吗？这让我觉得有点脏…你已经省略结尾了吗？>标签？你会采用这种做法吗？还是只是感觉不对？

## 分享这篇文章