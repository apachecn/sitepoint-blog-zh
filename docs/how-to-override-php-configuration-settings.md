# 如何覆盖 PHP 配置选项

> 原文：<https://www.sitepoint.com/how-to-override-php-configuration-settings/>

配置 PHP 很容易。你几乎可以在 php.ini 配置文件中改变解释器的任何方面，例如修改错误处理，增加内存使用等。

不幸的是，当您将应用程序转移到一个实时托管环境或者将代码分发给客户时，可能会出现问题。ISP 通常会锁定 php.ini 配置文件——尤其是在共享主机上。这可能导致您的应用程序失败。

幸运的是，没有必要升级到昂贵的专用服务器。下面的两种方法允许您在应用程序中覆盖 PHP 设置。

## Apache 模块指令

大多数 ISP 在 Linux 或 Unix 平台上提供 Apache web 服务器托管。希望他们还在 Apache 的 httpd.conf 配置中授予了“AllowOverride Options”或“AllowOverride All”权限。这允许您创建一个。htaccess 文件覆盖了默认的 Apache 和 PHP 配置。

内允许两个 PHP 指令。htaccess:

*   PHP _ flag*<boolean-flag-name>on | off*
*   PHP _ value*<flag-name><flag-value>*

php_flag 应该用于开/关值，而 php_value 可以用于任何其他值。比如下面的。htaccess 文件将禁用全局，将最大文件上传大小设置为 20MB，并允许 PHP 脚本运行 10 分钟(600 秒):

```
 php_flag register_globals off
php_value upload_max_filesize 20M
php_value max_execution_time 600 
```

但是，该解决方案不能在所有 Apache 安装或其他 web 服务器(如 IIS)上运行。

## PHP 运行时配置

一个更加可移植的、与服务器无关的解决方案是 PHP 的 ini_set 函数。—它允许您在运行时更改应用程序中的设置。该函数接受两个参数:[**ini _ set**(*flag-name*， *flag-value* )](http://php.net/manual/en/function.ini-set.php) ，例如

```
 <?php
ini_set('register_globals', 0);
ini_set('upload_max_filesize', '20M');
ini_set('max_execution_time', 600);
?> 
```

布尔值、数字和字符串可以互换使用——PHP 会尝试将值转换成合适的类型。

有几个相关的功能可用:

[**ini _ get**(*flag-name*)](http://www.php.net/manual/en/function.ini-get.php)
返回配置值。我建议检查您的配置更改并采取适当的措施。不要假设 ini_get()会一直工作。

[**ini_get_all** ([ *扩展*)](http://www.php.net/manual/en/function.ini-get-all.php)
以关联数组的形式返回所有的配置值。可选的*扩展*参数返回特定于该扩展的选项，例如‘allow _ URL _ fopen’。

[**get _ CFG _ var**(*flag-name*)](http://www.php.net/manual/en/function.get-cfg-var.php)
从 php.ini 返回原来的配置值(不是任何在 htaccess 或通过 ini_set)。

[**ini _ restore**(*flag-name*)](http://www.php.net/manual/en/function.ini-restore.php)
将一个配置选项恢复到其初始值。

即将推出:*如何处理卸载的 PHP 扩展*。

将应用程序移植到另一台服务器时，PHP 配置是否给你带来过问题？

## 分享这篇文章