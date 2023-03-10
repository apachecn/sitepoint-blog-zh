# PHP 命令行——第 1 部分文章

> 原文：<https://www.sitepoint.com/php-command-line-1-3/>

##### 解析选项

虽然乍一看，编写一个解析选项的函数似乎并不太难，但是为什么要这么麻烦呢？ [PEAR::Console_Getopt](http://pear.php.net/Console_Getopt) 提供了一个现成的解决方案，解决了你之前看到的`$argv vs. $_SERVER['argv']`的问题。

注意我在这里使用的`Console_Getopt`版本是 1.2。如果您想知道如何安装，请参见[PEAR](https://www.sitepoint.com/article/getting-started-with-pear)入门。请注意，卸载`Console_Getopt`不是一个好主意，因为包管理器本身就依赖于它。如果您需要升级版本，请使用:

`$ pear upgrade Console_Getopt-stable`

PEAR::Console_Getopt 提供了三个公共方法，每个方法都是静态调用的:

**1。`array Console_Getopt::readPHPArgv()`**

从`readPHPArgv()`返回的数组与`$argv`数组相同，但是`Console_Getopt`针对不同的 PHP 版本提供了更好的保护。如果它不能获取访问命令行参数(例如，如果它们不可用，比如用 CGI 二进制 where `register_argc_argv=Off`)，它将返回一个 PEAR 错误对象。

**2。`array Console_Getopt::getOpt(array argv, [string short_opts], [array long_opts])`**

`getOpt()`方法获取命令行参数的数组，并将它们解析成选项和参数的数组。它期望 argv 的第一个元素是正在执行的脚本的名称，这是为了在执行以下类型的脚本时使用:

`$ php some_script.php`

“`short_opts`”字符串标识用户可以提供的短选项，后跟标识给定选项的字符。它允许字符串中有两个特殊的标记:
、`:`指定前面的字母*后面必须跟一个“值”，而“`::`表示前面的选项*后面可以跟一个值*。通过例子最容易看出这一点。*

如果`$short_opts = 'lt'`，以下命令行是可能的(不会从`getOpt())`返回错误):

`$ php some_script.php -lt
$ php some_script.php -tl
$ php some_script.php -l -t
$ php some_script.php -t -l
$ php some_script.php -l
$ php some_script.php -t -t -l -l
$ php some_script.php -t
$ php some_script.php`

设置`$short_opts = 'lt:'`意味着“t”选项*必须在*后面至少跟一个字符(不是空格)，这将是它的值。看看这个例子:

`$ php some_script.php -ltv`

这里`'t' = 'v'`。

`$ php some_script.php -tvalue -l`

现在`'t' = 'value'`。

`$ php some_script.php -tl`

这里`'t' = 'l'`(这种情况下 l 不是选项)。

最后，设置`$short_opts = 'lt::'`意味着‘t’可以有一个*可选的*值(任何继续它的值，直到下一个空格)。注意，我可以在短选项字符串中的冒号后面放置更多的选项。比如`'lt:o::B'`。

这看起来有点神秘，但是以这种方式指定短选项字符串在 UNIX 上使用的许多语言中是相当标准的，从 C 到 Python。一旦您习惯了，这种方法就提供了一种有用的机制，可以用最少的努力获得最大的命令行参数。

以与 short_opts 相似的方式指定`long_opts`,但是不使用字符串，而是使用数组；此外，'`:`'标记被替换为一个`=`符号，以识别何时应该给出值。为了允许长选项'【T3 ')，我需要一个这样的数组:

`$long_opts = array (
'user=',
'pass=',
);`

在这种情况下，`'user'`和`'pass'`选项都需要一个值。如果我省略了等号，则不允许有值，而使用'【T2 ']则允许用户提供一个可选值。

从`getOpt()`返回的数组在其第一个顺序中总是有两个元素；第一个包含命令行选项，第二个包含参数。我们一会儿会更详细地看这个结构。

`Console_Getopt`提供的最终方法是:

**3。`array Console_Getopt::getOpt2(array argv, [string short_opts], [array long_opts])`**

这个方法与`getOpt()`几乎完全相同，除了它期望`argv`数组中的第一个元素是一个实参数，*而不是正在执行的脚本的名称*。换句话说，您可以在执行如下脚本时使用它:

`$ some_script.php -tl`

下面是 PEAR::Console_Getopt 的一个简单例子:

`<?php
// Include PEAR::Console_Getopt
require_once 'Console/Getopt.php';`

 `//定义错误的退出代码
define('NO_ARGS '，10)；
define('INVALID_OPTION '，11)；

//读取传入参数——同$ argv
$ args = Console _ Getopt::readPHPArgv()；

//确保我们得到了它们(对于非 CLI 二进制文件)
if(PEAR::isError($ args)){
fwrite(STDERR，$args- > getMessage()。“n”)；
出口(NO _ ARGS)；
}

//Short options
$ Short _ opts = ' lt:'；

//Long options
$ Long _ opts = array(
' user = '，
'pass= '，【T3])；

//将参数转换为选项——检查第一个参数
if(realpath($ _ SERVER[' argv '][0])= = _ _ FILE _ _){
$ options = Console _ Getopt::Getopt($ args，$short_opts，$ long _ opts)；
} else {
$ options = Console _ Getopt::Getopt 2($ args，$short_opts，$ long _ opts)；
}

//检查选项是否有效
if(PEAR::isError($ options)){
fwrite(STDERR，$options- > getMessage()。“n”)；
退出(INVALID _ OPTION)；
}` 

`print_r($options);
?>`

如果我们像这样调用这个脚本:

`$ php getopts.php -ltr --user=harryf --pass=secret arg1 arg2`

来自 [print_r()](http://www.php.net/print_r) 的输出如下所示:

`Array
(
[0] => Array
(
[0] => Array
(
[0] => l
[1] =>
)`

 `[1] = >数组
(
[0]=>t
[1]=>r
)

[2] = >数组
(
[0] = > -用户
[1] = > harryf
)

[3] = >阵
(
[0] = > -传
[1] = >秘
)

)

[1] = >数组
(
[0]=>arg 1
[1]=>arg 2
)` 

`)`

options 数组总是包含两个一阶元素，其中第一个元素包含另一个数组，该数组表示每个命令行*选项*。第二个元素包含一组*参数*。如果您回头看看脚本是如何被调用的，您应该能够看到这种关系。

您可以看到如何使用 PEAR::Console_Getopt 来解析 UNIX 'ls '实用程序使用的参数和选项，如下所示:

`$ ls -ltr --width=80 /home/harryf/scripts/`

如果您用`Console_Getopt`解析上面的命令行，您将得到一系列选项，例如:

`Array
(
[0] => Array
(
[0] => Array
(
[0] => l
[1] =>
)`

 `[1] = >数组
(
[0]=>t
[1]=>
)

[2] = >数组
(
[0]=>r
[1]=>
)

[3] = >数组
(
[0] = > -宽度
[1] = > 80
)
)

[1] = >数组
(
[0]=>/home/harryf/scripts/
)` 

`)`

如果您有无穷无尽的时间，现在就可以在 PHP 中重新实现“ls”了。

##### 和睦相处

正如我在本文前面提到的，使用 PHP CGI 二进制文件的方式可能与使用 CLI 二进制文件的方式差不多，但是还需要一些额外的“调整”。其中一些更改最好在运行时通过包含一个额外的 PHP 脚本来完成。其他的则需要在 php.ini 中创建，或者通过向 php 二进制文件传递额外的参数来创建。

从您可以在运行时更改的设置开始，这里有一个脚本，它为使用 CGI 二进制文件或 4.3.0 以下版本的 CLI 的用户修复了大多数问题:

`<?php
/**
* Sets up CLI environment based on SAPI and PHP version
*/
if (version_compare(phpversion(), '4.3.0', '<') || php_sapi_name() == 'cgi') {
// Handle output buffering
@ob_end_flush();
ob_implicit_flush(TRUE);`

 `// PHP ini 设置
set _ time _ limit(0)；
ini_set('track_errors '，TRUE)；
ini_set('html_errors '，FALSE)；
ini _ set(' magic _ quotes _ runtime '，FALSE)；

//定义流常量
define('STDIN '，fopen('php://stdin '，' r ')；
define('STDOUT '，fopen('php://stdout '，' w ')；
define('STDERR '，fopen('php://stderr '，' w ')；` 

`// Close the streams on script termination
register_shutdown_function(
create_function('',
'fclose(STDIN); fclose(STDOUT); fclose(STDERR); return true;')
);
}
?>`

文件名:cli _ 兼容性. php

在您的脚本中包含这些代码将解决大部分问题，但是还有一些问题。

CGI 可执行文件通常在执行时发送 HTTP 头——甚至从命令行发送。当用户使用 CGI 可执行文件执行命令行脚本时，他们可能会看到类似如下的输出:

`X-Powered-By: PHP/4.3.6
Content-type: text/html`

为了防止这种情况，PHP 需要使用'-q '选项来调用' quiet '。例如:

`$ php -q some_script.php`

不幸的是，这给用户带来了负担。在 UNIX 上，您可以通过将该选项作为脚本添加到 SheBang 中，并鼓励用户直接执行它来解决这个问题:

`#!/usr/local/bin/php -q
<?php
// Code starts here`

CGI 可执行文件的另一个问题是，它会自动将当前工作目录更改为正在执行的脚本所在的目录。假设我使用一个包含以下内容的简单脚本:

`<?php
print getcwd()."n";
?>`

Filename: getcwd.php

现在，我这样执行它:

`$ pwd
/home/harryf
$ php ./scripts/getcwd.php`

如果我使用的是 CLI 二进制文件，这将显示“/home/harryf”“*我的*当前目录。但是，如果我使用 CGI 二进制文件，我会得到“/home/harryf/scripts”——脚本目录。

我不知道有什么变通方法可以解决这个问题，所以，如果这个问题对您的应用程序至关重要，您最好的办法就是强制用户使用 CLI 二进制文件。将以上兼容性脚本的开头更改为:

`<?php
if ( php_sapi_name() == 'cgi' ) {
die ('Unsupported SAPI - please use the CLI binary');
}
if ( version_compare(phpversion(), '4.3.0', '<') ) {`

##### 包裹

这就是我们 PHP 命令行界面之旅的第一部分。到目前为止，我们已经涵盖了基础知识。下一次，我将讨论从 PHP 执行外部程序，我们将探索 PEAR 提供的一些软件包来美化您的输出，我们将研究 PHP 为命令行提供的一些(只针对 UNIX 的)扩展。

**Go to page:** [1](https://sitepoint.com/php-command-line-1) | [2](https://sitepoint.com/php-command-line-1-2/) | [3](https://sitepoint.com/php-command-line-1-3/)

## 分享这篇文章