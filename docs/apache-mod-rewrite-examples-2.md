# 学习 Apache mod_rewrite: 13 个真实世界的例子

> 原文：<https://www.sitepoint.com/apache-mod-rewrite-examples-2/>

## 修改 _ 重写标志

mod_rewrite 使用“标志”来赋予我们的重写条件和规则额外的特性。我们使用方括号在条件或规则的末尾添加标志，并用逗号分隔多个标志。您需要熟悉的主要标志有:

*   `last|L`–最后一个标志告诉 Apache 终止一系列重写条件和重写规则，就像`}`终止 PHP 中的语句块一样。注意这个标志并没有终止 mod_rewrite 处理！
*   `nocase|NC`–No Case 标志告诉 Apache 忽略正则表达式中字符串的大小写，当编写与域名的`{HTTP_HOST}`变量匹配的`RewriteCond`语句时，这通常是必要的，因为域名不区分大小写。
*   `redirect|R`–重定向标志用于触发外部(可见)重定向。默认情况下，这意味着 Apache 将发出一个 HTTP 302 响应，表明文档已经被临时移动，但是如果愿意，您可以指定 HTTP 代码。例如，您可以使用`[R=301]`让 Apache 发出一个 HTTP 301 响应(永久移动)，如果您需要搜索引擎重新索引一个已更改的 URI，这通常很有用。
*   `qsappend|QSA`–查询字符串附加标志用于“通过”现有的查询字符串。您还可以定义一个新的查询字符串，旧的字符串将被追加到这个字符串中，只是要注意不要复制键名。不使用`QSA`标志将导致在重定向期间创建查询字符串，从而破坏现有的查询字符串。
*   `forbidden|F`–禁止标志用于告诉 Apache 何时不提供页面来响应请求。因此，Apache 将发出 HTTP 403 响应，该响应可用于保护文件不被未授权的访问者、带宽吸血鬼等查看。
*   `ornext|OR`–OR 标志允许您用逻辑 OR 关系组合重写条件，而不是默认的 AND。
*   `next|N`–下一个标志告诉 mod_rewrite 从第一个规则重新开始重写过程，但是使用从最后处理的重写规则返回的 URI。当您不知道有多少字符时，这对于替换`{REQUEST_URI}`中的字符很有用。

你可以在 Apache.org 的 mod_rewrite 文档页面阅读其他可用的标志。

## 修改 _ 重写注释

在任何 mod_rewrite 代码的开始都需要使用`RewriteEngine on`语句，但它在另一个角色中也很有用。作为一名优秀的程序员，你知道注释在你的代码中有多重要。mod_rewrite 允许您通过将代码包装在`RewriteEngine off`和`RewriteEngine on`语句中来注释掉整个 mod_rewrite 代码块:

```
RewriteEngine off  

RewriteCond %{HTTP_HOST} !^www.example.com$ [NC]  

RewriteRule .? http://www.example.com%{REQUEST_URI} [R=301,L]  

RewriteEngine on
```

mod_rewrite 引擎不会读取上面的任何语句。`RewriteEngine`语句在开发新的 mod_rewrite 代码时非常有用——就像使用 PHP 注释的`/* ... */`包装器一样使用它们。

## 修改 _ 重写提示

作为一名网站管理员，您需要决定如何让访问者识别您的页面，以及如何重写这些 URIs，以便 Apache 可以提供适当的内容。一定要把一些仔细考虑到你的新 URI 计划的创建。不要忘记，在实施了你的新 URI 计划后，你可能不得不重新浏览旧的内容，更新现有的链接以适应新的计划。

设计新的 URI 方案时，尽可能使用唯一键。在前面的例子中，我使用国家、州和城市作为关键字——这些项目在数据库中是唯一的。但是当我为客户建立网站来更新他们自己的时候，坚持让他们为所有的文章提供独特的标题是不合理的。数据库中的文章通常由自动递增的 ID 来标识，这对于我友好的 URI 计划来说是完美的。这使得重写规则更加简单，因为您可以将 URI 原子直接映射到查询字符串值。

人们经常试图使用数据库从标题或其他类似字段重定向到特定的 ID 值。mod_rewrite 有一个用于这个目的的`RewriteMap`语句，但是您需要访问您的 Apache 主配置文件:`httpd.conf`。通常，只有当您拥有并操作服务器时，您才能访问该文件。相反，完全避免这个问题，使用 id 字段来创建链接。

请记住，空格在 URIs 中显示为`%20`，所以您需要在 PHP 代码中替换它们。PHP 的`str_replace`函数非常适合这个任务。通常，在生成链接时，我们需要用`%20`替换空格，在从`$_GET`数组读入查询字符串值时，将`%20`转换回空格。然而，当处理包含空格的唯一数据库字段值时，我更喜欢使用下划线字符来替换结果链接中的空格。为此，您可以使用以下 PHP 代码:

```
$name = str_replace ( ' ', '_', $name );
```

在实施新的 URI 计划时，注意不要破坏现有的相对联系。当他们的 CSS、JavaScript 文件和图像突然停止工作时，开发人员通常会感到惊讶。记住，相对链接是相对于浏览器端的当前 URI 的——那是请求的 URI，而不是重写的那个。根据站点的配置，您可能需要对所有静态资源使用绝对 URIs，或者 HTML `<base>`标签。

## 13 mod_rewrite 示例

前面，我们看了一个例子，它强制每个请求都包含域名的 www 部分。让我们看看更多的例子，看看 mod_rewrite 有多有用。

***1。强制一个域的 www，同时保留子域***

```
RewriteCond %{HTTP_HOST} ^([a-z.]+)?example.com$ [NC]  

RewriteCond %{HTTP_HOST} !^www. [NC]  

RewriteRule .? http://www.%1example.com%{REQUEST_URI} [R=301,L]
```

该规则使用`%1`变量捕获可选子域，如果它不是以 www .开头。，用 www 重定向。前置到子域。域和原始的`{REQUEST_URI}`被附加到结果中。

***2。从一个域中清除 www***

```
RewriteCond %{HTTP_HOST} !^example.com$ [NC]  

RewriteRule .? http://example.com%{REQUEST_URI} [R=301,L]
```

***3。去掉 www 但保留一个子域***

```
RewriteCond %{HTTP_HOST} ^www.(([a-z0-9_]+.)?example.com)$ [NC]  

RewriteRule .? http://%1%{REQUEST_URI} [R=301,L]
```

这里，子域在`%2`(内部原子)中被捕获，但是，因为它是可选的并且已经在`%1`变量中被捕获，所以您需要的只是子域的`%1`。

***4。防止图片链接***

如果一些不道德的网站管理员通过链接到你的网站上的图片来榨取你的带宽，你可以使用下面的规则来阻止这些请求:

```
RewriteCond %{HTTP_REFERER} !^$  

RewriteCond %{HTTP_REFERER} !^http://(www.)?example.com/ [NC]  

RewriteRule .(gif|jpg|png)$ - [F]
```

如果`{HTTP_REFERER}`值不为空，或者来自您自己的域(example.com)，此规则将阻止使用禁用标志`F`查看以`.gif`、`.jpg`或`.png`结尾的 URIs。

如果你对这些热链接感到很不爽，你可以改变图片，让访问者知道你知道他们在热链接:

```
RewriteCond %{HTTP_REFERER} !^$  

RewriteCond %{HTTP_REFERER} !^http://(www.)?example.com/.*$ [NC]  

RewriteRule .(gif|jpg|png)$ http://www.example.com/hotlinked.gif [R=301,L]
```

上面的规则没有阻塞 URI，而是将其重写为我们域中的特定映像。这个图像里出现什么，完全取决于你的想象！

您可以使用以下方式阻止特定的域:

```
RewriteCond %{HTTP_REFERER} !^http://(www.)?leech_site.com/ [NC]  

RewriteRule .(gif|jpg|png)$ - [F,L]
```

该规则阻止所有将`{HTTP_REFERER}`字段设置为坏域的请求。

当然，上述规则依赖于正确设置的`{HTTP_REFERER}`值。通常是这样的，但是如果你更愿意依赖 IP 地址，可以使用`{REMOTE_ADDR}`来代替。

***5。如果目录和文件不存在，重定向到 404 页面***

如果您的主机不提供“文件未找到”重定向，自己创建它！

```
RewriteCond %{REQUEST_FILENAME} !-f  

RewriteCond %{REQUEST_FILENAME} !-d  

RewriteRule .? /404.php [L]
```

这里，`-f`匹配现有的文件名，`-d`匹配现有的目录名。在重定向到`404.php`脚本之前，这个脚本检查请求的文件名是否是一个现有的文件名或目录名。您可以扩展这个脚本:在查询字符串中包含 URI，方法是在 URI 后面添加`?url=$1`;

```
RewriteRule ^/?(.*)$ /404.php?url=$1 [L]
```

这样，您的`404.php`脚本可以对请求的 URL 做一些事情:在消息中显示它，在电子邮件提醒中发送它，执行搜索，等等。

***6。重命名你的目录***

如果您在站点上移动了文件，更改了目录名，请尝试以下方法:

```
RewriteRule ^/?old_directory/([a-z/.]+)$ new_directory/$1 [R=301,L]
```

我在集合中包含了文字点字符(不是“任何字符”元字符),以允许文件扩展名。

**7*。将旧的`.html`链接转换为新的`.php`链接***

更新您的网站，但需要确保书签链接仍然有效？

```
RewriteRule ^/?([a-z/]+).html$ $1.php [L]
```

这不是重定向，所以你的访问者看不到它。为了使它永久(并且可见)，将标志改为`[R=301,L]`。

***8。创建无延伸链接***

如果您的网站使用 PHP 文件，并且您想让您的链接更容易记住，或者您只想隐藏文件扩展名，请尝试以下方法:

```
RewriteRule ^/?([a-z]+)$ $1.php [L]
```

如果您混合了`.html`和`.php`文件，您可以使用`RewriteCond`语句来检查具有任一扩展名的文件名是否作为文件存在:

```
RewriteCond %{REQUEST_FILENAME}.php -f  

RewriteRule ^/?([a-zA-Z0-9]+)$ $1.php [L]  

RewriteCond %{REQUEST_FILENAME}.html -f  

RewriteRule ^/?([a-zA-Z0-9]+)$ $1.html [L]
```

如果存在扩展名为`.php`的文件名，将选择该规则。

***9。检查查询字符串中的关键字***

如果您需要在查询字符串中有一个特定的键值，您可以用一个`RewriteCond`语句来检查它是否存在:

```
RewriteCond %{QUERY_STRING} !uniquekey=  

RewriteRule ^/?script_that_requires_uniquekey.php$ other_script.php [QSA,L]
```

上面的代码将检查`{QUERY_STRING}`变量是否缺少键`uniquekey`，如果`{REQUEST_URI}`是`script_that_requires_uniquekey`，它将重定向到另一个 URI。

***10。删除查询字符串***

Apache 的 mod_rewrite 会自动传递查询字符串，除非您执行以下任一操作:

*   分配一个新的查询字符串(您可以通过添加一个 QSA 标志来保留原来的查询字符串，例如`[QSA,L]`)。
*   在文件名后添加一个`?`(例如`index.php?`)。浏览器的位置字段中不会显示`?`。

***11。将工作中的 URI 重定向到新格式***

这里有一个卷曲的。比方说，我们有一组工作 URL，看起来像这样:`/index.php?id=nnnn`。然而，我们真的想把它们改成`/nnnn`，并确保搜索引擎把它们的索引更新成新的 URI 格式。首先，我们必须将旧的 URIs 重定向到新的，以便搜索引擎更新它们的索引，但是我们仍然必须将新的 URI 重写回旧的，以便运行`index.php`脚本。我让你头晕了吗？

这里的技巧是在查询字符串中放置一个访问者看不到的标记代码。只有当“标记”不在查询字符串中时，我们才从旧的链接重定向到新的格式。然后，我们将新格式链接重写回旧格式，并向查询字符串添加一个标记，使用 QSA 标志来确保我们没有删除现有的查询字符串。这是如何做到的:

```
RewriteCond %{QUERY_STRING} !marker  

RewriteCond %{QUERY_STRING} id=([-a-zA-Z0-9_+]+)  

RewriteRule ^/?index.php$ %1? [R=301,L]  

RewriteRule ^/?([-a-zA-Z0-9_+]+)$ index.php?marker&id=$1 [L]
```

这里，最初的 URI`http://www.example.com/index.php?id=nnnn`不包含标记，所以它被第一个规则重定向到带有 HTTP 301 响应的`http://www.example.com/nnnn`。第二条规则将`http://www.example.com/nnnn`重写回`http://www.example.com/index.php?marker&id=nnnn`，在新的查询字符串中添加`marker`和`id=nnnn`；然后，mod_rewrite 过程重新开始。

在第二次迭代中，标记被匹配，所以第一个规则被忽略，并且，由于在`index.php?marker&id=nnnn`中有一个点字符，第二个规则也被忽略…我们就完成了！

注意，虽然这个解决方案很有用，但是它需要 Apache 进行额外的处理，所以如果您在流量很大的共享服务器上使用它，就要小心了。

***12。确保使用安全的服务器***

Apache 可以通过两种方式确定您是否使用了安全服务器:使用`{HTTPS}`或`{SERVER_PORT}`变量:

```
RewriteCond %{REQUEST_URI} ^secure_page.php$  

RewriteCond %{HTTPS} !on   

RewriteRule ^/?(secure_page.php)$ https://www.example.com/$1 [R=301,L]
```

上面的例子测试了`{REQUEST_URI}`值是否等于我们的安全页面脚本，以及`{HTTPS}`值是否不等于 on。如果这两个条件都满足，请求将被重定向到安全服务器 URI。或者，您可以通过测试`{server_port}`值来做同样的事情，这里 443 通常是安全的服务器端口:

```
RewriteCond %{REQUEST_URI} ^secure_page.php$  

RewriteCond %{SERVER_PORT} !^443$  

RewriteRule ^/?(secure_page.php)$ https://www.example.com/$1 [R=301,L]
```

***13。仅在选定页面上实施安全服务器***

在安全域和非安全域共享 web 服务器的`DocumentRoot`目录的情况下，您将需要一个`RewriteCond`语句来检查安全服务器端口是否正在被使用，然后仅当请求的脚本是需要安全服务器的脚本列表中的一个时才重定向请求:

```
RewriteCond %{SERVER_PORT} !^443$  

RewriteRule ^/?(page1|page2|page3|page4|page5)$  https://www.example.com/%1 [R=301,L]
```

下面是如何将不需要安全服务器的页面请求重定向回端口 80:

```
RewriteCond %{ SERVER_PORT } ^443$   

RewriteRule !^/?(page6|page7|page8|page9)$ http://www.example.com%{REQUEST_URI} [R=301,L]
```

## 摘要

Apache mod_rewrite 主要用于支持 SEO 和用户友好的 URIs，但对于其他重要的重定向任务，它也是一个非常灵活的工具。如果你想了解更多，我找到了一些非常有用的资源:

***正则表达式***

*   很棒的教程:[http://gnosis . CX/publish/programming/regular _ expressions . html](http://gnosis.cx/publish/programming/regular_expressions.html)
*   小抄:[http://regexlib.com/CheatSheet.aspx](http://regexlib.com/CheatSheet.aspx)
*   一个支持正则表达式的文本编辑器:[http://www.editpadpro.com](http://www.editpadpro.com)
*   http://weitz.de/regex-coach/的蔻驰

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [PHP & MySQL 初学者网络开发](https://learnable.com/courses/php-mysql-web-development-for-beginners-13)。

**Go to page:** [1](https://sitepoint.com/apache-mod-rewrite-examples) | [2](https://sitepoint.com/apache-mod-rewrite-examples-2/)

## 分享这篇文章