# 命令行上的 PHP 第 2 部分

> 原文：<https://www.sitepoint.com/php-command-line-2/>

在本教程的第一部分中，我们看了一下 PHP 的命令行 SAPI(服务器 API)。在本文中，我们将更进一步，看看如何将命令行 PHP 脚本与操作系统提供的现有命令行工具联系起来。

与上一篇文章相比，本文的重点将是基于 Unix 的系统——这是不可避免的。从 Linux.com 的 [CLI 中为 Noobies](http://www.linux.com/article.pl?sid=04/05/31/1831246) 系列剽窃一段话:

“[将]DOS/Windows 的命令行环境与 Linux 的命令行环境进行比较，就像将一辆独轮车与一辆 18 轮大卡车进行比较一样。”

我假设您正在使用 PHP 4.3.x+和 CLI SAPI 在命令行上执行 PHP 脚本。提供的示例期望在`/usr/local/bin/php`找到 CLI 二进制文件。如果您的设置与此不同，修改第一行应该可以纠正每种情况。请注意，您可以在这里下载本教程的完整代码档案。

***今日命令行选项:***

*   外壳执行:从 PHP 运行外部程序
*   安全问题:恐怖分子威胁命令行！
*   与环境一起工作:与母操作系统对话
*   过程控制:它杀了我

##### 外壳执行

在基于 Unix 的系统上工作的优势之一是有大量的命令行工具可以用来解决您能想到的任何常见问题。

当您为命令行编写 PHP 脚本时，这变得尤为重要。例如，在一组文本文件上执行搜索和替换，最好留给像`grep`和`sed`这样的工具，既可以提高性能，又可以节省宝贵的开发时间。这并不是说您不应该使用 PHP 作为“前端”来执行搜索和替换操作，而是说您应该尽可能将“真正的工作”委托给现有的工具。

PHP 提供了许多通过 shell 执行外部程序的函数，即 [shell_exec()](http://www.php.net/shell_exec) 、 [passthru()](http://www.php.net/passthru) 、 [exec()](http://www.php.net/function.exec) 、 [popen()](mailto:http://www.php.net/system/#nlt#/system()/#enl#/ and  <a rel='external' class=) ，所有这些函数做的事情基本相同，但是为外部程序提供了不同的“接口”。这些函数中的每一个都只能捕获写入`STDOUT`管道的*输出*；每个都将产生一个子进程，外部程序将在其中执行。

还有两个函数用于执行外部程序: [proc_open()](http://www.php.net/proc_open) 和 [pcntl_exec()](http://www.php.net/pcntl_exec) 。它们的用途和行为不同于前面的函数，所以我们将分别对它们进行研究。

***我的分机是多少？***

为了给我们一个可以用其他 PHP 脚本执行的“已知数量”,首先，我们将查看一个检查任何给定安装中可用的 PHP 扩展的脚本。我会让代码自己解释；您在这里看到的一切，您在上一篇文章中已经看到了:

```
#!/usr/local/bin/php 

<?php 

# Include PEAR::Console_Getopt 

require_once 'Console/Getopt.php'; 

// Define error return codes 

define('INVALID_PHP_SAPI',3); 

define('UNKNOWN_EXTENSION',4); 

//-------------------------------------------------------------------------------------- 

/** 

* Displays the usage of this script 

* Called when -h option specified or on illegal option 

*/ 

function usage() { 

  $usage = <<<EOD 

Usage: ./extensions.php [OPTION] 

Lists the loaded PHP extensions or shows the functions 

a single extension makes available 

  -e=EXTENSION  Name of extension to list functions for 

  -h    Display usage 

EOD; 

  fwrite(STDOUT,$usage); 

  exit(0); 

} 

//-------------------------------------------------------------------------------------- 

/** 

* Gets the name of an extension from -e option 

* (defaults to NULL) 

*/ 

function getExtension() { 

  $args = Console_Getopt::readPHPArgv(); 

  // Could be an error with older PHP versions and the CGI SAPI 

  if ( PEAR::isError($args) ) { 

    fwrite(STDERR,$args->getMessage()."n"); 

    exit(INVALID_PHP_SAPI); 

  } 

  // Compatibility between "php extensions.php" and "./extensions.php" 

  if ( realpath($_SERVER['argv'][0]) == __FILE__ ) { 

    $options = Console_Getopt::getOpt($args,'he:'); 

  } else { 

    $options = Console_Getopt::getOpt2($args,'he:'); 

  } 

  // Check for invalid options 

  if ( PEAR::isError($options) ) { 

    fwrite(STDERR,$options->getMessage()."n"); 

    usage(); 

  } 

  // Set default length 

  $extension = NULL; 

  // Loop through the user provided options 

  foreach ( $options[0] as $option ) { 

    switch ( $option[0] ) { 

      case 'h': 

        usage(); 

      break; 

      case 'e': 

        $extension = $option[1]; 

      break; 

    } 

  } 

  return $extension; 

} 

//-------------------------------------------------------------------------------------- 

// Get a list of extensions 

$extensions = get_loaded_extensions(); 

$extension = getExtension(); 

// If it's not null, the -e option was used 

if ( !is_null($extension) ) { 

  // Does the extension actually exist? 

  if ( in_array($extension,$extensions) ) { 

    // Display the list of functionsthe extension provides 

    $funcs = get_extension_funcs($extension); 

    foreach ( $funcs as $func ) { 

      fwrite(STDOUT,$func."n"); 

    } 

  } else { 

    fwrite(STDERR,"Unknown extension $extensionn"); 

    exit(UNKNOWN_EXTENSION); 

  } 

} else { 

  // Display the list of extension 

  foreach ( $extensions as $extension ) { 

    fwrite(STDOUT, $extension."n"); 

  } 

} 

exit(0); 

?>
```

文件名:extensions.php

使用以下命令执行代码:

```
$ ./extensions.php
```

当执行时，上面的脚本默认显示一个加载的 PHP 扩展列表。但是，如果提供了命令行选项'-e '以及扩展名，它会列出该扩展提供的功能。

```
$ ./extensions.php -e mysql
```

上面的命令显示了 MySQL 扩展(或者您提供的任何扩展名称)提供的函数列表。

不要忘记，您需要通过如下设置更正权限来使脚本可执行:

```
$ chmod +x extensions.php
```

现在我已经有了一个简单的程序，我可以从另一个脚本中执行它。

***shell_exec()***

[shell_exec()](http://www.php.net/shell_exec) 命令实际上是[反斜杠运算符](http://www.php.net/language.operators.execution)的别名。它允许你通过 shell 执行一个外部程序*，并将结果以字符串的形式返回给你。*

是否使用反勾符或`shell_exec()`命令由你决定。

就个人而言，我更喜欢后者，因为它清楚地说明了你的代码在做什么，并且很容易在源代码中看到。

这里有一个基本的例子:

```
#!/usr/local/bin/php 

<?php 

$result = shell_exec('./extensions.php'); 

fwrite(STDOUT,$result); 

exit(0); 

?>
```

文件名:shell_exec1.php

如您所见，使用`shell_exec()`，我可以简单地命名一个我想要执行的外部程序，并在返回的变量中返回结果。上面的例子有一个重要的要求——`shell_exec1.php`脚本必须从`extensions.php`所在的目录执行。当我们考虑下面的环境时，我们将进一步研究这一点。

我们还可以通过 shell_exec()传递命令行选项。让我们看一个例子:

```
#!/usr/local/bin/php 

<?php 

$result = shell_exec('./extensions.php -e mysql'); 

fwrite(STDOUT,$result); 

exit(0); 

?>
```

文件名:shell_exec2.php

换句话说，我们告诉`shell_exec()`执行的命令可以是我们自己从命令行输入的任何东西。例如，我可以通过`grep`传输 extensions.php 的输出，过滤掉所有以字母‘p’开头的扩展名:

```
#!/usr/local/bin/php 

<?php 

$result = shell_exec('./extensions.php | grep -w "^p.*"'); 

fwrite(STDOUT,$result); 

exit(0); 

?>
```

文件名:shell_exec3.php

这将返回以下列表(来自我的系统):

```
posix 

pgsql 

pcre 

pcntl
```

关于`shell_exec()`需要注意的一件重要事情是，它只返回来自`STDOUT`的输出。看一下这个例子:

```
#!/usr/local/bin/php 

<?php 

$result = shell_exec('./extensions.php -e hair 2>/dev/null'); 

// Assumes that nothing was written to STDOUT if there was an error 

if ( !empty($result) ) { 

  fwrite(STDOUT,$result); 

} else { 

  fwrite(STDERR,"An error occurredn"); 

} 

exit(0); 

?>
```

文件名:shell_exec4.php

在这里，我已经将`STDERR`流重定向到 Unix 的空设备(我要扔掉它)。如果我用一个不存在的扩展名执行我的`extensions.php`脚本，没有任何东西被写到`STDOUT`，所以上面的脚本会显示一个(有点没用的)错误发生的消息。

注意，如果我*不*将`STDERR`重定向到空设备，我仍然会从我的 shell 中看到错误消息(“未知的扩展头发”)，错误已经“绕过”`shell_exec4.php`。

 *`passthru()`功能允许您执行外部程序并直接显示其结果:

```
#!/usr/local/bin/php 

<?php 

passthru('./extensions.php'); 

exit(0); 

?>
```

档案名称:passthru1.php

这就是你需要做的(`passthru()`不返回任何东西)。

不过，它确实需要第二个参数——一个由外部程序的*返回代码*填充的变量。例如:

```
#!/usr/local/bin/php 

<?php 

$return_code = 0; 

passthru('./extensions.php -e hair 2>/dev/null',$return_code); 

if ( $return_code != 0 ) { 

  fwrite(STDERR,"There was an error!n"); 

} 

exit(0); 

?>
```

档案名称:passthru2.php

这为我提供了一个比`shell_exec()`更准确的机制来检查错误，如果发生错误，我被迫假设不会向`STDOUT`写入任何内容。

***执行()*T3**

[exec()](http://www.php.net/function.exec) 函数提供了主题的另一种变体。它返回外部程序输出的最后一行，但也(可选地)用完整的输出填充一个数组，并使返回代码可用。如果我假设大多数程序会返回一个单行的错误消息，如果确实有一个的话，`exec()`会非常方便。让我们看另一个例子:

```
#!/usr/local/bin/php 

<?php 

$return_code = 0; 

$result = array(); 

$error = exec('./extensions.php -e hair 2>&1',$result, $return_code); 

if ( $return_code != 0 ) { 

  fwrite(STDERR,"Error: $errorn"); 

} else { 

  $result = implode("n",$result); 

  fwrite(STDOUT,$result); 

} 

exit(0); 

?>
```

Filename: exec1.php

首先，我将`STDERR`流重定向到`STDOUT`，这样我就可以从我的脚本中捕获两者。只有当`$return_code`不为零时，我才使用`$error`变量。否则，我可以显示`$result`数组的内容。

***系统()***

[system()](http://www.php.net/system) 函数提供了主题的另一种变体——介于`passthru()`和`exec()`之间。像`passthru()`一样，它也在`STDOUT`流上直接输出它从外部程序接收到的任何东西。然而，像`exec()`一样，它也返回最后一行输出，并使返回代码可用。例如:

```
#!/usr/local/bin/php 

<?php 

$return_code = 0; 

$result = array(); 

$error = system('./extensions.php -e hair 2>&1',$return_code); 

if ( $return_code != 0 ) { 

  // Fictional logger 

  # Logger::log($error); 

} 

exit(0); 

?>
```

文件名:system1.php

这段代码中的一个重要区别是，尽管错误消息仍然会立即显示在 shell 中，但我也有机会记录错误消息。

***popen()***

[popen()](http://www.php.net/popen) 函数允许我像处理文件一样处理外部程序。例如:

```
#!/usr/local/bin/php 

<?php 

$fp = popen('./extensions.php','r'); 

while ( !feof($fp) ) { 

  fwrite(STDOUT, fgets($fp)); 

} 

pclose($fp); 

exit(0); 

?>
```

Filename: popen1.php

和前面所有的函数一样，`popen()`只从`STDOUT`中读取数据。因此，以下示例将不显示任何内容:

```
#!/usr/local/bin/php 

<?php 

$fp = popen('./extensions.php -e hair 2>/dev/null','r'); 

while ( !feof($fp) ) { 

  fwrite(STDOUT, fgets($fp)); 

} 

pclose($fp); 

exit(0); 

?>
```

Filename: popen2.php

关于`popen()`的一个特别之处是它还允许你*将*写到程序的`STDIN`流中，就像我们在上一个教程中看到的那样。

假设我有一个简单的脚本，如下所示:

```
#!/usr/local/bin/php 

<?php 

while ( trim($line = fgets(STDIN)) != 'exit' ) { 

  fwrite(STDOUT,$line); 

} 

exit(0); 

?>
```

文件名:reader.php

如果我自己执行这个脚本，它会一行一行地显示我输入的所有内容，直到我输入字符串' exit '。

我现在可以用`popen()`把写成这样:

```
#!/usr/local/bin/php 

<?php 

$colors = array('red','green','blue'); 

// Open program in write mode 

$fp = popen('./reader.php','w'); 

foreach ( $colors as $color ) { 

  fwrite($fp,$color."n"); 

} 

fwrite($fp,"exitn"); 

pclose($fp); 

exit(0); 

?>
```

Filename: popen3.php

如果我运行这个脚本，`$colors`数组中的三种颜色将显示在终端的新行上。因为我在*写*模式下用`popen()`打开程序，而不是捕获`STDOUT`流，它使用`STDIN`来代替，这允许结果保持可见。

注意`popen()`是单向的。你只能读取外部程序的*或*,而不能两者都读取(尽管 PHP 手册提供了一些有趣的技巧来解决这个问题)。

在 PHP 4.3.x+中，函数`proc_open()`允许你同时使用多个 IO 管道，我们将在后面看到。

##### 安全问题

如果没有“haXor ownZ you”的持续威胁，编程就不会有任何乐趣，不是吗？好消息是，当从 PHP 执行外部程序时，有很多机会让世界进入你的内心世界。

***命令注射***

用 PHP 执行外部程序，其中用户输入影响执行的程序，这与 SQL 语句的执行有相似之处:您冒了*命令注入*的风险。

考虑下面的例子*如何不这样做！*

```
#!/usr/local/bin/php  

<?php  

fwrite(STDOUT,"This is insecurity in action!n");  

fwrite(STDOUT,"Enter a filename to list: ");  

$file = trim(fgets(STDIN));  

$result = shell_exec("ls -l $file");  

fwrite(STDOUT, $result);  

exit(0);  

?>
```

文件名:security1.php

看到问题了吗？假设我作为脚本的用户，在“输入要列出的文件名:”提示符下输入如下内容:

```
Enter a filename to list: whatever.php; rm -rf ./;
```

哎呀——这个目录中没有更多文件了！我用`shell_exec()`执行的命令实际上会是这样的:

```
$result = shell_exec("ls -l whatever.php; rm -rf ./;");
```

事实上，我正在执行*两个*命令！

在这个例子中防止这个问题发生的一个方法是将`$file`变量放在*单引号*中，并去掉用户可能添加的任何单引号，如下所示:

```
$file = str_replace(''','',$file);  

$result = shell_exec("ls -l '$file'");
```

*重要提示:*不要使用双引号对用户提交的值进行转义，因为这允许插入环境变量之类的东西。参见[什么时候命令行不是线？](http://freshmeat.net/articles/view/337/)讨论与用户输入混合时涉及的参数扩展和一般 CLI 安全问题。

更好的方法是使用 PHP 函数 [escapeshellarg](http://www.php.net/escapeshellarg) ，它确保字符串中的所有单引号都被正确转义，并在字符串的开头和结尾放置单引号。

如果我们采用这样的脚本:

```
<?php  

$arg = "foo 'bar' foo";  

echo escapeshellarg($arg)."n";  

?>
```

显示的输出如下所示:

```
'foo '''bar''' foo'
```

您可以将此输出安全地用作正在执行的外部程序的单个参数。

换句话说，上面的脚本`security1.php`应该变成:

```
#!/usr/local/bin/php  

<?php  

fwrite(STDOUT,"Enter a filename to list: ");  

$file = trim(fgets(STDIN));  

$file = escapeshellarg($file);  

$result = shell_exec("ls -l $file");  

fwrite(STDOUT, $result);  

exit(0);  

?>
```

文件名:security2.php

PHP 提供的另一个函数是 [escapeshellcmd()](http://www.php.net/escapeshellcmd) ，转义元字符如；带一个反斜杠。这提供了使用单引号的替代方法，在某些情况下可能需要使用单引号。不过，一般来说，除非有特殊需要，否则最好使用`escapeshellarg()`。

*对 Windows 用户的警告:*对于 PHP 版本 4.3.6 及以下，使用`escapeshellarg()`和`escapeshellcmd()`的*不*安全。参见安全跟踪器上的[该警报](http://www.securitytracker.com/alerts/2004/Jun/1010410.html)。

更一般地说，与 Web 应用程序一样，我们不能简单地依赖这里的转义函数；我们必须限制用户只输入他们需要提供的内容。例如，如果他们应该只输入包含字母表中的字符的字符串，则使用简单的正则表达式验证输入，如下所示:

```
if ( !preg_match('/^[a-zA-Z]*$/', $userinput) ) {  

  exit(INVALID_INPUT);  

}
```

***共享主机***

如果您还没有感到足够的多疑，请考虑当您传递给命令行脚本的参数包含敏感信息时会发生什么。例如，考虑执行以下命令:

```
$ mysqldump --user=harryf -password=secret somedatabase > somedatabase.sql
```

这使得我的用户名和密码在系统上运行的进程列表中可用，并且通过`ps`命令对所有人可见。对于某人来说，编写一个自动收集这类信息的程序并不难。

当我们从 PHP 脚本执行程序时，同样的问题也会出现。

为了演示，这里有一个产生“长时间等待”的例子(大多数见习机械师的第一个任务是去请求他们的主管长时间等待…)；基本上，这个脚本会“逗留”足够长的时间，让你用`ps`命令看到它。这也是一个观看[PEAR::Console _ progress bar](http://pear.php.net/package/Console_ProgressBar)运行的好机会:

```
#!/usr/local/bin/php  

<?php  

# Include PEAR::Console_Getopt  

require_once 'Console/Getopt.php';  

# Include PEAR::Console_ProgressBar  

require_once 'Console/ProgressBar.php';  

# Exit codes  

define('INVALID_PHP_SAPI',3);  

define('INVALID_LOGIN',4);  

# Valid username / password  

define('USERNAME','harryf');  

define('PASSWORD','secret');  

//--------------------------------------------------------------------------------------  

/**  

* Displays the usage of this script  

* Called when -h option specified or on illegal option  

*/  

function usage() {  

  $usage = <<<EOD  

Usage: ./longweight.php [OPTION]  

Builds long weights.  

  -l=LENGTH  Length of weight  

  -u=USERNAME  Username  

  -p=PASSWORD  Password  

  -h    Display usage  

EOD;  

  fwrite(STDOUT,$usage);  

  exit(0);  

}  

//--------------------------------------------------------------------------------------  

/**  

* Gets the options specified by the user  

*/  

function getOptions() {  

  $args = Console_Getopt::readPHPArgv();  

  // Could be an error with older PHP versions and the CGI SAPI  

  if ( PEAR::isError($args) ) {  

    fwrite(STDERR,$args->getMessage()."n");  

    exit(INVALID_PHP_SAPI);  

  }  

  // Compatibility between "php longweight.php" and "./longweight.php"  

  if ( realpath($_SERVER['argv'][0]) == __FILE__ ) {  

    $options = Console_Getopt::getOpt($args,'hl:u:p:');  

  } else {  

    $options = Console_Getopt::getOpt2($args,'hl:u:p:');  

  }  

  // Check for invalid options  

  if ( PEAR::isError($options) ) {  

    fwrite(STDERR,$options->getMessage()."n");  

    usage();  

  }  

  // Set defaults for length, username and password  

  $ret_options = array(  

    'length' => 10,  

    'username'=> NULL,  

    'password'=> NULL  

  );  

  // Loop through the user provided options  

  foreach ( $options[0] as $option ) {  

    switch ( $option[0] ) {  

      case 'h':  

        usage();  

      break;  

      case 'l':  

        $ret_options['length'] = $option[1];  

      break;  

      case 'u':  

        $ret_options['username'] = $option[1];  

      break;  

      case 'p':  

        $ret_options['password'] = $option[1];  

      break;  

    }  

  }  

  return $ret_options;  

}  

//--------------------------------------------------------------------------------------  

/**  

* Validates the user. If not username and password was provided  

* by options, prompts user to enter them  

*/  

function validateUser($username,$password) {  

  if (is_null($username) ) {  

    fwrite(STDOUT,'Enter username: ');  

    $username = trim(fgets(STDIN));  

  }  

  if (is_null($password) ) {  

    fwrite(STDOUT,'Enter password: ');  

    $password = trim(fgets(STDIN));  

  }  

  if ( !(($username == USERNAME) & ($password == PASSWORD)) ) {  

    exit(INVALID_LOGIN);  

  }  

}  

//--------------------------------------------------------------------------------------  

/**  

* Returns an instance of PEAR::Console_ProgressBar  

*/  

function & getProgressBar($length) {  

  // Progress bar display (see documentation)  

  $display = 'Weight preparation %fraction% [%bar%] %percent% complete';  

  // Indicator for progress  

  $progress = '+';  

  // Indicator for what's remaining  

  $remaining = '-';  

  // Width of the progress bar  

  $width = '50';  

  return new Console_ProgressBar($display,$progress,$remaining,$width,$length);  

};  

//--------------------------------------------------------------------------------------  

// Get the command line options  

$options = getOptions();  

// Make sure we have a valid user (exits if not)  

validateUser($options['username'],$options['password']);  

fwrite(STDOUT, "Preparing Long Weight of length {$options['length']}n");  

$PBar = & getProgressBar($options['length']);  

// Loop for the length of the weight  

for ($i=0;$i<$options['length'];$i++) {  

  // Update the progress bar  

  $PBar->update($i+1);  

  sleep(1);  

}  

fwrite(STDOUT,"nDid you enjoy your long weight?n");  

exit(0);  

?>
```

文件名:longweight.php

如果您觉得需要长时间等待，现在可以通过从命令行输入以下命令来获得:

```
$ ./longweight.php -l 30 -u harryf -p secret
```

该脚本将在进程列表中停留 30 秒，所有人都可以看到用户名和密码。

我现在用另一个脚本执行这个命令，例如:

```
#!/usr/local/bin/php  

<?php  

shell_exec('./longweight.php -l 30 -u harryf -p secret');  

fwrite(STDOUT,"Long weight finishedn");  

exit(0);  

?>
```

文件名:execlongweight1.php

现在，从命令行，在后台运行脚本并检查进程列表:

```
$ ./execlongweight1.php &  

$ ps -eo pid,ppid,cmd | grep longweight
```

我得到这样的输出:

```
26466  1589 /usr/local/bin/php ./execlongweight1.php  

26467 26466 /usr/local/bin/php ./longweight.php -l 30 -u harryf -p secret
```

我格式化进程列表的方式是在第一列显示进程 id，在第二列显示*父*进程 ID，在第三列显示正在执行的命令。您可以看到,`execlongweight1.php`脚本被赋予了进程 ID 26466。还可以看到`longweight.php`的父进程 ID 是 26466；它是由`execlongweight1.php`剧本产生的。对于任何想看的人来说，用户名和密码都一览无遗…

因为我的`longweight.php`脚本也接受通过`STDIN`交互输入用户名和密码，所以更好的方法是在写入模式下使用`popen()`执行脚本:

```
#!/usr/local/bin/php  

<?php  

$fp = popen('./longweight.php -l 30','w');  

fwrite($fp,"harryfn");  

fwrite($fp,"secretn");  

fclose($fp);  

exit(0);  

?>
```

文件名:execlongweight2.php

现在，当我们执行`cps`命令时，我们会看到以下内容:

```
26512  1589 /usr/local/bin/php ./execlongweight2.php  

26513 26512 /usr/local/bin/php ./longweight.php -l 30
```

用户名和密码不再可见。

不幸的是，同样的*不能与`mysqldump`一起工作，后者从不同于`STDIN`的来源读取用户输入。下一个最好的解决方案是在您的 Unix 帐户主目录中放置一个名为`.my.cnf`的文件，其中包含您的 MySQL 用户名和密码。*

```
[client]  

user=harryf  

password=secret
```

文件名:~/.my.cnf

`mysqldump`实用程序将自动从`.my.cnf`中读取用户名和密码，使您不必在命令行中暴露它们。

##### 与环境打交道

没有哪个 PHP 脚本是孤立的，尤其是当它被用来执行其他程序的时候。Unix shell 提供了自己的“内存”，您可以在其中存储环境变量。许多变量都是预定义的，有些变量，比如 PATH 变量，对于让 PHP 脚本“天真地”执行外部程序是必不可少的。

优秀的 [LINUX: Rute 用户指南和说明](http://www.icon.co.za/~psheer/book/index.html.gz)在这里提供了常见环境变量[的概要。](http://www.icon.co.za/~psheer/book/node12.html.gz#SECTION001290000000000000000)

当您从命令行执行 PHP 脚本时，它*继承了*在您的 shell 中定义的环境变量。这意味着您可以使用`export`命令设置一个环境变量，如下所示:

```
$ export SOMEVAR='Hello World!'
```

注意，我们使用单引号，而不是双引号！

现在，让我们执行下面的 PHP 脚本:

```
#!/usr/local/bin/php   

<?php   

fwrite(STDOUT,"The value of SOMEVAR is ".getenv('SOMEVAR')."n");   

exit(0);   

?>
```

文件名:env1.php

您将看到您分配给`SOMEVAR`的值。函数允许你读取环境变量。

您还可以使用 [putenv()](http://www.php.net/putenv) 函数从 PHP 脚本创建或修改环境变量，但是有一个重要的警告:您不能修改父进程环境，只能修改您的脚本可以访问的本地副本。

继续上一个例子，让我们执行下面的 PHP 脚本:

```
#!/usr/local/bin/php   

<?php   

fwrite(STDOUT,"The value of SOMEVAR is ".getenv('SOMEVAR')."n");   

putenv("SOMEVAR='Goodbye World!'");   

fwrite(STDOUT,"The value of SOMEVAR is ".getenv('SOMEVAR')."n");   

exit(0);   

?>
```

文件名:env2.php

在执行这段代码时，我们将在第二次调用`fwrite()`时看到`SOMEVAR`的修改值。但是，如果我在脚本执行完毕后从 shell 中键入以下内容，我会看到原始值“Hello World！”

```
$ echo $SOMEVAR
```

乍一看，你可能会认为这使得`putenv()`毫无用处。当您的 PHP 脚本需要为它将要执行的另一个程序建立环境时，它就变得很重要。看看这个例子:

```
#!/usr/local/bin/php   

<?php   

putenv("SOMEVAR='Goodbye World!'");   

fwrite(STDOUT,"Executing env1.phpn");   

fwrite(STDOUT,shell_exec('./env1.php'));   

exit(0);   

?>
```

文件名:env3.php

因为脚本`env1.php`从其父进程`env1.php`的*继承了环境，所以它看到了`SOMEVAR`的修改值并显示“再见世界！”*

换句话说，环境变量可以用来在程序之间传递信息。您可以定义一个环境变量`INSTALL_DIR`，它指向多个脚本应该安装的目录。某个主脚本，也许是`install.php`，设置这个变量，然后执行其他脚本来执行进一步的安装任务，比如将源代码档案提取到安装目录中，或者用[PHP document](http://www.phpdoc.org)创建 API 文档。

当从 Apache 下运行的 PHP 脚本执行外部程序时，了解环境变量也很重要。通常，脚本将在一个特殊的 Unix 用户环境中运行(“nobody”、“wwwrun”或类似的环境)，该用户只有很少的文件系统权限，并且可能是一个非标准的用户环境。

这可能意味着，例如，当您试图在 Apache 下执行一个您通常可以找到自己的`PATH`的程序时，什么也看不到。可能你只需要指定程序的完整路径或者使用`putenv()`临时更新 path 变量。也可能是权限问题，这是另一回事。

请注意，如果您控制服务器，并且需要从 Apache 下运行的 PHP 脚本执行外部程序，那么可能值得研究一下 [sudo](http://www.courtesan.com/sudo/) ，它提供了一种机制，通过这种机制，一个用户可以使用与另一个用户相同的环境和权限执行单个命令(显然，在这样做时，您需要非常小心)。

##### 过程控制

前面，我们看到了`popen()`函数，它允许我们读取*或*写入外部程序。有时，您需要两者都做，这就是 [proc_open()](http://www.php.net/proc_open) 函数派上用场的地方。
这里有一个简单的程序，它从`STDIN`逐行读取输入，并将每个单词的第一个字母转换成大写，然后将该行写回`STDOUT`。因为这是一个有点不稳定的程序，它不喜欢所有的大写字母。如果它发现一个大写单词，它会向`STDERR`抱怨并删除这个单词:

```
#!/usr/local/bin/php   

<?php   

while ( ($line = trim(fgets(STDIN))) != 'exit' ) {   

  $words = explode(' ',$line);   

  foreach ( $words as $index => $word ) {   

    if ( strcmp($word,STRTOUPPER($word)) != 0 ) {   

      $words[$index] = ucfirst($word);   

    } else {   

      fwrite(STDERR, "Bleugh! $word is all upper case!n");   

      unset($words[$index]);   

    }   

  }   

  $line = implode(' ',$words);   

  fwrite(STDOUT, $line."n");   

}   

exit(0);   

?>
```

文件名:ucfirst.php

能够从另一个 PHP 脚本与这个程序对话显然更棘手。我们需要同时连接到`STDIN`、`STDOUT`和`STDERR`。回车:`proc_open` …

```
#!/usr/local/bin/php   

<?php   

// Describes how proc_open should open the external program   

$Spec = array (   

  0 => array('pipe','r'), /* STDIN */   

  1 => array('pipe','w'), /* STDOUT */   

  2 => array('file','./badwords.log','a'), /* STDERR */   

);
```

首先，我需要定义一个“描述符规范”，它是一个具有特殊结构的数组。它告诉`proc_open()`如何连接到外部程序的`STDIN`、`STDOUT`和`STDERR`。它应该是一个数组的数组，母数组包含三个元素，分别对应`STDIN`、`STDOUT`和`STDERR`。每个子数组指定应该如何使用它们，标识“资源类型”:shell IO 的“管道”或“文件”，以及描述应该如何使用管道或文件的其他参数。这可能一开始看起来有点“笨拙”，但是你会习惯的。

在这里，我定义了一个描述符规范，它说，“使`STDIN`对外部程序可用，以便*读取*(该规范是从外部程序的角度来看的，尽管这个脚本将写入管道)，使`STDOUT`可用，这样外部程序可以向它写入输出，然后将`STDERR`指向文件‘badwoods . log’并向它追加任何错误消息。”

让我们转到程序的其余部分，这里“描述符规范”的目的应该变得更清楚了:

```
// Handles to external programs STDIN, STDOUT and STDERR placed here   

$handles = array();   

// Open the process using the descriptor spec   

$process = proc_open('./ucfirst.php', $Spec, $handles);   

// Some lines to input to ucfirst.php   

$lines = array (   

  'hello world!',   

  'a bad EXAMPLE of upper case.',   

  'goodbye world!',   

);   

foreach ( $lines as $line ) {   

  // Write the line to the STDIN of ucfirst.php   

  fwrite($handles[0],$line."n");   

  $response = fgets($handles[1]);   

  // Display the response to the user   

  fwrite(STDOUT, $response);   

}   

// Issue the exit command to the ucfirst.php program   

fwrite($handles[0],"exitn");   

// Clean up   

fclose($handles[0]);   

fclose($handles[1]);   

proc_close($process);   

exit(0);   

?>
```

文件名:proc_open.php

这里有几点需要注意。执行`proc_open()`返回一个代表*进程*的资源，但是为了与进程交互，您需要读取或写入存储在`$handles`数组中的句柄，数组的元素对应于描述符规范数组中的元素。

注意，你需要特别小心地关闭管道的把手，就像我在上面的清理部分所做的那样。一个更聪明的方法可能是注册一个关闭函数来负责清理—参见[register _ shut down _ function()](http://www.php.net/register_shutdown_function)。

现在，当我们执行`proc_open.php`时，输出如下所示:

```
Hello World!   

A Bad Of Upper Case.   

Goodbye World!
```

`badwords.log`文件包含:

```
Bleugh! EXAMPLE is all upper case!
```

对于 PHP5，`proc_open()`支持一个新的描述符“pty”，这将允许它控制从除了`STDIN`和`STDOUT`之外的其他位置读取的程序(例如，这应该可以解决向`mysqldump`发送密码的问题)。

PHP5 还引入了另外三个函数: [proc_get_status()](http://www.php.net/proc_get_status) ，它提供关于外部程序当前正在做什么的信息(最重要的是，提供它的进程 ID)；[proc _ termin ate()](http://www.php.net/proc_terminate)，它允许你向进程发送一个终止信号(下面将详细介绍)；以及 [proc_nice()](http://www.php.net/proc_nice) ，它允许你改变操作系统分配给程序的优先级(它获得了多少处理器时间)；通常这是一个介于 20(最低优先级)和-20(最高优先级)之间的数字。注意`proc_nice()`其实和`proc_open()`没有关系；这意味着控制当前 PHP 脚本的优先级，而不是外部程序的优先级。在 shell 中，可以使用`renice`命令达到同样的效果。

***信号***

Posix 函数提供各种工具从操作系统获取有用的信息(注意 Posix 扩展在 Windows 上不可用)。从与外部程序一起工作的角度来看，也许最有用的是 [posix_kill()](http://www.php.net/posix_kill) 函数，它允许您在程序执行时向程序发送*信号*。

如果您使用过 Unix 很多次，您可能会遇到`kill`命令，也许是它最著名的版本:

```
$ kill -9 <pid>
```

“-9”表示向由其 ID 标识的进程发送“SIGKILL”信号(立即退出)。Rute 用户教程在这里提供了一些常见的信号[(事实上，值得一读关于](http://www.icon.co.za/~psheer/book/node12.html.gz#SECTION001260000000000000000)[进程和环境变量](http://www.icon.co.za/~psheer/book/node12.html.gz)的整章)。

`posix_kill()`函数的工作方式与`kill`命令相同:您通过进程 ID 识别一个进程，并向它发送一个信号。通常情况下，你应该发送 15 号信号(`SIGTERM`，这给了你正在杀死的程序一个机会在它退出之前清理。`SIGKILL`信号(9)不能被程序截获；它立即死亡，这可能意味着它留下了一个烂摊子。

关于信号的更多信息，也值得看一看 PHP 的[进程控制扩展](http://www.php.net/pcntl)(默认情况下禁用)，它提供了允许脚本处理信号的功能(以及其他功能)。

*警告:*在 Apache 下运行 PHP 时不要使用 pcntl 函数！如果你这样做，奇怪的事情就会发生。

这里有一个“捕捉”到`SIGTERM`信号的脚本:

```
#!/usr/local/bin/php   

<?php   

// Required from PHP 4.3.0+: see manual   

declare(ticks = 1);   

function cleanUp() {   

  fwrite(STDOUT,"Performing clean up...n");   

  exit(0);   

}   

// Map the SIGTERM signal to the cleanup callback function   

pcntl_signal(SIGTERM, "cleanUp");   

// Illegal - you cant catch the KILL signal   

# pcntl_signal(SIGKILL, "cleanUp");   

while (1) {   

  // Loop forever   

}   

?>
```

Filename: pcntl1.php

我从命令行执行，如下所示:

```
$ ./pcntl1.php &
```

这指示 shell 在后台运行该进程。在执行之后，它立即告诉我我的脚本运行的进程 ID，例如:

```
[6] 2840
```

“2840”是进程 ID。我现在键入以下内容:

```
$ kill 2840
```

我看到了下面:

```
Performing clean up...
```

然后程序退出(注意`kill`命令默认为所需的`SIGTERM`信号，以防您感到疑惑)。

您可能会发现捕捉像`SIGINT`这样的信号很有用，它对应于用户按 CTRL+C 退出您的程序；这将给你机会回滚你的脚本所做的任何事情。

***更有 PCNTL 招数***

另一个值得注意的函数是 [pcntl_exec()](http://www.php.net/pcntl_exec) 。与我们迄今为止看到的任何程序执行函数不同，使用`pcntl_exec()`执行外部程序会导致程序*替换*执行它的脚本，而不是作为子进程运行。

例如，假设我执行以下脚本:

```
#!/usr/local/bin/php   

<?php   

fwrite(STDOUT,"My process ID is ".posix_getpid()."n");   

pcntl_exec('./longweight.php',array('-l15','-uharryf','-psecret'));   

?>
```

Filename: pcntl2.php

然后，我在一个单独的 shell 中执行下面的代码:

```
$ ps -ef | grep longweight.php
```

`longweight.php`的进程 ID 是与从`pcntl2.php`报告的进程 ID 相同的*。此外，如果我执行以下命令，我什么也看不到:*

```
$ ps -ef | grep pcntl2.php
```

我什么也没看到的原因是因为剧本被替换成了`longweight.php`。

这种行为在编写“包装”脚本时非常有用，这些脚本为另一个脚本建立了一个环境，然后悄悄地消失了。

PCNTL 函数有用的另一个领域是如果你用 PHP(一个服务器)编写某种“守护”进程。信不信由你，有很多 HTTP 服务器完全是用 PHP 编写的，比如 [Nanoweb](http://nanoweb.si.kz/) 和 [PEAR::HTTP_Server](http://pear.php.net/package/HTTP_Server) 。

使用 [pcntl_fork()](http://www.php.net/pcntl_fork) 函数，您可以编写一个充当服务器的脚本，但是创建子进程来处理传入的请求，就像 Apache Web server (1.x)一样。这允许我的服务器并行处理多个任务，减少了延迟。不幸的是，`pxntl_fork()`不适合进行简短的讨论，所以如果您需要的话，我会让您做进一步的研究。

##### 轮到你了

现在，您应该对如何让您的命令行 PHP 脚本“调谐”到基于 Unix 的系统上可用的大量工具和实用程序有了很好的了解。如果您是 Unix 新手，有很多东西需要学习，在这篇短文中，我只能介绍一些主题。不过，希望你能感觉到自己有多少权力可以支配。

如果你有兴趣了解更多，我强烈推荐 Paul Sheer 的 [Rute 用户指南和说明](http://www.icon.co.za/~psheer/book/index.html.gz)，也可以从亚马逊获得[。](http://www.amazon.com/exec/obidos/tg/detail/-/0130333514/)* 

## *分享这篇文章**