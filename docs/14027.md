# PHP 4.4 小问题

> 原文：<https://www.sitepoint.com/php-44-minor-gotcha/>

在 PHP 4.4 发布后的几天里，观察围绕该版本引入的可能的向后兼容性问题的讨论是很有趣的。评论者似乎对这是否真的破坏了向后兼容性产生了分歧。

“向后兼容中断”实际上是 PHP 错误处理代码生成的一个新的[通知](http://php.planetmirror.com/manual/en/ref.errorfunc.php#e-notice)，当试图通过引用返回临时变量时警告开发人员。该通知似乎是为了响应 PHP 4.3 在引用处理方面的一个错误而添加的。

今天，当我把家里自己的开发服务器升级到 PHP 4.4 时，我遇到了这个问题。我正在开发的一个应用程序运行在自己的“调试”模式下，通过将通知回显到输出中，使得通知*真正可见。*

元凶:

> 注意 D:htdocsaaareasourcesincludes controls . Inc . PHP 中的第 14 行:只有变量引用应该通过引用返回

有问题的行属于以下方法:

 `function &getnodecontrol($objecttype)
{
require_once(RESOURCE_DIR . "nodecontrols/$objecttype.inc.php");
return new $objecttype($this->db);
}`

这里的问题是“new $objecttype($this->db)”不是一个变量——它是“new”语句的结果——实际上是一个“临时”变量 PHP 不能创建对它的引用。当变量通过引用返回时，唯一可以返回的是一个已经初始化的变量。

正如 PHP 手册将它放在[返回引用](http://www.php.net/manual/en/language.references.return.php)下面:

> 注意:如果你尝试从一个函数返回一个引用，语法为:return($ found _ var)；这将不起作用，因为您现在试图通过引用返回表达式的结果，而不是变量。你只能通过引用从一个函数返回*个变量*,除此之外别无他法。

问题得到了解决，通知得到了加强，代码如下:

 `function &getnodecontrol($objecttype)
{
require_once(RESOURCE_DIR . "nodecontrols/$objecttype.inc.php");
$control = new $objecttype($this->db);
return $control;
}`

这里，“new $objecttype($this->db)”的结果存储在一个变量中，并返回对该变量的引用。

eZ Systems 的这篇[文章更详细地描述了这一变化。](http://ez.no/community/articles/reference_issues)

这种行为的改变受到了一些人的批评，他们的申请受到了这种改变的影响。一个例子是这个关于网络邮件客户端 Horde 问题的[消息。](http://archives.free.net.ph/message/20050712.144720.60af96bc.en.html)

作为回应，其他人指出无论如何都不应该在生产环境中启用通知，因此这种改变不应该引起任何问题。

Zend 周刊摘要(#245) 提供了一个有趣的关于部落问题的简明摘要。

有趣的是，这个讨论链接到了 PHP 4.4 中的一个更新的 bug ，它涉及到*通过引用传递*。它暗示可能需要 PHP 4.4 的未来更新来修复它，并且该更新可能引入新的警告和/或破坏向后兼容性。

## 分享这篇文章