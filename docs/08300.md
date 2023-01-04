# 跨站点脚本攻击(XSS)

> 原文：<https://www.sitepoint.com/php-security-cross-site-scripting-attacks-xss/>

跨站点脚本攻击是互联网上每天发生的五大安全攻击之一，您的 PHP 脚本可能无法幸免。

也称为 XSS，这种攻击基本上是一种代码注入攻击，它可能通过错误地验证用户数据来实现，这些数据通常通过 web 表单或使用更改的超链接插入到页面中。注入的代码可以是任何恶意的客户端代码，比如 JavaScript、VBScript、HTML、CSS、Flash 等等。该代码用于在服务器上保存有害数据或在用户浏览器中执行恶意操作。

不幸的是，跨站点脚本攻击经常发生，因为开发人员未能提供安全的代码。每个 PHP 程序员都有责任了解攻击是如何针对他们的 PHP 脚本来利用可能的安全漏洞的。阅读本文，您将了解更多关于跨站点脚本攻击以及如何在您的代码中防止它们的信息。

## 通过实例学习

让我们看看下面的代码片段。

```
<form action="post.php" method="post">
 <input type="text" name="comment" value="">
 <input type="submit" name="submit" value="Submit">
</form>
```

这里我们有一个简单的表单，其中有一个用于数据输入的文本框和一个提交按钮。一旦表单被提交，它将把数据提交给`post.php`进行处理。假设所有的`post.php`都像这样输出数据:

```
<?php
echo $_POST["comment"];
```

没有任何过滤，黑客可以通过表单提交以下内容，这将在浏览器中生成一个弹出窗口，显示消息“hacked”。

```
<script>alert("hacked")</script>
```

这个例子尽管本质上是恶意的，但似乎并没有造成多大伤害。但是想想如果 JavaScript 代码被编写来窃取用户的 cookie 并从中提取敏感信息会发生什么？XSS 的攻击远比一个简单的电话更糟糕。

跨站点脚本攻击可以根据它们传递恶意负载的方式分为两大类:非持续性 XSS 和持续性 XSS。请允许我详细讨论每种类型。

### 非持久性 XSS

也称为反射 XSS 攻击，这意味着实际的恶意代码不是存储在服务器上，而是通过服务器呈现给受害者，这是两种交付方法中更受欢迎的 XSS 策略。攻击是从外部来源发起的，如电子邮件或第三方网站。

下面是一个简单搜索结果脚本的一部分示例:

```
<?php
// Get search results based on the query
echo "You searched for: " . $_GET["query"];

// List search results
...
```

这个例子可以是一个非常不安全的结果页面，其中搜索查询被显示给用户。这里的问题是`$_GET["query"]`变量没有被验证或转义，因此攻击者可以向受害者发送以下链接:

```
http://example.com/search.php?query=<script>alert("hacked")</script>
```

如果没有验证，页面将包含:

```
You searched for: <script>alert("hacked")</script>
```

### 执着的 XSS

当恶意代码已经通过验证过程并存储在数据存储中时，就会发生这种类型的攻击。这可能是注释、日志文件、通知消息或网站上任何其他需要用户一次性输入的部分。稍后，当这个特定信息出现在网站上时，恶意代码就会被执行。

让我们用下面的例子来说明一个基本的基于文件的注释系统。假设与我之前展示的表单相同，假设接收脚本只是将注释附加到数据文件中。

```
<?php
file_put_contents("comments.txt", $_POST["comment"], FILE_APPEND);
```

在其他地方,`comments.txt`的内容向游客展示:

```
<?php
echo file_get_contents("comments.txt");
```

当用户提交评论时，评论被保存到数据文件中。然后整个文件(因此整个系列的评论)被显示给读者。如果恶意代码被提交，那么它将被保存和显示，没有任何验证或转义。

## 防止跨站点脚本攻击

幸运的是，就像 XSS 攻击可以对未受保护的网站进行一样容易，防范它们也一样容易。但是，即使在您编写一行代码之前，您也必须始终考虑预防。

在任何网络环境(无论是开发、试运行还是生产)中需要“强制执行”的第一条规则是**永远不要相信来自用户或任何其他第三方来源的数据。**这一点怎么强调都不为过。每一位数据都必须在输入时进行验证，在输出时进行转义。这是预防 XSS 的黄金法则。

为了实现可靠的安全措施来防止 XSS 攻击，我们应该注意数据验证、数据净化和输出转义。

### 数据有效性

数据验证是确保应用程序使用正确数据运行的过程。如果您的 PHP 脚本希望用户输入一个整数，那么任何其他类型的数据都将被丢弃。每条用户数据在接收时都必须经过验证，以确保其类型正确，如果没有通过验证过程，则被丢弃。

例如，如果您想要验证一个电话号码，您应该丢弃任何包含字母的字符串，因为电话号码应该只包含数字。你还应该考虑字符串的长度。如果您想要更宽松，您可以允许有限的一组特殊字符，如加号、括号和破折号，这些字符通常用于设置特定于您的目标区域的电话号码的格式。

```
<?php
// validate a US phone number
if (preg_match('/^((1-)?d{3}-)d{3}-d{4}$/', $phone)) {
    echo $phone . " is valid format.";
}
```

### 数据净化

数据清理侧重于通过从数据中删除任何不需要的位并将其规范化为正确的形式来操纵数据以确保其安全。例如，如果您期望一个纯文本字符串作为用户输入，您可能希望从中删除任何 HTML 标记。

```
<?php
// sanitize HTML from the comment
$comment = strip_tags($_POST["comment"]);
```

有时，数据验证和净化/规范化可以齐头并进。

```
<?php
// normalize and validate a US phone number
$phone = preg_replace('/[^d]/', "", $phone);
$len = strlen($phone);
if ($len == 7 || $len == 10 || $len == 11) {
    echo $phone . " is valid format.";
}
```

### 输出转义

为了保护显示/输出数据的完整性，您应该在将数据呈现给用户时对其进行转义。这可以防止浏览器对可能找到的任何特殊字符序列应用任何非预期的含义。

```
<?php
// escape output sent to the browser
echo "You searched for: " . htmlspecialchars($_GET["query"]);
```

## 现在一起！

为了更好地理解数据处理的三个方面，让我们再看一看前面的基于文件的注释系统，并修改它以确保它是安全的。代码中的潜在漏洞源于这样一个事实，即`$_POST["comment"]`被盲目地附加到`comments.txt`文件中，然后直接显示给用户。为了保护它，在将`$_POST["comment"]`值添加到文件之前，应该对其进行验证和清理，并且在向用户显示时，应该对文件的内容进行转义。

```
<?php
// validate comment
$comment = trim($_POST["comment"]);
if (empty($comment)) {
    exit("must provide a comment");
}

// sanitize comment
$comment = strip_tags($comment);

// comment is now safe for storage
file_put_contents("comments.txt", $comment, FILE_APPEND);

// escape comments before display
$comments = file_get_contents("comments.txt");
echo htmlspecialchars($comments);
```

该脚本首先验证传入的注释，以确保用户提供了一个非零长度的字符串。毕竟，空白的评论不是很有趣。

数据验证需要在一个定义良好的上下文中进行，这意味着如果我期望从用户那里返回一个整数，那么我会通过将数据转换为整数并将其作为整数来处理来相应地验证它。如果这导致无效数据，那么简单地丢弃它并让用户知道。

然后，脚本通过删除注释中可能包含的任何 HTML 标签来净化注释。

最后，检索、过滤和显示评论。

一般来说，`htmlspecialchars()`函数足以过滤浏览器中的输出。但是，如果你在网页中使用的是 ISO-8859-1 或 UTF-8 之外的字符编码，那么你会想要使用`htmlentities()`。有关这两个函数的更多信息，请阅读官方 PHP 文档中关于它们各自的文章。

请记住，在像 Web 这样不断发展的介质上，没有一个解决方案是 100%安全的。用最新的 XSS 测试向量彻底测试你的验证码。使用来自以下来源的测试数据应该可以揭示您的代码是否仍然容易受到 XSS 攻击。

*   RSnake XSS 备忘单(一个非常全面的 XSS 向量列表，你可以用它来测试你的代码)
*   Zend 框架的 [XSS 测试数据](http://www.zfsnippets.com/snippets/view/id/5)
*   [XSS 备忘单](http://html5sec.org/)(利用 HTML5 特性)

## 摘要

希望这篇文章很好地解释了什么是跨站点脚本攻击，以及如何防止它们发生在您的代码中。永远不要相信来自用户或任何其他第三方来源的数据。您可以通过在定义良好的上下文中验证传入的值、净化数据以保护您的代码，以及对输出进行转义以保护您的用户来保护自己。编写完代码后，尽可能彻底地测试代码，确保您的工作正常进行。

<small>图片 via [英格谢佩斯](http://www.shutterstock.com/gallery-195178p1.html) / [梭托](http://www.shutterstock.com)</small>

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start PHP](https://learnable.com/books/jump-start-php?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。有关于 PHP 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章