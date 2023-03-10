# PHP 命令行——第 1 部分文章

> 原文：<https://www.sitepoint.com/php-command-line-1-2/>

##### 多项选择

您还可以使用 STDIN 来处理选择:

`<?php
fwrite(STDOUT, "Pick some colors (enter the letter and press return)n");`

 `//选择颜色的数组
$colors = array (
'a'= >'红色'，
'b'= >'绿色'，
'c'= >'蓝色'，
)；

fwrite(STDOUT，“输入‘q’退出”)；

//显示选择
foreach($ colors as $ choice =>$ color){
fwrite(STDOUT，" t $ choice:$ colorn ")；
}

//循环，直到他们为 Quit
do {
//STDIN 中的一个字符输入‘q’，忽略空白字符
do {
$ selection = fgetc(STDIN)；
} while(trim($ selection)=“”)；

if ( array_key_exists($selection，$colors) ) {
fwrite(STDOUT，" You picked { $ colors[$ selection]} n ")；
}

} while(＄选择！= ' q ')；` 

`exit(0);
?>`

文件名:pickacolor.php

最终用户会看到类似这样的内容:

`Pick some colors (enter the letter and press return)
Enter 'q' to quit
a: Red
b: Green
c: Blue
b
You picked Green
c
You picked Blue
q`

使用一个循环，我可以让我的脚本继续执行，直到用户输入' q '退出。另一种方法是一个 while 循环，它永远持续下去，但是在满足某些条件时包含一个 [break](http://www.php.net/break) 指令。您可能需要一些时间来熟悉这一点，特别是如果您一直在 Web 应用程序中使用 PHP，在这种应用程序中，执行通常在 30 秒后自动停止，并且用户输入在请求开始时以单个包的形式提供。主要的事情是，当你像这样插入“永无止境”的循环时，要密切注意你的代码，这样你就不会无意中一遍又一遍地执行数据库查询。

现在，让我们回到上面的例子。看看这里发生了什么:

`// A character from STDIN, ignoring whitespace characters
do {
$selection = fgetc(STDIN);
} while ( trim($selection) == '' );`

`fgetc()`函数从来自用户的 STDIN 流中提取一个字符。当执行到这一点时，终端会暂停，等待用户输入字符并按下 return 键。我输入的两个字符，比如字母‘a’，*和*换行符都放在 STDIN 流中。两者最终都将被`fgetc()`读取，但是在连续的循环迭代中。因此，我需要忽略新的行字符，在本例中，我使用了 [trim()](http://www.php.net/trim) 函数，比较它返回的空字符串。

如果这没有意义，试着注释掉这个内部循环中`fgetc()`前后的`do {`和`} while ( trim($selection) == '' );`行。然后，看看运行脚本时会发生什么。

另外，现在，这个脚本的用户可以在按回车键之间输入多个字符。看看你是否能通过确认在用户的选择之间按下回车键*来防止这种情况。*

##### 错误和管道

前面我提到过，您可以将错误写入 PHP 中 STDERR 常量定义的不同输出位置。这与写入 STDOUT 的方式相同:

`<?php
// A constant to be used as an error return status
define ('DB_CONNECTION_FAILED',1);`

 `//尝试连接 MySQL
if(！@mysql_connect('localhost '，' user '，' pass '){
//写入 STDERR
fwrite(STDERR，mysql_error()。“n”)；
退出(DB _ CONNECTION _ FAILED)；
}` 

`fwrite(STDOUT,"Connected to databasen");
exit(0);
?>`

文件名:failedconnection.php

现在将使用 STDERR 报告任何连接错误。这个脚本没有演示为什么这个功能会有用。为此，请考虑以下情况:

`<?php
// A custom error handler
function CliErrorHandler($errno, $errstr, $errfile, $errline) {
fwrite(STDERR,"$errstr in $errfile on $errlinen");
}
// Tell PHP to use the error handler
set_error_handler('CliErrorHandler');`

 `fwrite(STDOUT，“打开文件 foobar . logn”)；

//文件不存在——生成错误
if ( $fp = fopen('foobar.log '，' r '){
//在这里对文件做点什么
fc lose($ FP)；
}` 

`fwrite(STDOUT,"Job finishedn");
exit(0);
?>`

文件名:outwitherrors.php

现在，当您正常执行这个脚本时(假设文件 foobar.log 不存在于与脚本相同的目录中)，它会产生如下输出:

`Opening file foobar.log
fopen(foobar.log): failed to open stream: No such file or directory in /home/harryf/outwitherrors.php on 11
Job finished`

像以前一样，错误消息与正常输出混合在一起。但是通过*管道*脚本的输出，我可以将错误从正常输出中分离出来:

`$ php outwitherrors.php 2> errors.log`

这一次，您只会看到这些消息:

`Opening file foobar.log
Job finished`

但是，如果查看运行脚本的目录，就会创建一个名为 errors.log 的新文件，其中包含错误消息。数字 2 是用于标识 STDERR 的命令行句柄。注意，1 是 STDOUT 的句柄，而 0 是 STDERR 的句柄。使用命令行中的`>`符号，您可以将输出定向到一个特定的位置。

虽然这看起来并不令人兴奋，但它是一个非常方便的系统管理工具。从 cron 运行一些脚本的简单应用程序如下所示:

`$ php outwitherrors.php >> transaction.log 2>> errors.log`

使用'【T0]'，我告诉终端将新消息添加到现有日志中(而不是覆盖它)。正常的操作消息现在被记录到 transaction.log 中，我可能一个月查阅一次，只是为了检查一切是否正常。同时，任何需要更快响应的错误都以 errors.log 结束，其他一些 cron 作业可能会根据需要每天(或更频繁地)给我发电子邮件。

当涉及到管道输出时，UNIX 和 Windows 命令行之间有一个区别，这一点您应该知道。在 UNIX 上，可以将 STDOUT 和 STDERR 流合并到一个目标，例如:

`$ php outwitherrors.php > everything.log 2>&1`

这样做的目的是将 STDERR 重新路由到 STDOUT，这意味着两者都被写入日志文件 everything.log。

不幸的是，Windows 不支持这一功能，尽管您可以找到工具(如 [StdErr](http://www.teaser.fr/~amajorel/stderr/) 可以帮助您达到相同的目的。

总的来说，管道 IO 是一个我不能完全公正对待的主题。在 UNIX 上，几乎是一个(黑？)艺术，并且通常在你开始使用其他命令行工具时发挥作用，比如 sed、awk 和 xargs (O'Reilly 已经将整本书都献给了这个主题，包括[洛里斯的书](http://www.oreilly.com/catalog/sed2/colophon.html))。然而，您应该感受到的是，通过遵守 shell 脚本的标准约定，您有机会利用一个强大的系统管理“框架”,它提供了各种其他工具。

##### 应对争论

您已经看到了一些如何在脚本开始执行时从 STDIN 读取用户输入的示例。如果能够在脚本执行时将信息传递给脚本呢？

如果你曾经用 C 或 Java 之类的语言编程过，你肯定会对名为“argc”和“argv”的变量很熟悉。PHP 使用相同的命名约定，整数变量`$argc`包含传递给脚本的参数数量，而$argv 包含参数的索引数组，每个参数之间的“分隔符”是一个空格字符。

要了解它们是如何工作的，请尝试以下脚本:

`<?php
// Correct English grammar...
$argc > 1 ? $plural = 's' : $plural = '';`

 `//显示接收到的参数个数
fwrite(STDOUT，" Got $ argc argument $ pluraln ")；` 

`// Write out the contents of the $argv array
foreach ( $argv as $key => $value ) {
fwrite(STDOUT,"$key => $valuen");
}
?>`

文件名:arguments.php

我像平常一样执行这个脚本:

`$ php arguments.php`

结果是:

`Got 1 argument
0 => arguments.php`

第一个也是唯一一个参数是正在执行的脚本的名称。

现在，如果我执行以下命令:

`$ php arguments.php Hello World!`

代码返回:

`Got 3 arguments
0 => arguments.php
1 => Hello
2 => World!`

字符串"`Hello`"和"`World!`"被视为两个独立的参数。

相反，如果我使用引号:

`$ php arguments.php "Hello World!"`

输出如下所示:

`Got 2 arguments
0 => arguments.php
1 => Hello World!`

如您所见，以这种方式向 PHP 脚本传递基本参数非常容易。当然，总有一些“陷阱”需要小心。

变量`$argc`和`$argv`在 PHP 的全局范围内对那些使用 PHP 4.3.0+版本的 CLI 二进制文件的用户可用。它们在全局范围之外不可用(比如在函数内部)。当你在 register_globals 关闭的情况下使用 CGI 二进制文件时，`$argc`和`$argv`将根本不可用。相反，最好使用`$_SERVER['argv']`和`$_SERVER['argc']`，通过您熟悉的`$_SERVER`数组来访问它们。不管 SAPI 和`register_globals`在这里都是可用的，只要(等等……)在 php.ini 中打开`'register_argc_argv'` ini 设置(谢天谢地，从 PHP 4.0.0 开始，默认情况下就是这样)。

另一个主要“问题”是您在上面看到的第一个参数——脚本本身的名称。这是因为我在命令行中命名了执行脚本的 PHP 二进制文件，所以参数是相对于 PHP 二进制文件计算的，*不是相对于它正在执行的脚本计算的。同时，正如您在上面看到的，在 UNIX 上通过用“SheBang”标识二进制文件，或者在 Windows 上通过文件关联，可以直接执行脚本。这样做意味着脚本的名称不再出现在参数列表中。换句话说，你需要仔细检查第一个参数包含了什么，以及它是否匹配`$_SERVER['SCRIPT_NAME']`的内容。当然，如果`$_SERVER['SCRIPT_NAME']`总是可用的话，生活就太简单了，但如果你使用的是 CGI 二进制文件，通常就不会这样了。更可靠的机制是将`__FILE__`常量的内容与`$_SERVER[argv][0]`的内容进行比较:*

`<?php
if ( realpath($_SERVER['argv'][0]) == __FILE__ ) {
fwrite(STDOUT,'You executed "$ php whereami.php"');
} else {
fwrite(STDOUT,'You executed "$ whereami.php"');
}
?>`

文件名:whereami.php

如果您有过使用 UNIX shell 执行命令的经验，您肯定会遇到命令行选项的使用，例如:

`$ ls -lt`

这以“长”格式列出了目录的内容，文件按修改时间排序。你可能知道的另一个例子如下:

`$ mysql --user=harryf --password=secret`

以上用于登录 MySQL。

这些被称为命令行*选项*，第一个例子使用“短选项”语法，而第二个例子使用“长选项”语法。

如果我尝试将这样的参数传递给上面的 arguments 脚本:

`$ php arguments.php --user=harry --password=secret`

我明白了:

`Got 3 arguments
0 => arguments.php
1 => --user=harry
2 => --password=secret`

`$argv`数组不知道 options 语法，只限于使用参数之间的空格字符来分解参数。解析每个参数的内容以在脚本中处理类似这样的“选项”取决于您。

**Go to page:** [1](https://sitepoint.com/php-command-line-1) | [2](https://sitepoint.com/php-command-line-1-2/) | [3](https://sitepoint.com/php-command-line-1-3/)

## 分享这篇文章