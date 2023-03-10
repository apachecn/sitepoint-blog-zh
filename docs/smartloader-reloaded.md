# 智能加载程序重新加载

> 原文：<https://www.sitepoint.com/smartloader-reloaded/>

几个月前，我[提出了](https://www.sitepoint.com/whats-your-plan-for-autoload/)一种有效使用 [__autoload()](http://www.php.net/autoload) 和类索引器的方法。令我惊讶的是，相当多的人开始使用它，并向我提供错误报告。我重写了大部分原始代码，消除了所有已知的错误，并在过去的一个月里一直在测试它。所以这里有一个[的新版本](http://svn.students.ch/smartloader-public/trunk/)，有一些改进:

*   增加了扫描目录的能力
*   将 SPL 递归目录运算符用于索引器/扫描器
*   优化的 SmartLoader::load()。现在，在我的机器上完成(包括)只需不到 1 毫秒
*   通过 DIRECTORY_SEPARATOR 常量的 Windows 支持
*   索引文件头中的更多信息，如:

```
 * Created by:    /usr/local/php5/lib/php/SmartLoader/SmartLoader.class.php
    * Created at:    Thu, 06 Apr 2006 23:54:48 +0200
    * Scanned:       134 directories and 402 files in 1.15 seconds. 
```

*   修正了各种错误

**向后兼容？**

罗伯特·施梅尔泽[挖出了](https://www.sitepoint.com/whats-your-plan-for-autoload/#comment-15485)最引人注目的漏洞。从 PHP 5.1.2 开始，这段代码不再有效:

```
 preg_match_all('/hand/', 'Talk to the hand!', $result = array())); 
```

除非我删除“= array()”，否则$result 变量将为空。为什么？

当然，人们可能会争论 array 初始化结果有多大意义。从我的角度来看，这是通过显示我传递了一个要填充的空数组来使代码更具可读性。另一方面，我想吴镇男会称之为“做某事[傻](http://derickrethans.nl/questions.php)”。；)

**如何使用**

使用 SmartLoader 的最好方法是将 [SVN 主干的内容](http://svn.students.ch/smartloader-public/trunk/)放入您的 include 目录中(不要忘记 SmartLoader/indexfiles 的写权限)。之后，您可以通过添加这两行来轻松地在脚本中设置它:

```
 require('smartloader.php');
SmartLoader::addIncludeDir(dirname(__FILE__).'/../'); /* (or just the document root) */ 
```

我强烈建议在你的类前面加上前缀，尤其是在使用方便的包含目录扫描时，否则你会遇到名称冲突。也许 [PHP 名称空间](http://www.petitiononline.com/phpns/petition.html)会在某个时候解决这个问题。

## 分享这篇文章