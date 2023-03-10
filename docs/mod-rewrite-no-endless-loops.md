# mod_rewrite:不再有无尽的循环！

> 原文：<https://www.sitepoint.com/mod-rewrite-no-endless-loops/>

**查看变更日志…**

Apache 2.0.45 和 1.3.28 的更改日志包含以下说明:

*Apache 2.0.45:
通过
在超过内部重定向的限制后中止来防止 mod_rewrite 中内部重定向的无限循环。
限制默认为 10，可使用重写选项
指令更改。PR 17462。[安德朗马洛]*

Apache 1 . 3 . 28:
2 . x 系列的反向移植:在 mod_rewrite 中，通过在超过内部重定向的限制后中止来防止内部重定向
的无限循环。
该限制默认为 10，可使用重写选项
指令进行更改。PR 17462。[安德朗马洛]

这两个流行的 Web 服务器版本为 RewriteOptions 指令引入了一个新选项:MaxRedirects。

mod_rewrite，对于那些不知道的人来说，是一个强大的模块，允许你处理传入的 URI 请求等等。

本文将解释 MaxRedirects 选项的使用和优点。提供 mod_rewrite 模块的虚拟主机公司应该注意到这些升级到最新 Apache 版本的原因。让你的网络服务器保持最新是必须的。不提供 mod_rewrite 模块的托管公司被鼓励启用它。

在我们继续之前，您需要一个安装了 mod_rewrite 的 Apache 安装。请查看我的 [FAQ](http://www.sitepointforums.com/showthread.php?threadid=117343) 获取安装帮助。

##### 典型案例

先说个例子。

我们想重定向所有 URIs 到一个 PHP 脚本。重定向将是内部的，因此客户端(例如浏览器)不会看到发生了重定向。为此，我们在文档根中创建一个。包含这些规则的 htaccess 文件:

```
# Enable rewrite engine 

Options +FollowSymLinks 

RewriteEngine On 

# Redirect internally all URIs to /index.php 

RewriteRule .* /index.php [L]
```

但是，这不会起作用，它会冻结您的 Apache Web 服务器。为什么？

问题是这一行:

```
RewriteRule .* /index.php [L]
```

简单地说，这条规则说“将所有请求重定向到/index.php”。这听起来像是我们最初打算实现的目标，但实际上，我们不会得到我们期望的结果。

以下是对 www.example.net/path/to/:的请求所发生的情况

***A 相***

*   URI 是/路径/到/
*   RewriteRule 将重写路径/to/ to /index.php 中的 URI
*   将对/index.php 进行内部重定向
*   内部重定向将被视为一个新的请求，因此将被再次解析(参见阶段 b)

***B 相***

*   重写引擎将再次处理之前的内部重定向/index.php
*   RewriteRule 将把 index.php 重写到/index.php
*   将对/index.php 进行内部重定向

此时，重写引擎将继续运行阶段 B，Apache Web 服务器将冻结。

如果听起来很复杂，不用担心！下面是一个显示该过程的简化日志文件:

`# phase a)  
 [] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/path/to/ -> path/to/  
 [] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'path/to/'  
 [] (2) [per-dir /www/htdocs/] rewrite path/to/ -> /index.php  
 [] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]  

 # phase b)  
 [redir#1] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php  
 [redir#1] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'  
 [redir#1] (2) [per-dir /www/htdocs/] rewrite index.php -> /index.php  
 [redir#1] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]  

 # phase b)  
 [redir#2] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php  
 [redir#2] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'  
 [redir#2] (2) [per-dir /www/htdocs/] rewrite index.php -> /index.php  
 [redir#2] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]  

 # phase b)  
 [redir#3] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php  
 [redir#3] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'  
 [redir#3] (2) [per-dir /www/htdocs/] rewrite index.php -> /index.php  
 [redir#3] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]  

 ....ad infinitum...  

 # phase b)  
 [redir#N] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php  
 [redir#N] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'  
 [redir#N] (2) [per-dir /www/htdocs/] rewrite index.php -> /index.php  
 [redir#N] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]`

这是 Apache 2 . 0 . 45 和 1.3.28 之前版本的情况。

##### Maxredirects

以下简化的日志文件显示了如果我们的示例在 Apache 2.0.45 或 1.3.28 下运行会发生什么:

```
# phase a)  

[] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/path/to/ -> path/to/  

[] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'path/to/'  

[] (2) [per-dir /www/htdocs/] rewrite path/to/ -> /index.php  

[] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]  

# phase b)  

[redir#1] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php  

[redir#1] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'  

[redir#1] (2) [per-dir /www/htdocs/] rewrite index.php -> /index.php  

[redir#1] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]  

# phase b)  

[redir#2] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php  

[redir#2] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'  

[redir#2] (2) [per-dir /www/htdocs/] rewrite index.php -> /index.php  

[redir#2] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]  

...last...  

# phase b)  

[redir#10] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php  

[redir#10] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'  

[redir#10] (2) [per-dir /www/htdocs/] rewrite index.php -> /index.php  

[redir#10] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]
```

虽然重写日志看起来和以前一样，但事实并非如此。内部重定向不是连续的，因为它们在 10 次尝试后停止。Web 服务器不会冻结，我们将收到 500 HTTP 状态错误(内部服务器错误)。

我们的 error_log 将如下所示:

```
[Mon Jul 21 15:24:27 2003] [error] [client 127.0.0.1] mod_rewrite: maximum number of internal redirects reached. Assuming configuration error. Use 'RewriteOptions MaxRedirects' to increase the limit if neccessary.
```

这个详细的错误日志将帮助我们快速准确地调试我们的规则。

即使我们使用和以前一样的规则，我们的 Apache Web 服务器也不会再冻结了。是不是很神奇？

##### 重写选项

所以我们的 Web 服务器不会再冻结了…但是为什么内部重定向在 10 次重定向后停止了呢？这就是 RewriteOptions 指令和 maxredirects 选项发挥作用的地方。

默认情况下(即，如果您没有声明它)，maxredirects 设置为:

```
RewriteOptions maxredirect=10
```

这个指令将强制重写引擎在 10 次内部重定向后停止，因为在那之后，我们可能会陷入一个死循环。

如果 10 个重定向的数字太高，您可以将其更改为一个较低的值。例如:

```
RewriteOptions maxredirect=3
```

我们的日志文件将如下所示:

```
# phase a)   

[] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/path/to/ -> path/to/   

[] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'path/to/'   

[] (2) [per-dir /www/htdocs/] rewrite path/to/ -> /index.php   

[] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]   

# phase b)   

[redir#1] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php   

[redir#1] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'   

[redir#1] (2) [per-dir /www/htdocs/] rewrite index.php -> /index.php   

[redir#1] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]   

# phase b)   

[redir#3] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php   

[redir#3] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'   

[redir#3] (2) [per-dir /www/htdocs/] rewrite index.php -> /index.php   

[redir#3] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]   

...stop
```

请记住，maxredirect 选项有助于控制我们创建的规则中的故意错误。它不会修复错误，但它会停止冻结我们的网络服务器。

这对主机公司和 mod_rewrite 用户都有好处。

##### 结论

最后但同样重要的是，我们可以做什么来纠正我们在例子中使用的规则？我们需要使用这条规则来代替:

```
RewriteRule .* index.php [L]
```

不同的是，在修改后的规则中，index.php 没有以斜线开头。在后一种情况下，mod_rewrite 足够聪明，可以防止无限循环，因为它看到 index.php 的原始请求与到 index.php 的内部重定向相同，所以它不会执行重定向。这可以在以下日志文件中看到:

```
# phase a)   

[] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/path/to/ -> path/to/   

[] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'path/to/'   

[] (2) [per-dir /www/htdocs/] rewrite path/to/ -> index.php   

[] (3) [per-dir /www/htdocs/] add per-dir prefix: index.php -> /www/htdocs/index.php   

[] (2) [per-dir /www/htdocs/] strip document_root prefix: /www/htdocs/index.php -> /index.php   

[] (1) [per-dir /www/htdocs/] internal redirect with /index.php [INTERNAL REDIRECT]   

# phase b)   

[redir#1] (3) [per-dir /www/htdocs/] strip per-dir prefix: /www/htdocs/index.php -> index.php   

[redir#1] (3) [per-dir /www/htdocs/] applying pattern '(.*)' to uri 'index.php'   

[redir#1] (2) [per-dir /www/htdocs/] rewrite index.php -> index.php   

[redir#1] (3) [per-dir /www/htdocs/] add per-dir prefix: index.php -> /www/htdocs/index.php   

# Smart check that will avoid the endless loops   

[redir#1] (1) [per-dir /www/htdocs/] initial URL equal rewritten URL: /www/htdocs/index.php [IGNORING REWRITE]
```

很有趣，不是吗？

如果您对 Apache 和/或 mod_rewrite 有任何疑问，请访问 [SitePoint 的 Apache 论坛](http://www.sitepointforums.com/forumdisplay.php?forumid=97)。

##### 文献学

*   [更改 Apache 2.0 的日志](http://www.apache.org/dist/httpd/CHANGES_2.0)
*   [Apache 1.3 的更改日志](http://www.apache.org/dist/httpd/CHANGES_1.3)
*   [mod_rewrite 重写选项指令](http://httpd.apache.org/docs/mod/mod_rewrite.html#RewriteOptions)
*   [Apache mod_rewrite 常见问题解答](http://www.sitepointforums.com/showthread.php?threadid=117343)
*   [SitePoint 的 Apache 论坛](http://www.sitepointforums.com/forumdisplay.php?forumid=97)

## 分享这篇文章