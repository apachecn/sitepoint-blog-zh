# 蒂姆的评论挑战…

> 原文：<https://www.sitepoint.com/tims-comment-challenge/>

[蒂姆·布雷](http://www.tbray.org/ongoing/)正在考虑为他的博客添加一个[评论系统](http://www.tbray.org/ongoing/When/200x/2006/05/15/Comment-System)的技术选择；

> 我想借此机会学习至少一项新技术。

现在他提到了 Rails，但是阅读他对设计的想法不得不同意他的结论，那是矫枉过正。首先，为什么要为一个相对简单的应用程序注入一长串的依赖项和陡峭的学习曲线呢(顺便说一句，这适用于任何框架——不仅仅是 Rails)？此外，因为他倾向于文件而不是数据库，所以不需要活动记录等。

总之——在我看来，最明智的解决方案应该是一个 CGI 脚本(可能是 Perl)——他计划静态输出——评论是在批准后“编译”的——不需要处理高负载，但(为了减少管理界面的痛苦)需要在他能看到时间流逝的情况下容易地侵入 UI。

但是回到[这个讨论](http://www.tbray.org/ongoing/When/200x/2006/02/17/PHP) …

> 我真的应该认真写一个 PHP 应用程序

所以采取 PHP 策略——一些随机的想法/讨论点；

*   我们建议 Tim 使用表单库吗？例如 [QuickForm](http://pear.php.net/package/HTML_QuickForm/) 或 [Patforms](http://www.php-tools.net/site.php?file=patForms/overview.xml) (相关点——如果需要安装 PEAR，我们应该让他去哪里寻求帮助)？与此相反，我怀疑评论表单真的需要一个完整的表单库——香草 PHP？
*   对于注释标记，我们希望 Tim 指向什么？正如在之前提到的[,](https://www.sitepoint.com/safehtml-cleaning-form-input/)[Safe HTML](http://pixel-apes.com/safehtml/)(在 PEAR 下打包为 [HTML_Safe](http://pear.php.net/package/HTML_Safe) )将允许发布原始 HTML，也许在 [tidy](http://www.php.net/tidy) 的帮助下可以确定它是 XHTML。有 [PHP Markdown](http://www.michelf.com/projects/php-markdown/) (对此不太了解，例如安全记录/ UTF-8 处理)用于相当标准的标记。或者 [Dokuwiki 的解析器](http://dev.splitbrain.org/browse/darcs/dokuwiki/inc/parser)可以被提取出来(稍加修改)——不应该损害 UTF-8，也不应该导致 XHTML 被破坏。还有什么？
*   对于认证来说，OpenID 似乎正在成为一个重要的竞争者——维基百科似乎计划在今年夏天的某个时候支持它(稍后会有更多的介绍)——几乎任何通用语言都有库，尽管不能说 PHP 的东西有多棒。
*   Rasmus 的 [no-MVC MVC](http://toys.lerdorf.com/archives/38-The-no-framework-PHP-MVC-framework.html) 领域中的某个设计，尽管在设计上很有争议(见评论)，但在这里可能有实际意义。
*   对于他提到的 XML 一代， [DOM](http://www.php.net/dom) 还是 [SimpleXML](http://www.php.net/simplexml/) (暗示 PHP 5.x+)？
*   还有什么能让 Tim 相信(或者无意中打消)PHP 呢？

## 分享这篇文章