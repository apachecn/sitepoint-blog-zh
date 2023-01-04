# 来自未来的 PHP

> 原文：<https://www.sitepoint.com/a-php-from-the-future/>

作为开发人员，我们发现自己生活在激动人心的时代。随着人们越来越关注在线活动，我们正在处理更大的数据集(甚至是“大数据”)；可扩展性和连接性比以往任何时候都更加重要；隐私的本质正在被重新审视。但是在这一切的阴影下，一个更实际的问题悄然而至。PHP 将如何改变和发展，以使我们能够建设未来，无论它可能拥有什么？

## 我预测

不一致的字符串函数签名会被规范化吗？我表示怀疑。PHP 会是多线程的吗？可能不会，但也许我们会看到[承诺](http://en.wikipedia.org/wiki/Futures_and_promises)或 [Lua 风格的协程](http://www.lua.org/pil/9.1.html)。如果一个[警察岗亭突然出现](http://en.wikipedia.org/wiki/Doctor_Who)，一个古怪的人探出头来说:“看，我给你带来了一个来自未来的 PHP！”，我们可能会看到什么？我预测:

*   我们将会看到一个拥有两位数次要版本号的 PHP 版本。拥有对 Unicode 编码字符串的完全原生支持当然很好，但是开发团队[几乎已经放弃了](http://tinyurl.com/42eqajo)，所以不要抱太大希望。由于没有明显的变化侵入到足以保证一个主要版本的冲击，我可以很容易地想象一个 PHP 5.10，5.11，5.12，…
*   **我们将看到反映其他编程语言的微小改进。**我们最近在 [PHP 5.4](http://tinyurl.com/d852fxa) 中获得了二进制数字文字、短数组语法和函数数组引用，在 [5.5](http://tinyurl.com/qcf8e6n) 中获得了生成器和`finally`。Python 风格的数组切片将会受到欢迎，同样受 C#启发的 [get/set 属性语法](https://wiki.php.net/rfc/propertygetsetsyntax-as-implemented)也会受到欢迎。
*   生态系统将会有更少的框架，更多的元框架。首先是图书馆，然后是 CMS 和博客平台，然后是全功能框架。整体平台的趋势已经达到顶峰，我们正在用微框架和元框架缩小规模。轻量级平台将不同的库集合到一个易于安装的包中，这将给我们带来一个完整的循环。
*   将会有过多的新扩展。像 [Zephir](http://blog.phalconphp.com/post/57161129440/phalcon-2-0-the-future) 这样的新工具让非 C 开发人员编写扩展变得更加容易，所以随着这些产品的成熟，预计会看到大量新的扩展。当然，现在 Pecl 中的一些扩展可能会成为核心，旧的扩展将会被淘汰。[嵌入式 NoSQL](http://unqlite.org/) 服务将很好地补充 SQLite 扩展，你不这样认为吗？
*   对 Zend 引擎的依赖将会减少。大多数人都不知道 PHP 的其他实现，比如[法兰格](http://www.php-compiler.net/)、[栎](http://quercus.caucho.com/)、 [HHVM](http://www.hhvm.com/blog) ，甚至[鹦鹉](http://www.parrot.org/)。未来，各种运行时间，每一个都针对特定的用例进行了优化，可以根据需要进行交换[看起来并不难以置信](https://wiki.php.net/rfc/php_native_interface)。
*   PHP 会适应。越来越多的移动设备， [C10k](http://en.wikipedia.org/wiki/C10k_problem) ，网络插座——是的，我们使用网络的方式正在改变。像 [FastCGI/FPM](http://php.net/manual/en/install.fpm.php) 、HHVM 和[棘轮](http://socketo.me/)这样的产品只是 PHP 正面应对这些挑战所需的冰山一角。但是我们也可能在意想不到的地方找到 PHP。路由器、智能设备和其他设备的情况如何？有了内置的网络服务器和嵌入式数据库，*AMP 栈现在可以拼写成 PHP。发布一个包含更少核心扩展/功能和专门运行时的版本可以让嵌入式 PHP 成为现实。
*   **PHP 将会蓬勃发展。**尽管有缺点，但没有任何主流语言能像 PHP 那样紧密、灵活地集成到 web 堆栈中。我们可以使用`$_GET`和`$_POST`直接访问传入的数据，并且处理会话和头的功能已经嵌入其中。像 Java、Ruby、Python 等语言需要特殊的框架或库来轻松完成这些事情。批评者可以尽情抱怨，但另一种语言不会很快取代 PHP，除非它能同样顺利地集成。

## 你的想法？

但是消失的蓝盒子并不存在，没有人真正知道未来会发生什么。我只是试图观察事情的总体趋势，并保守地估计我们可能会在哪里结束。也许一些预测有价值；也许有些是可笑的。你怎么想呢?

我将留给你一句我最喜欢的与未来学相关的名言:

> 预测未来的最好方法是创造未来。–[艾伦·凯](http://en.wikipedia.org/wiki/Alan_Kay)

欢迎在下面的评论区发表你的想法。

## 分享这篇文章