# 开放存储库给 JavaScript 带来了模块化

> 原文：<https://www.sitepoint.com/open-repository-brings-modularity-to-javascript/>

现在还为时尚早，但是一群 Perl 黑客已经联合起来组成了 JavaScript 存档网络 [JSAN](http://www.openjsan.org/) 。像久负盛名的 Perl 对等物，，以及其他借鉴灵感的网站(如 [PEAR](http://pear.php.net) for PHP)，JSAN 的目标是成为一个开放的 JavaScript 脚本库，这些脚本以模块化的方式设计，具有可管理的依赖性，鼓励代码重用。

实际上，这意味着你可以选择一个特定的模块，在你的网站上提供你想要的功能，JSAN 系统不仅会自动下载并安装该模块的文件，还会自动下载并安装该模块所依赖的任何模块的文件。将文件安装到您站点的目录结构中后，您就可以在自己的脚本中使用它们提供的功能了。

由于 JSAN 是由 Perl 黑客构想的，用于下载和安装 JSAN 模块的自动化系统自然是用 Perl 编写的。如果你是一名开发 Windows box 的开发人员，我很同情你刚刚发出的抱怨。如果这能安慰你的话，有传言称“另类 JSAN 客户”正在酝酿中，所以还是祈祷吧。同时，在安装和使用 JSAN 之前，您需要安装 Perl 并熟悉操作系统的命令提示符。

在这个阶段，文档很早就不存在了，但是任何熟悉 CPAN 或 PEAR 并精通 JavaScript 的人都应该能够根据已经存在的新生的[安装指南](http://www.openjsan.org/documentation/)来解决这个问题。一旦安装，使用单个模块的文档以 CPAN 使用的[标准格式](http://search.cpan.org/~nwclark/perl-5.8.7/pod/perlpod.pod)提供在脚本文件中。

除了依赖跟踪和脚本的自动安装，JSAN 对如何编写脚本几乎没有限制。事实上，通过浏览到站点上适当的脚本文件并直接下载它，可以很容易地获得独立的模块——不需要 Perl 客户机。然而，最初的模块库显然倾向于使用 JavaScript 类作为名称空间的不可信脚本，如在 [DHTML Utopia](https://www.sitepoint.com/books/dhtml1/) 中所展示的。

如果你使用官方客户端，你将受益于标准化的目录结构，这将允许你使用 JSAN 内置的脚本导入机制。我第一次在 [jsolait library for XML-RPC](http://jsolait.net/examples/xmlrpc/) 中看到的一个技巧是，系统允许一个脚本动态加载它需要的另一个脚本，而不是要求 Web 开发人员在网站的 HTML 代码中加载一个单独的

## 分享这篇文章