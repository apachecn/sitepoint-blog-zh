# PHP6 获得一个 COMEFROM 语句

> 原文：<https://www.sitepoint.com/php6-gets-a-comefrom-statement/>

PHP6 中一个更有争议的新增功能是 [GOTO](http://php.net/~derick/meeting-notes.html#adding-goto) 。有些人认为[违背了多年来良好的编程意识，但是有一些有效的用例，当运行诸如解析之类的性能关键操作时，调用 PHP 用户函数的开销变得很大。](http://en.wikipedia.org/wiki/GOTO#Criticism)

总之`GOTO`似乎已经打开了一些更激进的语言修改的大门，最近的讨论a 围绕着给 PHP 添加一个 [COMEFROM](http://marc.theaimsgroup.com/?l=php-dev&m=111805996014466&w=2) 语句，这导致一个初始补丁被应用到 PHP6 CVS 分支[这里](http://news.php.net/php.cvs/37564)。

在 [INTERCAL](http://www.ofb.net/~jlm/intercal.html) 中可以找到`COMEFROM`的先例，由于各种原因，这种语言从未成为主流，但旨在成为更好的 [LISP](http://en.wikipedia.org/wiki/Lisp_programming_language) ，正如[这段代码清单](http://www.ofb.net/~jlm/replicate.i)所示。

和`GOTO`一样，`COMEFROM`如果使用不当会导致 spagetti，所以最初的实现对它的使用进行了合理的限制:你只能`COMEFROM`一个是*的 PHP 脚本，而不是*使用了`COMEFROM`的脚本。一个例子，如果我有一些包含文件一样；

```
 <?php
// login.php - the script we want to COMEFROM
function login($username, $password) {
   $auth = new Auth();
   return $auth->isValidUser($username, $password);
} 

```

我可以带着这样的勇气走进这个世界；

```
 <?php
// index.php
require_once 'login.php';

class MyAuth implements LoginHandler {
    // Some other implementation providing the same interface
    function login($username, $password) {
        // authenticate against, say, an LDAP server
    }
}

// Execute a COMEFROM, replacing use of Auth class with MyAuth
COMEFROM 'login.php:3' [
   $auth = new MyAuth();
   return $auth->isValidUser($username, $password);
]

if ( login($_POST['username'], $_POST['password'] )) {
   echo "You are logged in<br />";
} 

```

如上面的代码所示，这使您有可能覆盖代码的行为，而不必物理地改变它或担心像动态包含这样的事情。这个小例子没有说明的是一些更高级的东西`COMEFROM`支持的，比如函数式编程、宏和类似 Ruby 的“块”,所有这些都使 PHP 变得更加动态。也就是说，目前，我认为缺少的是一种返回到你拥有的代码中的方法，这将允许像极快的 AOP 实现这样的东西——也许`GOTO`可以被扩展以支持这一点？

无论如何——这会是驱动主机迁移到 PHP 的需求的特性吗？

## 分享这篇文章