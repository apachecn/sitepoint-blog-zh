# PHP 中的 Python _ _ name _ _ = = _ _ main _ _。

> 原文：<https://www.sitepoint.com/python-name-main-in-php/>

[Python](http://www.python.org) 的一个很好的特性，标志着它是一种为开发人员设计的语言(相对于为工具供应商设计的语言),如果你正在直接执行一个脚本，它就能够有条件地运行一些逻辑，但是如果这个脚本是由另一个脚本导入的，就可以忽略它。

比如说；

 `class User:
def setName(self, name):
self.name = name
def getName(self):
return self.name`

`# Only execute this we're "main"...
if __name__ == "__main__":
u = User()
u.setName("Harry")
print u.getName()`

只执行' if __name__ == "__main__:'之后的代码是我直接运行这个脚本。如果我从另一个脚本导入用户类，它会被忽略。

成为测试(或者更好的单元测试)Python 脚本的非常方便的工具。以至于我想用 PHP 写出来。

这样做的一个技巧是这样的:

 `< ?php class User { var $name; function setName($name) { $this->name = $name;
}
function getName() {
return $this->name;
}
}`

`if ( realpath(__FILE__) == realpath($_SERVER['SCRIPT_FILENAME']) ) {
$u = & new User();
$u->setName("Harry");
print $u->getName();
}
?>`

这依赖于[预定义的常量](http://www.php.net/manual/en/language.constants.predefined.php) __FILE__，该常量填充了访问 __FILE__ 的脚本的名称(即，它与包含无关)。据我所知，__FILE__ 的值对于脚本应该是唯一的。

但是这里有两个问题。首先，如果您经常使用这种方法，将会有大量对 [realpath()](http://www.php.net/realpath) 的调用，这意味着性能下降。另外＄_ SERVER[' SCRIPT _ FILENAME ']在通过 CGI 使用 PHP 时返回 PHP 可执行文件的名称，否则可能无法依赖于除 Apache 之外的 web 服务器。

另一种方法是使用 __FILE__ 的值有条件地定义一个常数，例如；

 `class User {
// ....
}`

`if ( !defined('__MAIN__') ) {
define ('__MAIN__',__FILE__);
}
if ( __FILE__ == __MAIN__ ) {
$u = & new User();
$u->setName("Harry");
print $u->getName();
}`

如果常量 __MAIN__ 还没有被定义(由包含这个常量的脚本定义)，它将被赋予 __FILE__ 的值，并执行条件代码块。

这种方法可能是最值得信赖的，因为它应该适用于任何 PHP 安装，但需要额外的工作，如果您忘记在某个地方定义 __MAIN__，您可能会意外地执行代码。

请注意以下事项:

 `@define ('__MAIN__',__FILE__);`

如果已经定义了 __MAIN__ 的话，用于抑制错误通知的 [@操作符](http://www.php.net/manual/en/language.operators.errorcontrol.php)比使用 [is_defined()](http://www.php.net/is_defined) 要慢。

第三个选项是使用[get _ included _ files()](http://www.php.net/get_included_files)like，我不确定我 100%相信它在任何地方都能正确工作；

 `class User {
// ...
}`

`if ( __FILE__ == array_shift(get_included_files()) ) {
$u = & new User();
$u->setName("Harry");
print $u->getName();
}`

这是基于这样的假设:开始执行的文件将总是 get_included_files()返回的列表中的第一个元素(据我所知应该是这样)，并且它将匹配 __FILE_ 的值。只要这个脚本没有被任何其他脚本包含(或要求)，if 条件中的代码就会被执行。

当然，整体方法需要在条件中解析代码，这只能通过使用像 Zend Accelerator 这样的操作码缓存来避免，但当您构建模块化代码并希望在不运行整个应用程序的情况下开发和测试应用程序中的“模块”时，它是一个有用的工具。

## 分享这篇文章