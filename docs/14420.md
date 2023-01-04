# 蛰伏的 Javascript，隐藏的 PHP [3]

> 原文：<https://www.sitepoint.com/crouching-javascript-hidden-php-3/>

它还活着！[http://xmlrpccom.sourceforge.net/scriptserver/](http://xmlrpccom.sourceforge.net/scriptserver/)。

下面是简短的解释…

假设你有一个 PHP 类，看起来像这样；

 `class Math {
function add ($x, $y) {
return $x + $y;
}
}`

你现在可以在 Javascript 中使用它，比如:

 `var m = new math();
alert (m.add(2,2));`

不需要考虑底层机制/编码(如前面[讨论的](https://www.sitepoint.com/blog/))。

一个完整的例子可以看下面；

–[一个“服务器”](http://xmlrpccom.sourceforge.net/scriptserver/scriptserver/examples/postoffice_server.phps)的 PHP 源代码–这是你在服务器端写的东西

–[PHP 服务器自动生成的 Javascript 客户端代码](http://xmlrpccom.sourceforge.net/scriptserver/scriptserver/examples/postoffice_server.php?client)(注意，可以用 PHP 常量的开关“压缩”)

–[客户端](http://xmlrpccom.sourceforge.net/scriptserver/scriptserver/examples/postoffice_client_generated.php)(查看 Javascript 的>源代码)——这些都是你在客户端手工编写的代码。

(更新)
—[来自服务器](http://xmlrpccom.sourceforge.net/scriptserver/scriptserver/examples/postoffice_server.php/colors/listcolors/)的输出 Javascript 客户端从 PHP 获取信息的例子；包含匿名函数的字符串(用于 eval ),匿名函数本身包含一个数组。比 XML 紧凑得多，解析上没有问题(尽管不是很友好。URL 的尾部对应于/类/方法/名称。

从[这里](http://sourceforge.net/project/showfiles.php?group_id=96178&package_id=129197&release_id=266241)下载第一个(alpha)版本中的所有内容。

唷！现在，这是我的系统，可以赶上电子邮件等(凯文谋杀我之前；))

## 分享这篇文章