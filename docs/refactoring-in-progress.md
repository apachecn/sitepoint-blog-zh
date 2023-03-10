# 重构正在进行

> 原文：<https://www.sitepoint.com/refactoring-in-progress/>

目前正在重构 [WACT 的](http://wact.sourceforge.net)模板解析器，[源文件解析器](http://cvs.sourceforge.net/viewcvs.py/wact/wact/framework/template/compiler/sourcefileparser.inc.php)。重构从 1.39 版开始。

SourceFileParser 实际上是 WACT 模板引擎的中心，所以不出错有点重要。正如你在[1.38](http://cvs.sourceforge.net/viewcvs.py/*checkout*/wact/wact/framework/template/compiler/sourcefileparser.inc.php?content-type=text%2Fplain&rev=1.38)版本中所看到的，这个主要问题是 parse()方法，它是一个巨大的循环，处理所有的 XML 事件(目前我们正在使用一个 [XMLPull](http://www.xmlpull.org/) API ),并且在这种过程形式下，几乎不可能修改。因此需要重构…

虽然我拥有 [Refactoring](http://www.amazon.com/exec/obidos/ASIN/0201485672/) ，但不能说我是任何一种重构大师(我还没有从头到尾读完它),所以我试图应用尽可能多的“常识”,做出“小步”而不是大的改变，并对每一步进行彻底的测试。

这是目前为止的进展情况。

–创建了一个类 [TreeBuilder](http://cvs.sourceforge.net/viewcvs.py/wact/wact/framework/template/compiler/treebuilder.inc.php) ，我将 SourceFileParser::parse()中的部分过程转储到相应的静态方法中。

–方法的名称需要尽可能“合理”,以便它们在 SourceFileParser 中出现时“易于阅读”。

–现在我不想担心处理 TreeBuilder 的实例，以及它将使用的任何属性，因此使用静态调用。一旦我完成了静态方法的创建，将它作为 SourceFileParser 中的一个实例(可能)在重构的“第二轮”中出现。

–对于每个新方法，添加一个相应的测试[这里是](http://cvs.sourceforge.net/viewcvs.py/wact/wact/tests/cases/template/treebuilder.test.php)，它与现有的 SourceFileParser 测试相结合，确认在每次更改后一切仍然正常。这种重构的一个好的副作用是，我们现在能够通过单元测试更详细地检查解析。

–现在不太担心 TreeBuilder 的设计。似乎正在成为某种“神类”，可以做任何事情，但是像这样的问题可以等到对 SourceFileParser 的主要更改完成之后。

已经出现的一个有用的优化是消除了 SourceFileParser 中不断出现的代码块。在添加静态方法时不断看到它重复出现，所以现在像 tree builder::handlePlainTagOpen()和 tree builder::handlePlainTagClose()这样的方法将一些工作委托给 TreeBuilder::handlePlainText()。否则，SourceFileParser::parse()方法将慢慢变得可读。

总之，只是一些有趣的笔记。感谢你的想法。从大型过程到许多小型静态方法(然后再到适当的对象等等)的一般演变，虽然忽略了巧妙的 API 设计的问题，但似乎对重构经典的被攻击的脚本相当有效。

## 分享这篇文章