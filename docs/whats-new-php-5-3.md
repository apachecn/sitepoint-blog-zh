# PHP 5.3 有什么新特性？文章

> 原文：<https://www.sitepoint.com/whats-new-php-5-3/>

PHP 6 即将推出，但是对于那些迫不及待的开发者来说，有一个好消息——许多原计划用于 PHP 6 的特性已经被移植到 PHP 5.3，最终的稳定版本将于今年上半年发布。

这个消息可能也会受到那些希望使用一些新功能，但其主机提供商在一段时间内不会升级到版本 6 的人的欢迎——主机提供商通常会在执行验收测试时推迟主要版本的更新(阅读:稳定性已在其他地方首先得到证明)。许多托管公司可能会推迟升级他们的服务，直到 6.1 版本发布。然而，从 5.2.x 到 5.3 的小升级对大多数主机公司来说不是什么障碍。

本文介绍了这些新特性，给出了它们可能有用的示例，并提供了演示代码来帮助您轻松地启动和运行。它不包括安装 PHP 5.3 这样的主题 PHP 5.3 的最新开发版本[目前已经推出](http://snaps.php.net/)。如果你想玩这篇文章中的代码，你应该安装 PHP 5.3，然后[下载代码档案](https://www.sitepoint.com/examples/php5/PHPdemos.zip)。一篇关于安装 PHP 5.3 的文章可以在墨尔本 PHP 用户组网站上找到。

##### 名称空间

在面向对象 PHP 出现之前，许多应用程序开发人员使用冗长的函数名来避免名称空间冲突。比如 WordPress，实现了`wp_update_post`和`wp_create_user`等功能。`wp_ prefix`表示该功能属于 WordPress 应用程序，减少了与任何现有功能冲突的机会。

在面向对象的世界中，名称空间冲突不太可能发生。考虑下面的示例代码片段，它基于一个虚构的博客应用程序:

```
<?php 

class User { 

  public function set( $attribute, $value ) { ... } 

  public function save() { ... } 

} 

$user = new User(); 

$user->set('fullname', 'Ben Balbo'); 

$user->save();
```

在本例中，save 方法不会与任何其他方法冲突，因为它包含在 User 类中。但是仍然有一个潜在的问题:用户类可能已经由系统的其他部分定义了，例如，如果博客应用程序在内容管理系统中运行。这个问题的解决方案是使用新的名称空间关键字。再次使用上面的代码，考虑下面的示例文件:

```
<?php 

namespace MyCompany::Blog; 

class User { 

        public function set( $attribute, $value ) { 

                $this->$attribute = $value; 

        } 

        public function save() { 

                echo '<p>Blog user ' . $this->fullname . ' saved</p>'; 

        } 
```

}

$ User = new my company::Blog::User()；
$user- > set('fullname '，' Ben Balbo ')；
$ user->save()；

从表面上看，命名空间给我们的函数带来的好处可能并不明显——毕竟，我们只是简单地将`MyCompany_Blog_User`改成了`MyCompany::Blog::User`。然而，我们现在可以使用不同的名称空间为 CMS 创建一个`User`类:

```
<?php 

namespace MyCompany::CMS; 
```

类别用户{

公共函数集($attribute，$ value){
$ this->$ attribute = $ value；
}

公共函数 save() {
echo ' < p > CMS 用户'。$this- >全名。保存的</p>'；
}

}

我们现在可以使用类`MyCompany::Blog::User`和`MyCompany::CMS::User`。

##### `use`关键字

使用完整的名称空间对类进行寻址仍然会导致冗长的调用，如果您使用来自`MyCompany::Blog`名称空间的大量类，您可能不希望每次都重新键入类的完整路径。这就是`use`关键字派上用场的地方。您的应用程序很可能在任何给定的时间使用许多不同的类。比方说，用户创建了一个新帖子:

```
<?php 

use MyCompany::Blog; 

$user = new Blog::User(); 

$post = new Blog::Post(); 

$post->setUser( $user ); 

$post->setTitle( $title ); 

$post->setBody( $body ); 

$post->save();
```

`use`关键字不限于定义工作的名称空间。您还可以使用它将单个类导入到您的文件中，如下所示:

```
<?php 

use MyCompany::Blog::User; 

$user = new User();
```

##### 命名空间别名

前面，我指出命名空间的一个优点是能够在不同的命名空间中用相同的名称定义多个类。显然会有这两个类被同一个脚本使用的情况。我们可以只导入名称空间，但是，我们也可以选择只导入类。为此，我们可以使用名称空间别名来标识每个类，如下所示:

```
<?php 

use MyCompany::Blog::User as BlogUser; 

use MyCompany::CMS::User as CMSUser; 
```

$ blog user = new blog user()；
$bloguser- > set('全名'，'无名氏')；
$ bloguser->save()；

$ CMS user = new CMS user()；
$cmsuser- > set('全名'，'无名氏')；
$ CMS user->save()；

##### 类别常数

常量现在可以在类级别定义了！注意，当您导入名称空间时，类常量是可用的，但是您不能导入常量本身。下面是我们如何使用它们的一个例子:

```
<?php 

namespace MyCompany; 
```

class Blog {
const VERSION = ' 1 . 0 . 0 '；
}

回声'< p >博客。我的公司::博客::版本。</p>’；

使用 my company::Blog；
echo ' < p >博客版'。博客::版本。</p>’；

使用 MyCompany::Blog::VERSION 作为 Foo
echo ' < p >博客版'。福。</p>’；
这将导致以下输出:
博客版本 1.0.0
博客版本 1.0.0
博客版本 Foo

##### 命名空间函数

在我们现在生活的面向对象的世界中，静态类方法的使用已经取代了函数的使用。然而，如果你确实需要在你的包中添加一个函数，它也将受到命名空间的限制！

这里有一个例子:

```
<?php 

namespace bundle; 

function foo() { echo '<p>This is the bundled foo</p>'; } 

foo(); // This prints 'This is the bundled foo' 
```

函数 foo() { echo ' < p >这是全局 foo</p>'；}
要求(' lib/bundle . class . PHP ')；
bundle::foo()；// This 打印‘这是捆绑的 foo’
foo()；//这会打印“这是全局 foo”

##### 全局名称空间

在处理函数时，全局名称空间是一个重要的考虑因素。在前面的例子中，您会注意到没有直接的方法从包代码中调用全局`foo`函数。

解析函数调用的默认方法是使用当前的命名空间。如果找不到该函数，它将查找具有该名称的内部函数。它不会自动在其他名称空间中查找。

为了从包名称空间中调用全局`foo`函数，我们需要直接定位全局名称空间。我们通过使用双冒号来做到这一点:

```
<?php 

namespace bundle; 

function foo() { echo '<p>This is the bundled foo</p>'; } 

foo(); // This prints 'This is the bundled foo' 

::foo(); // This prints 'This is the global foo'
```

##### 自动加载命名空间类

如果您正在定义神奇的`__autoload`函数来按需包含类定义文件，那么您可能正在利用一个包含所有类文件的目录。在使用名称空间之前，这种方法就足够了，因为每个类都需要有一个惟一的名称。不过，现在有可能有多个同名的类。

幸运的是，`__autoload`函数将被传递给该类的完全命名空间引用。因此，在上面的例子中，您可能会遇到这样的调用:

```
__autoload( 'MyCompany::Blog::User' );
```

现在可以对该参数执行字符串替换操作，将双冒号转换为另一个字符。最明显的替代是目录分隔符:

```
function __autoload( $classname ) {  

        $classname = strtolower( $classname );  

        $classname = str_replace( '::', DIRECTORY_SEPARATOR, $classname );  

        require_once( dirname( __FILE__ ) . '/' . $classname . '.class.php' );  

}
```

这将采用上面预期的调用，并包括文件`./classes/mycompany/blog/user.class.php`。

##### 后期静态绑定

后期静态绑定为父类提供了使用已在子类中被重写的静态方法的能力。您可能认为这是默认行为，但请考虑以下示例:

```
<?php  

class ParentClass {  

        static public function say( $str ) {  

                self::do_print( $str );  

        }  

        static public function do_print( $str ) {  

                echo "<p>Parent says $str</p>";  

        }  

}  

class ChildClass extends ParentClass {  

        static public function do_print( $str ) {  

                echo "<p>Child says $str</p>";  

        }  

}  

ChildClass::say( 'Hello' );
```

你可能希望这个返回“孩子说你好”。虽然我理解你为什么会期望这样，但看到它返回“家长问好”你会失望的。

这样做的原因是对`self::`和`__CLASS__`的引用解析为使用这些引用的类。PHP 5.3 现在包含了一个`static::`引用，它解析为运行时调用的静态类:

```
 static public function say( $str ) {  

                static::do_print( $str );  

        }
```

添加了`static::`引用后，脚本将返回字符串“Child says Hello”。

##### `__callstatic`

到目前为止，PHP 已经在你已经熟悉的类中支持了许多神奇的方法，比如`__set`、`__get`和`__call`。PHP 5.3 引入了`__callstatic`方法，它的行为与 call 方法完全一样，但它是在静态上下文中操作的。换句话说，该方法直接作用于类上未识别的静态调用。

下面的例子说明了这个概念:

```
<?php  

class Factory {  

        static function GetDatabaseHandle() {  

                echo '<p>Returns a database handle</p>';  

        }  

        static function __callstatic( $methodname, $args ) {  

                echo '<p>Unknown static method <strong>' . $methodname . '</strong>' .  

                        ' called with parameters:</p>';  

                echo '<pre>' . print_r( $args, true ) . '</pre>';  

        }  

}  

Factory::GetDatabaseHandle();  

Factory::CreateUser();  

Factory::CreateBlogPost( 'Author', 'Post Title', 'Post Body' );
```

##### 可变静态调用

什么时候静态成员或方法不是静态的？当然是动态引用的时候！

再一次，这是一个增强，为您的类带来了对象功能。除了拥有可变变量和可变方法调用之外，现在还可以拥有可变静态调用。以上一节中定义的工厂类为例，我们可以通过调用以下代码获得相同的结果:

```
$classname = 'Factory';  

$methodname = 'CreateUser';  

$classname::$methodname();  

$methodname = 'CreateBlogPost';  

$author = 'Author';  

$posttitle = 'Post Title';  

$postbody = 'Post Body';  

$classname::$methodname( $author, $posttitle, $postbody );  

You can create dynamic namespaces like so:  

<?php  

require_once( 'lib/autoload.php' );  

$class = 'MyCompany::Blog::User';  

$user = new $class();  

$user->set('fullname', 'Ben Balbo');  

$user->save();
```

这些小小的改动可以让你的代码更具可读性，并让你在面向对象的意义上拥有充分的灵活性。

##### MySQL 本地驱动程序

在 PHP 5.3 版本之前，任何与 MySQL 的交互通常都是与`libmysql` —一个 MySQL 数据库客户端库一起发生的。

PHP 5.3 的原生 MySQL 驱动程序是为 PHP 和 ZEND 引擎从头开始设计的，这带来了许多优势。最明显的是，原生驱动是特定于 PHP 的，因此已经针对 ZEND 引擎进行了优化。这就产生了一个占用空间更小、执行时间更快的客户机。

其次，原生驱动程序利用了 ZEND 引擎的内存管理，与`libmysql`不同，它将遵守 PHP 中的内存限制设置。

本机驱动程序已经在 PHP 许可下获得许可，以避免许可问题。

##### 附加 OpenSSL 函数

如果您曾经不得不在脚本中执行任何与 OpenSSL 相关的操作(比如生成 Diffie Hellman 密钥或加密内容)，那么您要么在用户域中执行该操作，要么将请求传递给系统调用。

PHP 5.3 中 OpenSSL 功能的一个补丁提供了通过 OpenSSL 库执行这些操作所需的额外功能，这不仅使您的生活更轻松，应用程序更快，而且允许您重用 OpenSSL 附带的成熟代码。

这对目前使用 OpenID 的人来说是个好消息。

##### 改进的命令行参数支持

希望你能意识到 PHP 不仅仅是一种网络脚本语言。PHP 的命令行版本在 web 服务器环境之外运行，对于自动化系统和应用程序过程非常有用。

例如，PHP 的 getopt 函数已经存在了一段时间，但一直局限于一些系统类型；最常见的是，它不能在 Windows 操作系统环境下运行。

从 PHP 5.3 开始，getopt 函数不再依赖于系统。万岁！

##### XSLT 分析

XSLT 是一个复杂的庞然大物，这种模板机制的大多数用户都熟悉 xsltproc 的分析选项。从 PHP 5.3 开始，您可以在 PHP 脚本中分析转换。本文附带的[示例代码](https://www.sitepoint.com/examples/php5/PHPdemos.zip)的这个片段让您知道我们可能如何使用它:

```
$doc = new DOMDocument();  

$xsl = new XSLTProcessor();  

$doc->load('./lib/collection.xsl');  

$xsl->importStyleSheet($doc);  

$doc->load('./lib/collection.xml');  

$xsl->setProfiling("/tmp/xslt-profiling.txt");  

echo $xsl->transformToXML($doc);  

echo '<h2>Profile report</h2>';  

echo '<pre>' . file_get_contents( '/tmp/xslt-profiling.txt' ) . '</pre>';
```

概要文件产生的信息将类似于以下内容:

```
number    match    name   mode     Calls   Tot  100us  Avg  

0         collection                    1       4       4  

1         cd                            2       1       0  

           Total                         3       5
```

##### 新的误差等级

PHP 当然是一门有一些，呃，怪癖的语言。比如`E_ALL`为什么不包括所有的错误等级？

现在有了。是的，PHP 5.3 现在包含了作为`E_ALL`一部分的`E_STRICT`。

此外，虽然`E_STRICT`过去常常报告可能在不久的将来被弃用的功能的使用，以及不良的编程实践，如定义抽象静态方法，但在 PHP 5.3 中，这两个错误被分别拆分为`E_DEPRECATED`和`E_STRICT`，这更有意义。

##### 其他小的改进

PHP 5.3 中还有一些其他的改进，这些改进要么不能保证本文中有完整的一节，要么在撰写本文时是不可测试的，例如:

*   通过`ext/sqlite`扩展支持 Sqlite3
*   SPL 的`DirectoryIterator`，现在实现了`ArrayAccess`
*   两个新闻功能:`array_replace`和`array_replace_recursive`。虽然这些函数在 PHP 5.3.0 下测试时是未定义的，但实现它们的 C 代码表明它们将包含类似于`array_merge`的功能。然而，有一个例外，即`array_replace`函数将只更新第一个数组中的值，其中两个数组中的键都匹配。任何出现在第二个数组中但没有出现在第一个数组中的键都将被忽略。

##### 摘要

PHP 5.3 包含了很多最初计划包含在 PHP 6 中的功能，这使它从一个小的升级变成了一个重要的版本，每个 PHP 开发人员都应该开始考虑这个问题。我们讨论了本文中的大部分特性，并查看了一些演示如何使用这些新特性的代码。

不要忘记[下载本文附带的代码档案](https://www.sitepoint.com/examples/php5/PHPdemos.zip)，享受在边缘生活的乐趣！

## 分享这篇文章