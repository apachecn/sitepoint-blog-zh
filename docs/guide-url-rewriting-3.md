# URL 重写初学者指南

> 原文：<https://www.sitepoint.com/guide-url-rewriting-3/>

## 情况

但这还不是全部！虽然`RewriteRule`给了你一个专业的 URL 重写的机会，但是你可以在使用条件上更加定制化。

条件的格式很简单:

```
RewriteCond Something_to_test Condition
```

任何`RewriteCond`条件都会影响后面的`RewriteRule`的行为，这有点令人困惑，因为在后面的`RewriteRule`模式匹配当前 URL 之前，`RewriteCond`不会被评估。

它是这样工作的:mod_rewrite 获取所有的`RewriteRules`，并开始将当前 URL 与每个`RewriteRule`模式进行匹配。如果有一个`RewriteRule`模式与 URL 匹配，mod_rewrite 检查是否存在这个`RewriteRule`的条件，以及第一个条件是否返回 true。如果是，将进行适当的替换，如果不是，mod_rewrite 将寻找剩余的条件。当没有更多的条件时，检查随后的`RewriteRule`。

这样，您就可以使用基于 Apache 中 HTTP 传输过程中几乎所有已知信息的条件来定制 URL 重写——甚至更多！基本上，您可以在`Something_to_test`字符串中使用所有这些变量:

*   HTTP header variables:
    `HTTP_USER_AGENT, HTTP_REFERER,`

    `HTTP_COOKIE, HTTP_FORWARDED, HTTP_HOST, HTTP_PROXY_CONNECTION,`

    `HTTP_ACCEPT`

*   连接&请求变量:`REMOTE_ADDR, REMOTE_HOST, REMOTE_USER,` `REMOTE_IDENT, REQUEST_METHOD, SCRIPT_FILENAME,` `PATH_INFO, QUERY_STRING, AUTH_TYPE`
*   服务器内部变量:`DOCUMENT_ROOT, SERVER_ADMIN,` `SERVER_NAME, SERVER_ADDR, SERVER_PORT, SERVER_PROTOCOL,` `SERVER_SOFTWARE`
*   系统变量:`TIME_YEAR, TIME_MON, TIME_DAY,` `TIME_HOUR, TIME_MIN, TIME_SEC, TIME_WDAY, TIME`
*   mod _ 重写特殊值:`API_VERSION, THE_REQUEST, REQUEST_URI,` `REQUEST_FILENAME, IS_SUBREQ`

该条件可以是一个简单的字符串或标准的正则表达式，并增加了如下内容:

*   `<, >, =` 简单的比较运算符
*   `-f`如果`Something_to_test`是文件
*   如果`Something_to_test`是一个目录

如您所见，这些足以指定像这样的条件(摘自 mod_rewrite 手册):

```
 RewriteCond %{HTTP_USER_AGENT} ^Mozilla.*    
  RewriteRule ^/$ /homepage.max.html [L]    

  RewriteCond %{HTTP_USER_AGENT} ^Lynx.*    
  RewriteRule ^/$ /homepage.min.html [L]    

  RewriteRule ^/$ /homepage.std.html [L] 
```

当浏览器请求索引页面时，会发生三种情况:

*   带有 Mozilla 引擎的浏览器将在 homepage.max.html 使用
*   使用 Lynx(基于字符的浏览器)将打开 homepage.min.html
*   如果浏览器的名称不包含“Mozilla”或“Lynx ”,将发送标准的 homepage.std.html 文件

您甚至可以禁止用户从服务器外部访问图像:

```
 RewriteCond %{HTTP_REFERER} !^$  
  RewriteCond %{HTTP_REFERER} !^http://localhost/.*$ [OR,NC]  
  RewriteCond %{HTTP_REFERER} !^http://mysite.com/.*$ [OR,NC]  
  RewriteCond %{HTTP_REFERER} !^http://www.mysite.com/.*$ [OR,NC]  
  RewriteRule .*.(gif|GIF|jpg|JPG)$ http://mysiimg/bad.gif [L,R] 
```

但是当然，有无限的可能性，包括 IP 或时间相关的条件等。

**Go to page:** [1](/guide-url-rewriting) | [2](/guide-url-rewriting-2/) | [3](/guide-url-rewriting-3/) | [4](/guide-url-rewriting-4/)

## 分享这篇文章