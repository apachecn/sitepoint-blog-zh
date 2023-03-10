# 用 ASPA 将 ASP 转换成 PHP

> 原文：<https://www.sitepoint.com/convert-asp-to-php-with-aspa/>

如果你对 php 有一点了解，你可能以前遇到过 [asp2php](http://asp2php.naken.cc/) ，这是一个试图将 ASP 3.0 代码(主要是 VBScript)自动转换成 PHP 的应用程序。我之前已经[提到过](https://www.sitepoint.com/is-php-the-natural-upgrade-path-for-asp-30-vb-6/)和[拿这个东西开玩笑过](https://www.sitepoint.com/asp-users-flock-to-php-in-droves/)，但是最近一直在玩一些看起来很严肃的东西。

虽然 asp2php 是一个勇敢的项目，我也不想批评它，但从过去的实验来看，我发现它并不令人信服。是的，它提供了类似 PHP 的东西，但真正的问题是仍然要做什么——基本上是 80/20 的事情。虽然这是任何这类工具都应该有的，但问题是除了修改 C 源代码之外，没有办法控制 get 生成了什么。

不久前，当我在寻找能够解析 PHP的工具时，遇到了由 Anakreon Mejdi 开发的[aspA](http://storm.cs.unipi.gr/~anakreon/aspa.html):“ASPA 试图将 ASP 页面翻译成 PHP 的过程自动化。支持的语言是 JScript 和 VbScript。。

ASPA 是用 Java 编写的，建立在一个成熟、开源的[解析生成器](http://en.wikipedia.org/wiki/Compiler-compiler)框架 [ANTLR](http://www.antlr.org/) 之上。在我看来，它的特别之处在于，通过运行时(转换期间)加载的 XML“映射”文件，控制输出 PHP 相当容易。你会发现这里描述了一点(PDF)。

和 ASPA 玩了一会儿后，我有一些想法。

–预计必须花时间安装–这里有常见的 Java 问题需要处理，包括[和](http://ant.apache.org/)–我需要重建 ASPA。在这里，我只给出基于 WinXP 的“对我有效”的提示。

–我用的是 ASPA 的 1.2 版本。它缺少各种依赖项，即 antlr、log4j、dom4j 和 commons-collections，Anakreon [描述了这些。获得它们的一个快速方法是下载 ASPA 的 1.1 版本，它们都在`./lib`子目录中——只需复制 1.2 版本的`./lib`子目录。但是缺少的是 Jaxen `.jar`，它也需要进入`./lib`目录——你可以从这里得到一个构建版本](http://storm.cs.unipi.gr/~anakreon/aspa.html)

–如果您查看`./etc/log4j.properties`文件，您会发现它标识了一个类似于`log4j.appender.FILE.File=logs/log.txt`的日志文件–这是相对于基本 ASPA 目录的，您需要确保该文件/目录存在。一旦用 ant 构建了 ASPA，`aspa-1.2/build/classes/gr/omadak/leviathan/asp/log4j.properties`文件就可以用来控制每次转换时的日志记录量

–随着 ASPA 的建立，XML 翻译文件可以在`aspa-1.2/build/classes/gr/omadak/leviathan/asp/objects`找到。我*相信*(未经测试)你可以在这里创建新的 XML 文件，文件名对应于你想要转换成 PHP 的 ASP / ActiveX 对象，如这里[所述](http://www.antlr.org/workshop/ANTLR2004/proceedings/XMLAsASTTemplates.pdf) (PDF)。虽然 ASPA 在这里做的是一个很好的开始，但我觉得要真正赋予用户能力，需要像 Rhino 或 Jython 这样的东西，并结合从 ASPA 获取解析时间信息。

–为了进行测试，我选择了第一个 ASP 博客应用程序，发布在 GPL 下，我可以通过 Google 找到——Ublog。为了比较，我将结果[和 ASPA 日志文件一起放在这里](http://www.phppatterns.com/lib/exe/fetch.php/develop/ublog_php.zip?id=develop%3Axul_to_phpgtk&cache=cache)。我没有修改它，也没有对输出做任何重大的调整。我想基本上是在 80%的水平。如日志中所述，有些文件转换失败。其他事情，比如调用`require`命名一个`.asp`，它将被转换成一个`.php`文件，因此不再存在。与此同时，一些想法需要深入到所有这些`print`声明的 [XSS](http://en.wikipedia.org/wiki/Cross-site_scripting) 含义中。需要思考的是 API 是否以最聪明的方式翻译，例如，ASPA 准备与约翰·林的 ADODB 一起使用的 DB 调用，但是看起来大约 90%是 API 方式的。但是繁重的工作已经完成了——最后的 20%是可行的，通过尽可能多地将数据放入 XML 翻译文件，有可能降低到 5%或更低。

总的来说，ASPA 看起来很像处于“可行”阶段，但现在需要以“可行”为目标。特别是，修改 PHP 输出的能力相当容易，这标志着它的重要性，如果可以将一个易于安装的二进制发行版放在一起，或者更好的是，如果将一个 GUI 放在顶部来帮助管理转换(例如，XML 翻译文件对 ASPA 来说是“全局的”，但它们的使用可能是特定于应用程序的)，那么这里可能有一个重要的“大众”工具。以目前的形式，我想这将会对那些对 PHP 版本的代码感兴趣的 ASP 开发者有很大的帮助。也有机会将某种 PHP 框架注入其中，作为目标输出 API——一个支持常见 ASP 习惯用法的 API。也许这些成群结队的事情有一天会发生；)

让我来到 ASPA 的是寻找能够解析 PHP 脚本的工具，做一些类似 PHP lint 的事情。有趣的是，几乎没有人[真正“理解”](http://www.antlr.org:8080/pipermail/antlr-interest/2004-December/010569.html) PHP，当他们理解时，似乎会导致人们想要保留结果[关闭源代码](http://www.php-compiler.net/)。除了像 [phpxref](http://phpxref.sourceforge.net/) 这样的手工编码的东西，两个例外是 [scintilla](http://www.scintilla.org/) (你可以通过 [SilverCity](http://sourceforge.net/projects/silvercity/) 或 [wx 在 Python 中使用它。STC](http://www.yellowbrain.com/stc/index.html) 但是令牌非常通用)和 [spident](http://spindent.paneris.net/) (这比我想要提交的 Java 要多)。

## 分享这篇文章