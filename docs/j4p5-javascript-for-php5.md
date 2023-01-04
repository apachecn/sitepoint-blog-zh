# j4p 5:PHP 5 的 Javascript

> 原文：<https://www.sitepoint.com/j4p5-javascript-for-php5/>

需要分散注意力吗？检出[j4p 5](http://j4p5.sourceforge.net/)；

> J4P5 是一个用 PHP 5 编写的 JavaScript 解释器，允许在服务器的沙箱中运行不可信的脚本。它旨在实现 Ecma-262 3d 版的大部分功能。

我不骗你。事实上，这看起来是一个非常严肃的尝试。你需要自己下载并运行。里面有 Javascript 的语法规则和用 PHP 编写的 Javascript 运行时。它通过了“有效”测试——这些例子直接来自`$ unzip`。看看它做了什么和待办事项列表，除了 Unicode 问题之外，J4P5 已经完成了 50%以上。

除了新奇之外，一个真实的用途可能是对简单测试的网络测试能力的扩展。只要做一点工作，就有可能创建一个令人敬畏的 Javascript 源代码压缩器——词法分析模式都在那里。

还需要更多的分心？试试 J4P5 从哪个[metaptp](http://metaphp.sourceforge.net/)得到它的[解析器生成器](http://metaphp.sourceforge.net/index.php?page=tow)。相当什么是 metaphp(是？)试图实现我不确定——似乎是试图在 PHP 的基础上构建一个更高级的语言，其中包含一些 ORM 和函数式编程(下载中的 FP 内容非常有趣)。总之——它产生了一个解析器生成器，足以实现一个 Javascript 解析器。

有人想要 4P5 吗？；)

## 分享这篇文章