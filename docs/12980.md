# 一个 PHP 开发人员对 Python 的看法

> 原文：<https://www.sitepoint.com/a-php-guys-look-at-python/>

尽管困难重重，本周我还是找到了一点空闲时间。我没有做一些明智的事情，比如打扫车库或做一些锻炼，而是抓住机会学习了一门新的编程语言:Python。

像许多 SitePoint 读者一样，我开始接触 PHP。这些年来，我已经变得非常适应了，不管是缺点还是缺点。PHP 仍然是一个可靠的选择，但是 PHP 最近没有太大的变化。与此同时，我一直在开发的应用程序的规模和复杂性都在急剧增长。

Python 和 PHP 有很多共同之处:它是一种动态类型的开源脚本语言，拥有优秀的文档和蓬勃发展的社区。这两种语言在处理 Unicode 文本时也有点古怪。

与 PHP 不同，Python 最初并不是作为 Web 开发语言而设计的——它是一种通用编程语言，只是碰巧拥有一些用于构建网站的优秀库和框架，如 [Django](http://www.djangoproject.com/) 。这听起来像是*对* Python 的争论，但事实证明，当你开始编写更大的 web 应用程序时，你的大部分代码与 HTML 无关，PHP 的 HTML 友好特性似乎只是碍事。

## 挥手够了！代码是什么样的？

让我们看看 Python 代码的一些简洁的特性。

Python 代码最显著的特点是代码块周围没有大括号或其他分隔符。相反，Python 使用代码的缩进来表示块:

```
print "Let's count to ten!"
for i in range(10):
  number = i + 1
  print number
print "All done!"
```

上面的代码包括一个将运行十次的`for`循环。跟随在`for`语句之后的两行被缩进以表明它们在`for`循环中。最后一行没有缩进，所以 Python 知道`for`循环结束了。

起初，相信缩进来描述你的代码结构是非常脆弱的；但是一旦你习惯了，你会发现你的代码看起来不那么混乱了。更好的是，你会发现阅读别人写的 Python 代码更容易，因为开发人员被迫整齐一致地缩进他们的代码。

Python 有许多方便的小特性，与其他语言相比，这些特性使得常见的任务不那么麻烦。以多重赋值为例，这样可以避免创建临时变量:

```
a = 1
b = 2
print a, b     # prints '1 2'
a, b = b, a+b
print a, b     # prints '2 3'
```

Python 也有许多灵活的特性来处理它所谓的序列。这些让您可以使用简单、一致的语法来拆分和组合列表(相当于 PHP 数组)和文本字符串，而不必记住每个列表和文本字符串的晦涩的函数名。

Python 的 list comprehensions 允许您用最少的代码从简单的列表中快速构建复杂的列表。乍一看，它们有点难以理解，但很快就变得不可或缺:

```
me = 'Kevin Yank'
range(5)                                 # [0, 1, 2, 3, 4]
[me[x] for x in range(5)]                # ['K', 'e', 'v', 'i', 'n']
[x for x in range(10) if x % 2 == 0]     # [0, 2, 4, 6, 8]
[me[x] for x in range(10) if x % 2 == 0] # ['K', 'v', 'n', 'Y', 'n'] 
```

但是除了那些语法上的甜言蜜语之外，在多年编写 PHP 代码之后，我所欣赏的 Python 的最大特点是它明智的模块和包系统，用于将代码组织到多个文件中。在 PHP 中，当一个脚本`include` s(或`require` s)调用另一个脚本时，它会冒着自己的变量、函数和类名被包含的脚本破坏的风险。

Python 自动为程序导入的每个文件(称为模块)建立一个单独的名称空间，这样自然就避免了命名冲突。这让您可以选择更短、更自然的名称(特别是对于大型项目中的类)，同时迫使您对代码的结构给予更多的考虑。

## 从哪里开始？

除非你有使用一门新语言的理由，否则学习这门语言没有什么意义。在过去的几年里，许多 web 开发人员的理由是 Django。因此，最好的起点可能是 SitePoint 的 [Django Djumpstart](https://www.sitepoint.com/build-to-do-list-30-minutes/) 的文章。

最近，许多开发者对 Python 重新产生了兴趣，因为它是谷歌应用引擎的开发语言。在深入研究 Python 之前，有必要看一看 App Engine。

说到深入了解 Python，Mark Pilgrim 的书[深入了解 Python](http://www.diveintopython.org/) 可以免费在线阅读，这是从头开始学习这门语言的好方法。如果你在寻找更有深度和细节的东西，那么[官方 Python 教程](http://docs.python.org/tut/tut.html)是值得一读的，尽管有点枯燥。它也明显更新了。

## 分享这篇文章