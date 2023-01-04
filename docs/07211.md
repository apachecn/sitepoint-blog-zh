# 在 jQuery 中使用 Cookies

> 原文：<https://www.sitepoint.com/eat-those-cookies-with-jquery/>

Cookies 是在客户端存储数据最常用的技术。我之前的文章[如何用 JavaScript](https://www.sitepoint.com/how-to-deal-with-cookies-in-javascript/ "How to Deal with Cookies in JavaScript") 处理 cookie 解释了如何使用原始 JavaScript 对 cookie 执行 <abbr title="Create Read Update Delete">CRUD</abbr> 操作。本文转向 jQuery，将指导您使用 [jquery.cookie](https://github.com/carhartl/jquery-cookie "jQuery Cookie plugin") ，这是一个使 cookie 处理变得简单的插件。

本文假设读者熟悉之前[引用的帖子](https://www.sitepoint.com/how-to-deal-with-cookies-in-javascript/ "How to Deal with Cookies in JavaScript")的内容，或者至少对 cookies 有基本的了解。说到这里，让我们开始吧。

## 正在安装 jquery.cookie

你需要做的第一件事是从 GitHub 的库[下载 jquery.cookie。一旦你有了文件`jquery.cookie.js`，你只需要把它添加到你的页面中。请注意，作为一个 jQuery 插件，您必须将它包含在 jQuery 库的之后的**中。您的页面应该有一段类似如下的代码:**](https://github.com/carhartl/jquery-cookie "Scarica jquery.cookie")

```
<head>
  <script src="path/to/jquery.js"></script>
  <script src="path/to/jquery.cookie.js"></script>
</head>
```

## 这些方法

为了创建和检索 cookie，jquery.cookie 使用相同的方法`cookie()`，但是使用不同数量的参数。要创建一个 cookie，您需要传入两个必需的参数，分别是 cookie 的名称和值。您可以传递第三个可选参数，该参数是包含一些附加选项的对象文字。这些选项是`path`、`domain`、`expires`和`secure`。值得注意的是，这些选项可以在调用`cookie()`方法时进行本地设置，也可以通过`$.cookie.defaults`对象进行全局设置。使用前者设置的选项优先于使用后者设置的选项。为了了解 cookies 是如何创建的，让我们看几个例子。

以下示例跟踪用户访问网站的次数:

```
$.cookie("visits", 10);
```

此示例存储用户最喜欢的城市，并指定可以读写 cookie 的域和路径:

```
$.cookie("favourite-city", "London", {path: "/", domain: "jspro.com"});
```

此示例存储用户名。这个特殊的 cookie 在 2013 年 10 月 29 日上午 11 点到期，只能通过安全连接发送。

```
$.cookie("name", "Aurelio", {expires: new Date(2013, 10, 29, 11, 00, 00), secure: true});
```

### 正在检索 Cookies

检索 cookie 非常容易。您只需传递一个参数(cookie 的名称)来检索它，如以下示例所示:

要检索用户访问网站的次数:

```
console.debug($.cookie("visits")); // print "10"
```

要检索用户最喜欢的城市:

```
console.debug($.cookie("favourite-city")); // print "London"
```

要检索用户名，请执行以下操作:

```
console.debug($.cookie("name")); // print "Aurelio"
```

### 删除 Cookie

现在您知道了如何创建和检索 cookie。您需要知道的最后一件事是如何使用`removeCookie()`方法删除 cookie。如果找到了请求的 cookie，它返回`true`，否则返回`false`。要知道当你想移除一个 cookie 的时候，需要传入相同的选项，比如`path`和`domain`，否则操作会失败。现在，让我们看几个`removeCookie()`方法的例子。

要删除存储站点访问的 cookie:

```
$.removeCookie("visits"); // successfully deleted
```

要删除存储用户最喜欢的城市的 cookie:

```
$.removeCookie("favourite-city", {path: "/", domain: "jspro.com"}); // successfully deleted
```

接下来，我们尝试删除存储用户名的 cookie。这个例子失败了，因为没有指定`secure`值。

```
$.cookie("name"); // fails because the secure value is missing
```

## 结论

本文向您展示了如何使用 jquery 插件 jquery.cookie 管理 cookie。它通过将 cookie 实现细节抽象成几个简单、灵活的方法来解决许多问题。如果您需要进一步的澄清或额外的示例，请参考[官方文档](https://github.com/carhartl/jquery-cookie "jquery.cookie official documentation")。

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和互动在线课程，如 [jQuery:新手到忍者:新的踢腿和技巧](https://learnable.com/books/jquery-novice-to-ninja-new-kicks-and-tricks?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)。
*对这篇文章的评论是封闭的。有关于 jQuery 的问题吗？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?15-JavaScript-amp-jQuery?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？*

## 分享这篇文章