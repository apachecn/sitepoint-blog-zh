# proc_open 和 pty 的

> 原文：<https://www.sitepoint.com/proc-open-and-ptys/>

我收件箱里的一些东西(最好自下而上阅读),是从 [CLI 第 2 部分](https://www.sitepoint.com/article/php-command-line-2)开始的，有人要吗？

+++++++++++++++++++++++++

现在我正在使用 PHP 5 . 0 . 1 版本

PHP 版本
[http://www.php.net/ChangeLog-5.php#5.0.1](http://www.php.net/ChangeLog-5.php#5.0.1)

> 版本 5.0.0 候选版本 2 * * 2004 年 4 月 25 日

> (…)

> *增加了对 proc_open()的 pty 支持。(Wez)

手动
[http://www.php.net/manual/en/function.proc-open.php](http://www.php.net/manual/en/function.proc-open.php)

> PHP 5RC2 为带有 Unix98 ptys 的系统引入了 pty 支持。这个
> 允许你的脚本与期望与终端对话的应用程序进行交互。pty 像管道一样工作，但是是双向的，
> ，所以不需要指定读/写模式。下面的例子
> 显示了如何使用 pty 请注意，您不必让所有描述符
> 与 pty 对话。还要注意，即使
> pty 被指定了 3 次，也只创建了一个 pty。在 PHP 的未来版本中，
> 可能不仅仅是读写 pty。
> (…)
> descriptorspec 中的文件描述符编号不限于 0、1
> 和 2——您可以指定任何有效的文件描述符编号，它将被
> 传递给子进程。这允许您的脚本与作为“协同进程”运行的其他脚本进行互操作。*特别是，这对于以更安全的方式向 PGP、GPG 和 openssl 等程序传递密码非常有用。对于读取由辅助文件描述符上的程序提供的状态信息
> 也很有用。

对吗？这是我的剧本…

 `< ? echo "this is a testr"; // Create a pseudo terminal for the child process $descriptorspec = array( 0 => array("pty"),
1 => array("pty"),
2 => array("pty")
);
$process = proc_open("/usr/bin/passwd", $descriptorspec, $pipes);
if (is_resource($process)) {
fwrite($pipes[0], "mypasswd");
fclose($pipes[0]);`

 `而(！feof($ pipes[1]){
echo fgets($ pipes[1]，1024)；
}
fclose($ pipes[1])；
//为了避免死锁
$ return _ value = proc _ close($ process)，在调用
// proc_close 之前关闭任何管道是很重要的；` 

`echo "command returned $return_valuen";
}
?>`

但是…当我运行程序时:

 `username:~/Sites/Grid david$ php pty.php
this is a test
Warning: proc_open(): pty is not a valid descriptor spec/mode in
/Users/david/Sites/Grid/pty.php on line 10`

`LINE 10
$process = proc_open("/usr/bin/passwd", $descriptorspec, $pipes);`

你能帮我一下吗？

你好，大卫，

>

>没有确定是新手问题；)这不是我试过的，而是 PHP

> 5+应该

>(理论上)允许使用 proc_open 函数，在

>第二个 PHP

> CLI 文章。手册上有一些注释
([http://www.php.net/proc_open](http://www.php.net/proc_open))。

>您需要一个描述符规范，类似于:

>

> $descriptorspec = array(

> 0 => array("pty ")，

> 1 => array("pty ")，

> 2 = >数组(" pty ")

> );

>

会有兴趣听听你的进展。

>

>> ———————————————————–

>>来自:《大卫》

>>留言:这是个新手问题，可能我需要一个大 RTFM。它是

>>非常有趣和有用你的两篇文章“PHP 上的命令

>>行”。

>>

>>我有一个很大的疑问…我们正在使用 PHP 4.3.3，我必须

>>编写一个 PHP 脚本来更改 unix 用户密码。

>>

类似这样的事情

> >[http://www.faqs.org/faqs/unix-faq/faq/part3/section-9.html](http://www.faqs.org/faqs/unix-faq/faq/part3/section-9.html)

>>

>>用 PHP 可以做到吗？谢谢你

>>不断进步。

## 分享这篇文章