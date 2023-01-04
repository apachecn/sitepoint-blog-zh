# Unicode 文本的 US-ASCII 音译

> 原文：<https://www.sitepoint.com/us-ascii-transliterations-of-unicode-text/>

继[这个版本](https://www.sitepoint.com/php-utf-8-01/)之后，[这个](https://www.sitepoint.com/php-utf-8-01/#comment-14626)比我想象的要容易——将 [Text::Unidecode](http://search.cpan.org/perldoc/Text::Unidecode) 移植到 PHP——代码在此处[可用](http://prdownloads.sourceforge.net/phputf8/utf8_to_ascii-0.1.tar.gz?download)或者从[主页](http://sourceforge.net/projects/phputf8)找到 [utf8_to_ascii](http://sourceforge.net/project/showfiles.php?group_id=142846&package_id=182244) 包——单独发布它以保留原始(Perl artistic)许可证，而其余的东西在 LGPL 下。

现在我首先需要指出的是，与肖恩·m·伯克用 [Text::Unidecode](http://search.cpan.org/perldoc/Text::Unidecode) 所做的惊人工作相比，我所做的很容易。你真的需要阅读[文档](http://search.cpan.org/perldoc/Text::Unidecode)来理解它做什么和它的局限性，但是，简而言之，它保留了一个 unicode 字符和相应的合理的 US-ASCII 等价物的“数据库”。例如，一个简单的转换是从“Zürich”到“Zuerich”，在日耳曼语中“ue”是“ü”的常见替换。

我才真正了解到肖恩在通过 PHP 版本传递这个 [UTF-8 采样器](http://www.columbia.edu/kermit/utf8.html)方面做得有多好——粗略估计，它对该文档中 85%以上的非 ASCII 字符做了“一些事情”。以下是一些片段，给你一个前后的感觉；

```
Before: *Sanskrit* /(standard transcription):/ k?ca? ?aknomyattum; nopahinasti m?m.
After:  *Sanskrit* /(standard transcription):/ kaca- saknomyattum; nopahinasti mam.

Before: *Greek*: ????? ?? ??? ???????? ?????? ????? ?? ???? ??????.
After: *Greek*: Mporo na phao spasmena gualia khoris na patho tipota.

Before: *Anglo-Saxon* /(Latin):/ Ic mæg glæs eotan ond hit ne hearmiað me.
After: *Anglo-Saxon* /(Latin):/ Ic maeg glaes eotan ond hit ne hearmiad me.

Before: *Soenderjysk*: Æ ka æe glass uhen at det go mæ naue.
After: *Soenderjysk*: AE ka aee glass uhen at det go mae naue.

Before: *Ukrainian*: ? ???? ???? ????, ? ???? ???? ?? ?????????.
After: *Ukrainian*: Ia mozhu yisti shklo, i vono mieni nie poshkodit'.

Before: *Farsi / Persian*: .?? ?? ????? ????? ????? ??? ???? ?????
After: *Farsi / Persian*: .mn my twnm bdwni Hss drd shyshh bkhwrm

```

对于母语为英语的人来说，这些是否真的有意义，我不能说(感谢反馈)。我想这部分取决于语言，比如希腊语比波斯语更容易。还应该指出的是，Text::Unidecode 数据库(我是 1 对 1 移植的——事实上有一个脚本可以自动完成它)并不完全完整——对于某些字符和语言，它没有数据。

也就是说，如果这些语言与你的网站不相关，当你需要 ASCII 而不是 UTF-8 时，这可能是一个很大的帮助。例如，如果您不想冒任何“网络钓鱼”的风险或处理 UTF-8 字符的开销，您可以将它用于文件名或关键的“标识符”，如 userid。你也可以考虑将它用于搜索引擎友好的网址——虽然现代浏览器在网址中很大程度上支持 UTF-8，但是[网络钓鱼](http://en.wikipedia.org/wiki/Phishing)又是一个问题，不这样做可能更聪明。

不管怎样——第一个 PHP 版本“工作”,尽管毫无疑问它可以变得更快(尽管我非常怀疑它会变得和 Perl 版本一样快)。这也可以很容易地移植到其他语言，如 Python 和 Ruby。

## 分享这篇文章