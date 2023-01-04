# 学习 Apache mod_rewrite: 13 个真实世界的例子

> 原文：<https://www.sitepoint.com/apache-mod-rewrite-examples/>

这篇文章写于 2007 年，至今仍是我们最受欢迎的帖子之一。如果你渴望了解更多关于 Apache 的知识，你可能会对这篇关于 Apache CloudStack 的文章很感兴趣。

Apache 的低成本、强大的功能使它成为世界各地组织的首选服务器。它最宝贵的财富之一是 [mod_rewrite 模块](http://httpd.apache.org/docs/2.0/mod/mod_rewrite.html)，其目的是按照一组规则指定的方式重写访问者的请求 URI。

本文将引导您了解重写规则、正则表达式和重写条件，并提供大量示例。

首先，我假设你理解你的网站需要 URI 重写特性的一般原因。如果你想了解这个领域的信息，在 SitePoint 的文章中有一本很好的入门书，[mod _ rewrite:URL 重写入门指南](https://www.sitepoint.com/article/guide-url-rewriting/)。在那里，您还可以找到如何在您自己的服务器上启用它的说明。

## 测试您的服务器设置

有些主机没有启用 mod_rewrite(默认情况下不启用)。您可以通过用一行简单的 PHP 代码创建一个 PHP 脚本来确定您的服务器是否启用了 mod_rewrite:

```
phpinfo();
```

如果用浏览器加载脚本，请查看 Apache 模块部分。如果 mod_rewrite 不在列表中，你必须要求你的主机启用它——或者找一个“好的主机”。大多数主机都会启用它，所以你可以开始了。

## mod_rewrite 的魔力

这里有一个简单的例子:创建三个名为`test.html`、`test.php`和`.htaccess`的文本文件。

在`test.html`文件中，输入以下内容:

```
<h1>This is the HTML file.</h1>
```

在`test.php`文件中，添加以下内容:

```
<h1>This is the PHP file.</h1>
```

使用以下内容创建第三个文件`.htaccess`:

```
RewriteEngine on 

RewriteRule ^/?test.html$ test.php [L]
```

将所有三个文件(以 ASCII 模式)上传到服务器上的一个目录中，并键入:

```
http://www.example.com/path/to/test.html
```

到 location 框中——当然是使用您自己的域和目录路径！如果页面显示“这是 PHP 文件”，说明工作正常！如果它显示“这是 HTML 文件”，那么一定是哪里出错了。

如果你的测试成功了，你会注意到`test.html` URI 仍然在浏览器的位置框中，但是我们已经看到了`test.php`文件的内容。你刚刚见证了 mod_rewrite 的魔力！

## mod-重写正则表达式

现在我们可以开始重写你的 URIs 了！假设我们有一个显示城市信息的网站。通过 URI 选择的城市如下:

```
http://www.example.com/display.php?country=USA&state=California&city=San_Diego
```

我们的问题是这太长了，对用户不友好。我们更希望访问者可以使用:

```
http://www.example.com/USA/California/San_Diego
```

我们需要能够告诉 Apache 将后者的 URI 重写为前者。为了让`display.php`脚本读取和解析查询字符串，我们需要使用正则表达式告诉 mod_rewrite 如何匹配两个 URIs。如果你不熟悉正则表达式(regex)，许多网站都提供了优秀的教程。在这篇文章的最后，我列出了我找到的关于这个主题的最好的网页。如果你不明白我的解释，我建议你看一下前两个链接。

一种非常常见的方法是使用表达式`(.*)`。该表达式组合了两个元字符:点字符(表示任何字符)和星号字符(表示零个或多个前面的字符)。因此，`(.*)`匹配`{REQUEST_URI}`字符串中的所有内容。`{REQUEST_URI}`是 URI 的一部分，跟在查询字符串的域后面，但不包括`?`字符，并且是重写规则试图匹配的唯一 Apache 变量。

将表达式放在括号中存储在“atom”中，atom 是一个变量，允许在规则中重用匹配的字符。因此，上面的表达式将在原子中存储美国/加利福尼亚/圣地亚哥。为了解决我们的问题，我们需要三个这样的原子，由子目录斜杠(`/`)分隔，因此正则表达式将变成:

```
(.*)/(.*)/(.*)
```

给定上面的表达式，regex 引擎将匹配(并保存)由两个斜杠分隔的三个值在`{REQUEST_URI}`字符串中的任何位置。然而，为了解决我们的具体问题，我们需要对此进行一定的限制——毕竟，上面的第一个和最后一个原子可以匹配任何东西！

首先，我们可以添加开始和结束锚字符。`^`字符匹配字符串开头的匹配字符，而`$`字符匹配字符串结尾的字符。

```
^(.*)/(.*)/(.*)$
```

该表达式指定整个字符串必须由我们的正则表达式匹配；在它之前或之后不能有任何别的东西。

然而，这种方法仍然允许太多的匹配。我们将我们的匹配存储为原子，并将它们传递给查询字符串，因此我们必须能够信任我们匹配的内容。用`(.*)`匹配任何东西都有很大的潜在安全风险，如果使用不当，甚至会导致 mod_rewrite 陷入循环！

为了避免不必要的问题，让我们改变原子来精确地指定我们将允许的字符。因为原子表示位置名称，所以我们应该将匹配的字符限制为从 A 到 Z 的大写和小写字母，并且因为我们使用它来表示名称中的空格，所以也应该允许使用下划线字符(`_`)。我们用方括号指定一个集合，用`-`字符指定一个范围。所以允许的字符集被写成`[a-zA-Z_]`。因为我们希望避免匹配空白名称，所以我们添加了`+`元字符，它指定只匹配一个或多个前面的字符。因此，我们的正则表达式现在是:

```
^([a-zA-Z_]+)/([a-zA-Z_]+)/([a-zA-Z_]+)$
```

`{REQUEST_URI}`字符串以一个`/`字符开始。Apache 在更改版本时更改了 regex 引擎，所以 Apache 版本 1 要求使用前导斜杠，而 Apache 2 禁止这样做！我们可以通过使用表达式`^/?` ( `?`是表示零或前面一个字符的元字符)使前导斜杠可选来满足这两个版本。所以现在我们有:

```
^/?([a-zA-Z_]+)/([a-zA-Z_]+)/([a-zA-Z_]+)$
```

有了正则表达式，我们现在可以将原子映射到查询字符串:

```
display.php?country=$1&state=$2&city=$3
```

```
$1 is the first (country) atom, $2 is the second (state) atom and $3 is the third (city) atom. Note that there can only be nine atoms created, in the order in which the opening brackets appear -- $1 ... $9 in a regular expression.

我们快到了！用以下文本创建一个新的`.htaccess`文件:

```

```
RewriteRule ^/?([a-zA-Z_]+)/([a-zA-Z_]+)/([a-zA-Z_]+)$ display.php?country=$1&state=$2&city=$3 [L]
```

将此文件保存到`display.php`所在的目录。重写规则必须在一行中，在`RewriteRule`语句、正则表达式和重定向之间有一个空格(在任何可选标志之前)。我们已经使用了`[L]`，或者‘最后’标志，它是终止标志(稍后将详细介绍标志)。

我们的重写规则现在完成了！原子值从请求字符串中提取出来，并添加到我们重写的 URI 的查询字符串中。`display.php`脚本可能会从查询字符串中提取这些值，并在数据库查询或类似的事情中使用它们。

但是，如果您只有一个简短的允许国家列表，那么最好通过在 regex 中指定可接受的值来避免潜在的数据库问题。这里有一个例子:

```
^/?(USA|Canada|Mexico)/([a-zA-Z_]+)/([a-zA-Z_]+)$
```

如果因为数据库中的值完全是小写而担心大小写，可以通过在重写的 URI 后面添加不区分大小写标志`[NC]`，让 regex 引擎忽略大小写。只是不要忘记在获得`$_GET`数组后，在脚本中将值转换成小写。

如果您想使用数字(0，1，… 9)来表示国会选区，那么您需要将 atom 的规范从(`[a-zA-Z_]+`)更改为(`[0-9]`)来匹配单个数字，(`[0-9]{1,2}`)来匹配一个或两个数字(0 到 99)，或者(`[0-9]+`)来表示一个或多个数字，这对数据库 id 很有用。

## `RewriteCond`声明

既然你已经学会了如何使用 mod_rewrite 的基本`RewriteRule`语句和`{REQUEST_URI}`字符串，那么是时候看看我们如何使用条件语句和`RewriteCond`语句访问其他变量了。`RewriteCond`语句用于指定应用`RewriteRule`语句的条件。

```
RewriteCond is similar in format to RewriteRule in that you have the command name, RewriteCond, a variable to be matched, the regex, and flags. The logical OR flag, [OR], is a useful flag to remember because all RewriteCond and RewriteRule statements are inclusive, in the sense of a logical AND relationship, until terminated by the Last, [L], flag.

您可以用一个`RewriteCond`语句测试许多服务器变量。你可以在我之前提到的 SitePoint 文章中找到一个列表，但是[这是我找到的最好的服务器变量](http://www.php.net/reserved.variables#reserved.variables.server)列表。

作为一个例子，让我们假设我们想要在你的域名中强制使用 www。为此，您需要测试 Apache `{HTTP_HOST}`变量，看看 www。已经存在，如果不存在，则重定向到所需的主机名:

```

```
RewriteCond %{HTTP_HOST} !^www.example.com$ [NC] 

RewriteRule .? http://www.example.com%{REQUEST_URI} [R=301,L]
```

这里，为了表示`{HTTP_HOST}`是一个 Apache 变量，我们必须在它前面加上一个`%`字符。正则表达式以`!`字符开始，如果与模式不匹配，这将导致条件为真。我们还必须对点字符进行转义，以便它匹配一个文字点，而不是任何字符，就像点元字符一样。我们还添加了 No Case 标志，使该操作不区分大小写。

`RewriteRule`将匹配零个或一个任意字符，并将重定向到`http://www.example.com`加上原始的`{REQUEST_URI}`值。`R=301`或 redirect 标志将导致 Apache 发出一个 HTTP 301 响应，表明这是一个永久重定向；最后一个标志告诉 mod_rewrite 您已经完成了这个 block 语句。

`RewriteCond`语句也可以创建原子，但是这些用`%1 ... %9`表示，就像`RewriteRule`原子用`$1 ... $9`表示一样。在后面的例子中，您将看到这些原子变量的运行。

**Go to page:** [1](https://sitepoint.com/apache-mod-rewrite-examples) | [2](https://sitepoint.com/apache-mod-rewrite-examples-2/)

## 分享这篇文章