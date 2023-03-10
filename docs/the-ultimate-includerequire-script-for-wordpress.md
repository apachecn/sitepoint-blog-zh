# WordPress 的最终包含/要求脚本

> 原文：<https://www.sitepoint.com/the-ultimate-includerequire-script-for-wordpress/>

作为一名为我的客户创建高度定制产品的 WordPress 开发者，我发现自己在试图在 WordPress 框架内创建 PHP 脚本时一次又一次地遇到同样的问题。我喜欢使用 WordPress 的内置功能，但是我一遍又一遍地遇到同样的错误信息:“调用未定义的方法…”或者“无法打开流”

然后，根据您的需要，寻找您需要包含、要求、包含 _once 或要求 _once 的适当文件。一旦您找到了正确的文件，通常是 wp-includes 文件夹中的某个 PHP 文档，例如，您必须在脚本的开头包含/要求它——通常只是为了找到另一个“调用未定义的方法…”

为了节省时间，让您现在就开始创建代码*，而不是在搜索解决方案的几个小时(或几天)之后，我创建了一个简单的脚本，用它来启动我所有的定制文件。它处理我 99%的开发所需的所有适当的文件。*

 *### 警告

我只推荐在你开发你的插件、主题或者脚本的时候使用这个，然后在进入制作之前，找到你真正需要的文件。我*不*建议你一直运行这个脚本，因为它会对你可能不需要的文件进行不必要的调用。

我将带您浏览这个脚本，以便您可以看到我在做什么以及为什么。这也可以作为你以后的参考工具。

### 忘记 ABSPATH——走自己的路

我喜欢总是定义 ABSPATH，但是对于一些脚本和 WordPress 安装来说，它是断断续续的。我曾经使用简单的 *if* 测试来检查 ABSPATH 是否被定义，但是这也不总是有效:

[sourcecode language="php"]

/*注意:这是我不再做的事情*/

//定义 ABSPATH

如果(！已定义(' ABSPATH ')}

define('ABSPATH '，dirname(___FILE__)。'/');

}

[/sourcecode]

相反，我创建了我自己的 WordPress 安装路径，它要稳定得多:

[sourcecode language="php"]

$ location = $ _ SERVER[' DOCUMENT _ ROOT ']；

包括(＄位置。/WP-config . PHP’)；
包括(＄位置。/WP-load . PHP’)；
包括(＄位置。/WP-includes/pluggable . PHP ')；

[/sourcecode]

这假设你的 WordPress 安装在你网站的根目录下，比如 www.yoursite.com

如果不是这样，只需将字符串添加到$location 变量中，如下所示:

[sourcecode language="php"]

$ location = $ _ SERVER[' DOCUMENT _ ROOT ']。/your-WP-install '；

[/sourcecode]

### 三巨头

根据我的经验，在设置脚本时，有三个文件会导致我所有的问题。我称之为我的“三大”,因为一旦我包含或需要它们，当涉及到“调用未定义的方法”错误时，我的大多数问题都会消失。它们是:

1.  wp-config.php
2.  pluggable.php
3.  wp-load.php

一旦你有了这些，你会发现你的大多数错误都消失了。

[sourcecode language="php"]

//获得三巨头

包括(ABSPATH。WP-config . PHP’)；

包括(ABSPATH。WP-load . PHP’)；

包括(ABSPATH。WP-includes/pluggable . PHP’)；

[/sourcecode]

### 全局$wpdb

另一件有用的事情是确保您的$wpdb 是一个全局变量。这使得你创建的任何函数都可以访问 WordPress 数据库查询特性。这可以节省大量时间。

因为您包括了 wp-config.php 和 wp-db.php 文件，所以让$wpdb 正常工作应该没有任何问题，但是我仍然喜欢先运行一个 *if* 测试来检查:

[sourcecode language="php"]

//获得四大

全局$ wpdb

如果(！isset($wpdb))

{

包括(＄位置。/WP-config . PHP’)；

包括(＄位置。WP-includes/WP-db . PHP’)；

}

[/sourcecode]

### 测试脚本

如果你是从全新安装 WordPress 开始，你只有一个管理员用户。因此，尝试连接到 WordPress 数据库并查询用户是没有意义的。我构建了这个简单的测试来看看我们是否连接正确。如果是这样，你会在一个表格中看到你在 WordPress 注册的名字和电子邮件地址:

[sourcecode language="php"]

/*
测试上面的 include 语句是通过
在一个表格中列出所有管理员及其电子邮件
地址来工作的。
*/

//开始测试
$ args = array(//设置查询只获取 admins
' role ' =>' Administrator '，
' fields ' =>' all _ with _ meta '
)；

$ query = get _ users($ args)；//使用 get_users 调用

//设置表格
echo '<table cell padding = " 0 " cellspacing = " 0 " border = " 1 "><tr><thead><th>好听的名字</th><th>Email</th></thead></tr【T21’；
//为每个管理员创建一个表行
foreach($ query as $ query){
echo '<TD>'。$query- > user_nicename。</TD>TD>a href = " mailto:'。$query- >用户电子邮件。>’。$query- >用户电子邮件。</a></TD></tr>’；

}//End foreach
echo’</table>；//关闭表格标记

//结束测试–从这里注释掉或删除上面的“开始测试”

[/sourcecode]

如果测试成功了，你看到了你的名字和电子邮件地址，只需删除我们对测试脚本的注释，然后开始编码！

你可以把这个 PHP 文件放在 WordPress 文件夹中的任何地方，它就会工作。因此，对于初始测试和编程，这个脚本可以节省您大量的时间，否则您将花费大量的时间来解决基本的技术问题。

同样，一旦您的函数正常工作，并且只包含绝对必要的文件，您可能需要做一些细节工作。例如，如果你不需要包括 wp-config.php 文件，就不要！只需一行一行地注释掉代码，看看它是否能正常工作。留下你需要的，丢掉其余的。

希望这将为你节省大量时间，让你专注于编码，而不是 WordPress 开发所需文件的基本映射。

下面是完整的脚本，便于参考。只需复制，将文件保存在任何需要的地方，并享受完成工作的乐趣:

[sourcecode language="php"]

贾斯丁的魔法包

假设你正在使用服务器的根文件夹来安装你的
WordPress。如果没有，就在你正在使用的
WordPress 的位置添加子文件夹。

例如，将子文件夹添加到$location 变量:
$ location = $ _ SERVER[' DOCUMENT _ ROOT ']。/your-子文件夹'；
*/

$ location = $ _ SERVER[' DOCUMENT _ ROOT ']；

包括(＄位置。/WP-config . PHP’)；
包括(＄位置。/WP-load . PHP’)；
包括(＄位置。/WP-includes/pluggable . PHP ')；

全局$ wpdb

如果(！isset($wpdb) )
{
包含($location。/WP-config . PHP’)；
包括(地点。/WP-includes/WP-db . PHP ')；
}

/*
测试上面的 include 语句是通过
在一个表格中列出所有管理员及其电子邮件
地址来工作的。
*/

//开始测试
$ args = array(//设置查询只获取 admins
' role ' =>' Administrator '，
' fields ' =>' all _ with _ meta '
)；

$ query = get _ users($ args)；//使用 get_users 调用

//设置表格
echo '<table cell padding = " 0 " cellspacing = " 0 " border = " 1 "><tr><thead><th>好听的名字</th><th>Email</th></thead></tr【T21’；
//为每个管理员创建一个表行
foreach($ query as $ query){
echo '<TD>'。$query- > user_nicename。</TD>TD>a href = " mailto:'。$query- >用户电子邮件。>’。$query- >用户电子邮件。</a></TD></tr>’；

}//End foreach
echo’</table>；//关闭表格标记

//结束测试–从这里注释掉或删除上面的“开始测试”

？>

[/sourcecode]

你想更深入地研究 PHP 吗？查看我们的合作伙伴网站，[PHPMaster.com](https://www.sitepoint.com)。* 

## *分享这篇文章*