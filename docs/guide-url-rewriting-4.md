# URL 重写初学者指南

> 原文：<https://www.sitepoint.com/guide-url-rewriting-4/>

## 对于高级用户

我在介绍里提到了用户友好，还没处理。首先，假设我们有一个巨大的下载站点，它将可下载的软件分成不同的类别，每个类别都有一个唯一的 id(在 SQL 选择中使用)。我们使用像 open.php 这样的链接？categoryid=23487678 显示类别的内容。

为了确保我们的网址容易记忆(如 http://www.downloadsite.com/Nettools/Messengers ),我们可以使用:

```
 RewriteRule ^/NetTools$ /test.php?target=3    

  RewriteRule ^/NetTools/Messengers$ /test.php?target=34
```

假设 NetTools 类别的 ID 是 3，Messengers 子类别的 ID 是 34。

但是我们的站点很大，正如我提到的——谁想从数据库中找出所有的 id，然后手工编辑配置文件？没有人。相反，我们可以使用 mod_rewrite 的映射特性。Map 允许我们提供一个替换表——存储在一个文本文件中——在一个散列文件中(用于快速查找),或者甚至通过一个外部程序提供！

为了获得更好的性能，我将使用 PHP 生成一个文本文件，它包含以下内容:

```
 NetTools            3    

  NetTools/Messengers 34    

  .    

  .    

  .    

  and so on.
```

`httpd.conf`文件将包含:

```
 RewriteMap categories txt:/path/to/file/categoryids.txt    

  RewriteRule ^(.*)$ open.php?categoryid=${categories:$1|0}
```

这几行告诉 mod_rewrite 在 Apache 启动时读取 categoryids.txt 文件，并提供 open.php URL 的 ID。`|0`意味着如果文本文件中没有匹配的键，`categoryid`将为 0。

您还可以选择通过脚本或其他可执行代码动态地提供 IDs。该程序由 Apache 在服务器启动时启动，并一直运行到关闭。程序必须禁用缓冲 I/O，从标准输入中读取，并将结果写入标准输出——就这么简单！

使用 RewriteMap，您可以做更多事情，包括:

*   通过服务器的负载平衡(使用`rnd:`)，
*   创建具有同质 URL 布局的 Webcluster，
*   重定向到镜像站点而不修改您的 Web 应用程序，
*   根据主机列表拒绝用户访问，

诸如此类。

## 提示、技巧和建议

1.  在生产服务器中使用 mod_rewrite 之前，我建议设置一个测试服务器(或者游乐场，随便你怎么称呼)。

*   在开发过程中，您必须避免在应用程序中使用“老式”的 URL。*   可能仍然需要验证通过 URL 传递的数据(例如，传递不存在的——太大或太小——id 可能有风险)。*   编写“智能”重写规则节省了我的编码时间，并帮助我编写更简单的代码。我用的是错误报告(`E_ALL`)；无处不在(而且我推荐！)，但是第一万次做下面这些我觉得很无聊:

```
if (isset($_GET['id']) && (validNumber($_GET['id']))    

if (isset($_GET['todo']) && ($_GET['todo']=='deleteitem'))
```

下面的技巧通过每次在`RewriteRules`中提供所有需要的参数，帮助我去掉了多余的`isset()`表达式:

```
RewriteRule ^/products/[0-9]+$ products.php?id=$1&todo=
```

我知道，我知道这不是生命意义的答案——但很难在这么短的例子中展示这可能提供的解决方案有多好和清晰。

## 最后…

这就是我们对 mod_rewrite 的“简要”概述。在你掌握了基础知识之后，你会发现你可以很容易地创建自己的规则。如果你喜欢 URL 重写的想法，可能想试试 mod _ rewrite——下面是一些想法(注意底层 PHP 代码在这种情况下并不重要):

```
http://www.mysite.com/1/2/3/content.html    

    => 1_2_3_content.html    

    http://www.mysite.com/1/2/3/content.html    

    => content.php ? category=1     

    http://www.mysite.com/1/2/3/    

    => content.php ? category=1 & subcat1 = 2 & subcat2 = 3    

    http://www.mysite.com/1/2/3/details    

    => content.php ? category=1 & subcat1 = 2 & subcat2 = 3    

    http://www.mysite.com/bookshop/browse/bytitle    

    => library.php ? target=listbooks & order = title    

    http://www.mysite.com/bookshop/browse/byauthor    

    => library.php ? target=listbooks & order = author    

http://www.mysite.com/bookshop/product/123    

    => library.php ? target=showproduct & itemid=123    

http://www.mysite.com/bookshop/helpdesk/2    

    => library.php ? target=showhelp & page=2    

   http://www.mysite.com/bookshop/registration    

    => library.php ? target=reg 
```

链接

*   [正则表达式常见问题解答、帮助和教程](http://directory.google.com/Top/Computers/Programming/Languages/Regular_Expressions/FAQs,_Help,_and_Tutorials/%20)
*   搜索引擎友好的 URL(幸运的是，这里列出的 URL 已经是搜索引擎友好的了:)

1.  搜索引擎友好的 PHP 页面，作者 John Coggeshall
2.  克里斯·比斯利

*   [“IIS rewrite 是 Apache 针对 IIS 的 mod_rewrite 模块的精简实现”](http://www.qwerksoft.com/products/iisrewrite/IISRewriteDocumentation.htm)

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [PHP & MySQL 初学者网络开发](https://learnable.com/courses/php-mysql-web-development-for-beginners-13)。

**Go to page:** [1](/guide-url-rewriting) | [2](/guide-url-rewriting-2/) | [3](/guide-url-rewriting-3/) | [4](/guide-url-rewriting-4/)

## 分享这篇文章