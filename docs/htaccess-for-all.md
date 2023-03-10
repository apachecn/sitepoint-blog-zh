# 。面向所有人的 htaccess

> 原文：<https://www.sitepoint.com/htaccess-for-all/>

Htaccess(超文本访问)是一个简单的配置文件，它允许设计人员、开发人员和程序员等更改 Apache Web 服务器的配置，以便提供额外的功能。这种功能可以包括重定向用户、URL 重写和提供密码保护的目录；但是它可以做得更多。

所以让我们开始吧…

## 创建和上载。htaccess 文件

创建一个。htaccess 文件非常简单。

只需打开记事本或类似的基于文本的程序，关闭自动换行，添加代码，并以通常的方式保存文件。

例如，您可以称之为:

```
htaccess.txt
```

将文件上传到 web 服务器上的相关目录，然后像这样重命名它:

```
.htaccess
```

记住。htaccess 文件应该使用 644 权限并以 ASCII 模式上传。如果你的。htaccess 文件不工作，那么你应该联系你的系统管理员或网站托管公司，并确保他们已启用。因为一些虚拟主机公司不允许在没有事先许可的情况下使用 htaccess。不幸的是。htaccess 无法在基于 Windows 的服务器上运行。

## 使用。htaccess

重要的是要记住。htaccess 文件将影响它所在的目录和所有产生的子目录。因此，如果你加上你的。htaccess”文件添加到“网站根目录”，那么它将影响所有后续文件夹，如下所示:

```
http://www.yourdomain.com/
| -- directory1
| -- directory2
| -- directory3
|    | -- directory3/childdirectory1
|    | -- directory3/childdirectory2
| -- .htaccess
| -- index.html
```

但是，如果您将。htaccess '文件在 http://www.yourdomain.com/directory1 然后的功能。“htaccess”将仅限于该文件夹及其所有子文件夹。例如:

```
http://www.yourdomain.com/
| -- directory1
|    | -- directory1/childdirectory1
|    | -- directory1/childdirectory2
|    | -- directory1/childdirectory3
|    |    | -- directory1/childdirectory3/newdirectory1
|    |    | -- directory1/childdirectory3/newdirectory2
|    | -- .htaccess
|    | -- index.html
```

编辑完您的。它可能看起来有点复杂，所以我建议实现注释。为此，只需将散列符号放在每一行的开头，如下所示:

```
# comment here
# another comment here
```

## 有用的片段

首先，现在是片段时间…
(尽管其中一两个片段是 Apache 的严格指令)

### 目录索引

您可以使用以下命令更改目录的默认索引文件:

```
DirectoryIndex welcome.html welcome.php
```

### 自定义错误页面

您可以使用以下命令将用户重定向到错误页面:

```
ErrorDocument 404 error.html
```

你可以这样扩展它:

```
ErrorDocument 400 /400.html
ErrorDocument 401 /401.html
ErrorDocument 403 /403.html
ErrorDocument 404 /404.html
ErrorDocument 500 /500.html
ErrorDocument 502 /502.html
ErrorDocument 504 /504.html
```

但是记得创建你的错误页面！

### 在你的 URL 中去掉 www

通过使用以下方法消除对“www”的需求，保持网站的一致性:

```
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} ^www.yourdomain.com [NC]
RewriteRule ^(.*)$ http://yourdomain.com/$1 [L,R=301]
```

### 为您的服务器设置时区

```
SetEnv TZ Europe/London
```

### 控制对文件的访问

大多数人都会记得。htaccess 最常用于限制或拒绝对单个文件和文件夹的访问，您可以这样做:

```
deny from all
```

但是，如果您想更具体地禁止特定的 IP 地址，那么您可以使用:

```
order allow,deny
deny from XXX.XXX.XXX.XXX
allow from all
```

或者，对于多个 IP 地址，您可以使用:

```
allow from all
deny from 145.186.14.122
deny from 124.15
```

### 301 永久重定向

担心那些老链接？然后尝试:

```
Redirect 301 /olddirectory/file.html http://www.domainname.com/newdirectory/file.html
```

### 设定服务器管理员的电子邮件地址

通过使用以下代码，您可以为服务器管理员指定默认的电子邮件地址:

```
ServerSignature EMail
SetEnv SERVER_ADMIN webmaster@domain.com
```

### 检测药片并重定向

如果您想将基于平板电脑的用户重定向到特定网页或目录，请尝试:

```
RewriteCond %{HTTP_USER_AGENT} ^.*iPad.*$
RewriteRule ^(.*)$ http://yourdomain.com/folderfortablets [R=301]
RewriteCond %{HTTP_USER_AGENT} ^.*Android.*$
RewriteRule ^(.*)$ http://yourdomain.com/folderfortablets [R=301]
```

### 链接保护

担心热链接或只是想减少您的带宽使用？尝试尝试:

```
Options +FollowSymlinks
RewriteEngine On
RewriteCond %{HTTP_REFERER} !^$
RewriteCond %{HTTP_REFERER} !^http://(www.)?domainname.com/ [nc]
RewriteRule .*.(gif|jpg|png)$ http://domainname.com/img/hotlink_f_o.png [nc]
```

### 强制“文件另存为”

如果您希望强制用户下载文件，而不是在浏览器中查看，您可以使用:

```
AddType application/octet-stream .csv
AddType application/octet-stream .xls
AddType application/octet-stream .doc
AddType application/octet-stream .avi
AddType application/octet-stream .mpg
AddType application/octet-stream .mov
AddType application/octet-stream .pdf
```

或者你可以简化为:

```
AddType application/octet-stream .avi .mpg .mov .pdf .xls .mp4
```

### 重写 URL

如果你想让你的网址更容易阅读(即改变 content.php？id=92 到 content-92.html)您可以实现以下“重写”规则:

```
RewriteEngine on
RewriteRule ^content-([0-9]+).html$ content.php?id=$1
```

### 将浏览器重定向到 https

这对那些刚刚安装了 SSL 证书的人总是有用的:

```
RewriteEngine On
RewriteCond %{HTTPS} !on
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

### 激活 SSI

如果要为 HTML 和/或 SHTML 文件类型激活 SSI，请尝试:

```
AddType text/html .html
AddType text/html .shtml
AddHandler server-parsed .html
AddHandler server-parsed .shtml
AddHandler server-parsed .htm
```

### 禁用或启用目录浏览

```
# disable directory browsing
Options All -Indexes
# enable directory browsing
Options All +Indexes
```

### 更改字符集和语言头

对于想要更改特定目录的当前字符集和语言的用户，请使用:

```
AddDefaultCharset UTF-8
DefaultLanguage en-GB
```

### 阻止不需要的推荐

如果您想要阻止不受欢迎的访问者访问特定网站或一系列网站，您可以使用:

```
<IfModule mod_rewrite.c>
 RewriteEngine on
 RewriteCond %{HTTP_REFERER} website1.com [NC,OR]
 RewriteCond %{HTTP_REFERER} website2.com [NC,OR]
 RewriteRule .* - [F]
</ifModule>
```

### 阻止不需要的用户代理

通过以下方法，您可以阻止某些僵尸程序或蜘蛛搜索您的网站，从而节省带宽:

```
<IfModule mod_rewrite.c>
SetEnvIfNoCase ^User-Agent$ .*(bot1|bot2|bot3|bot4|bot5|bot6|) HTTP_SAFE_BADBOT
SetEnvIfNoCase ^User-Agent$ .*(bot1|bot2|bot3|bot4|bot5|bot6|) HTTP_SAFE_BADBOT
Deny from env=HTTP_SAFE_BADBOT
</ifModule>
```

### 阻止对各种文件的访问

如果您想保护特定的文件，甚至阻止对。htaccess 文件，尝试自定义以下代码:

```
<Files privatefile.jpg>
 order allow,deny
 deny from all
</Files>

<FilesMatch ".(htaccess|htpasswd|ini|phps|fla|psd|log|sh)$">
 Order Allow,Deny
 Deny from all
</FilesMatch>
```

### 最后…

仅仅出于安全的原因，我认为将。htaccess 文件非常有用:

```
AccessFileName ht.access
```

在写这篇文章时，我试图强调 htaccess 可以使用的功能范围。当然，我还没有涵盖所有内容，但是正如你所看到的。htaccess 可能是一个旧的工具，但它仍然有一个重要的作用，以提高您的网站。

## 分享这篇文章