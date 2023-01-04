# 发现安全漏洞

> 原文：<https://www.sitepoint.com/spot-the-security-hole/>

如果你是从《科技时报》的时事通讯中看到这个页面的，我们深表歉意。我们的一些环节出了问题。您可能在追求以下目标之一:

*   **[参数:一个 JavaScript 古怪](https://www.sitepoint.com/arguments-a-javascript-oddity/)**
*   **[Mozilla Prism:用 UI 卡在中间](https://www.sitepoint.com/mozilla-prism-stuck-in-the-middle-with-ui/)**
*   **[Adobe 集成运行时:不止是热风](https://www.sitepoint.com/adobe-air-more-than-just-hot-air/)**

我们现在让你回到我们的常规节目…

这里有一个 PHP 脚本:

```
 <?php
# Common include file for MySQL
require("auth_conn_inc_reg.php");

$valid = false;

if (isset($_SERVER['PHP_AUTH_USER']) &&
	isset($_SERVER['PHP_AUTH_PW']) ) {

	$sql = "SELECT * FROM users WHERE
		logins='{$_SERVER['PHP_AUTH_USER']}' AND
		password='{$_SERVER['PHP_AUTH_PW']}'";

	$mysql_result = mysql_query($sql,$connection);

	$num_rows = @mysql_num_rows($mysql_result);

	if ( $num_rows != 0 ) {
		$valid = true;
	}
}

if ( !$valid ) {
	header ("WWW-Authenticate: Basic realm="Restricted"");
	header ("HTTP/1.0 401 Unauthorized");
	echo "Authorization required";
	exit();

} else {

	# Valid user - do stuff here

}
?>
```

发现问题？

阅读这篇文章让我感到沮丧的是，它是本月英国一份 Linux 杂志上的一篇文章的一部分(我将省略它的名字；这是一本好杂志)。当然每个人都会犯错误，我自己也不例外，但是这个特殊的例子是一个经典的例子，也是 PHP 在安全性方面受到指责的原因之一。

怎样才能阻止这种事情在未来一再发生？也许在[http://www.php.net/mysql_query](http://www.php.net/mysql_query)上需要一条大消息，比如“在你使用这个函数之前，确保你已经阅读了关于 [mysql_escape_string()](http://www.php.net/mysql_escape_string) 。或许 mysql_escape_string()上的页面可以更好地解释为什么它很重要？

## 分享这篇文章