# PHP 和标准:arg_separator.output

> 原文：<https://www.sitepoint.com/php-and-standards-arg-separatoroutput/>

PHP 的配置指令 [arg_separator.output](http://www.php.net/manual/en/ini.core.php#ini.arg-separator.output) 允许你告诉 PHP 它应该如何在一个 URL 中分隔参数，并且有一个默认值“&”。

该指令影响 PHP 自动生成或修改的所有 URL。唯一可能影响我们的时候是当我们使用 [PHP 会话处理](http://www.php.net/manual/en/ref.session.php)和 [session.use-trans-sid](http://www.php.net/manual/en/ref.session.php#ini.session.use-trans-sid) 来自动生成带有会话 id 的 URL。所以，如果你不用这个，下面的问题可能不会影响到你。

理论上，如果您的 web 应用程序喜欢在 URL 中用另一个字符分隔参数，比如“；”，那么您可以告诉 PHP 使用该字符:

 `http://www.example.com/url?variable1=value1;variable2=value2`

然而，PHP 手册的[会话处理](http://www.php.net/manual/en/ref.session.php)部分中的以下注释指出了一个问题。

> 注意:arg_separator.output php.ini 指令允许定制参数分隔符。为了完全符合 XHTML，请指定& there。

这似乎很奇怪。首先，这个问题与 XHTML 一致性无关。快速浏览一下 [HTML 4.01 规范](https://www.w3.org/TR/REC-html40/appendix/notes.html#h-B.2.2)、 [HTML 3.2 规范](https://www.w3.org/TR/REC-html32-19970114#sgml)甚至这个[HTML 所基于的 SGML 简介](https://www.w3.org/TR/REC-html40/intro/sgmltut.html#h-3.2.2)应该会提醒你，所有出现的“&”都必须转义(例如，用&)，不管使用的 HTML 或 XHTML 版本如何。我相信，认为这只是 XHTML 问题的神话可能是由于这样一个事实，即验证一个标记只是在使用 XHTML 的同时才变得流行。

其次，PHP 手册中给出的 arg_separator.output 指令中给出的[描述](http://www.php.net/manual/en/ini.core.php#ini.arg-separator.output)表示将在 URL 中使用分隔符:

> *arg _ separator . output string*PHP 生成的 URL 中用来分隔参数的分隔符。

PHP 掩盖了 URL 和用 HTML 属性值表示的 URL 之间的区别。在第二种情况下，必须对一小部分字符(&和' or ')进行转义。我们应该能够将 arg_separator.output 设置为' & '，每当 PHP 在 HTML(或 XHTML，就此而言)中将它用作属性值时，它应该适当地对其进行转义。

果不其然，当使用值' & '时，PHP 错误地将您的链接转换成如下形式:

 `[Tony](/?name=tony&SID=26ff231956e4dc5feaa3648fad0295af)`

这是 PHP 的默认行为，在所有版本的 HTML 和 XHTML 中都是不正确的。

PHP 中的一个[注释](http://www.php.net/manual/en/ini.core.php#50514)展示了这造成的混乱:

> 当您想要使用 xhtml 时，将 arg_separator.output 设置为“&”是不好的。Xhtml 要求写出&而不是。例如，这会阻止使用 php 会话验证 xhtml。我希望默认值将被更改

这里隐含的 XHTML 和 HTML 之间的区别是不正确的。对属性值中的&符号进行转义的要求同样适用于 HTML(基于 SGML)和 XHTML(基于 XML)。此外，通过将缺省值设置为' & '来解决这个问题的建议方案是不合适的。理想情况下，这个值应该是' & '，PHP 应该意识到当它处理属性值时，它需要自己转换' & '。

如果您使用 PHP 的会话处理和 [session.use_trans_sid](http://www.php.net/manual/en/ref.session.php#ini.session.use-trans-sid) 来自动生成带有会话 id 的 URL，现在您可以在 PHP 配置中将 arg_separator.output 设置为“&”，以便在 HTML 或 XHTML 中保持格式良好。

[Lachlan](https://www.sitepoint.com/blog/) 向我指出，改变这个值也会影响 [http_build_query](http://www.php.net/manual/en/function.http-build-query.php) ，它是用来生成原始 URL 的，不应该包含 HTML 实体。因此，如果您确实要将 arg_separator.output 设置为'【T4]'来解决这个问题，请避免使用 http_build_query，反之亦然。

最优雅的解决方案是在 URL 中插入您自己的会话 id，并完全避免使用 session.use_trans_sid，因为 session.use_trans_sid 是一种巫术。

## 分享这篇文章