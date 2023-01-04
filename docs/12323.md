# 在$_GET 和$_POST 上

> 原文：<https://www.sitepoint.com/on-get-and-post/>

PHP 手册对臭名昭著的超级全局变量`$_GET`和`$_POST`有如下描述:

> $_GET
> 通过 HTTP GET 方法传递给当前脚本的变量的关联数组。
> 来源:【php.net】T2

> $_POST
> 通过 HTTP POST 方法传递给当前脚本的变量的关联数组。
> 来源:【php.net】T2

让我们看看一个典型的 PHP 应用程序是如何工作的，好吗？

当一个 PHP 脚本被 web 服务器调用时，它是一个 [HTTP 请求](https://www.w3.org/Protocols/rfc2616/rfc2616-sec5.html#sec5)的结果。一个 HTTP 请求有一个目标 URI，URI 由不同的部分组成。其中一个部分是`query`。这是任何问号后面的部分。或者更正式地说:

```
  <scheme>://<authority><path>?<query>
  absoluteURI   = scheme ":" ( hier_part | opaque_part )
  hier_part     = ( net_path | abs_path ) [ "?" query ]
  net_path      = "//" authority [ abs_path ]
  abs_path      = "/"  path_segments

```

来源: [rfc 2396](http://www.ietf.org/rfc/rfc2396.txt)

当 PHP 进程启动时，`query`被解析成一个关联数组。出于某种原因，有人选择了不幸的`$_GET`，因为它是用于 GET 请求的，对吗？

不对！

*所有的* HTTP 请求——GET、POST 和[不太为人所知的请求](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#sec9.6)——都有一个 URI。因为它们都有一个 URI，所以它们也有一个`query`，因此`$_GET`数组对它们都可用。换句话说，POST 请求也有`$_GET`参数。难怪人们会被 HTTP 和 REST 绊倒。但是更糟的是。

除了 URI，一些 HTTP 请求可能包含消息体；不是所有的请求(例如，GET 可能不是)——而是一些。包含消息体的请求最常见的例子是 POST 类型。输入`$_POST`变量。像它的表亲`$_GET`一样，这在启动时被填充，但是消息体被解析为 [`application/x-www-form-urlencoded`](https://www.w3.org/TR/html401/interact/forms.html) 。与`$_GET`不同，这不是 HTTP 规范的一部分，而是浏览器提交 HTML `<form>`的特定编码类型。实际上，这是一个相当安全的赌注，因为浏览器只能以 GET 和 POST 的形式提交表单，但是从技术上来说，没有什么可以阻止某人发送带有表单编码消息体的 [PUT](https://www.w3.org/Protocols/rfc2616/rfc2616-sec9.html#sec9.6) 类型请求。

然后是`$_FILES`，与`$_POST`相似，它是对消息体的解释，但是这次是根据 [rfc 1867](http://www.ietf.org/rfc/rfc1867.txt) ，顺便说一下，这是 POST 请求的推荐编码。耶！

现在所有的规范都已经总结出来了，下面是我对 PHP 的不满。

`$_GET`和`$_POST`的问题在于，很多人假设——可以理解，但却是错误的——T2 以某种方式与获取请求联系在一起，而`$_POST`相应地就是你用来发布请求的。但事实并非如此。一个帖子请求可以在`$_GET`中包含相关数据。还是在`$_FILES`。表单编码不限于 POST 请求——因此`$_POST`可能包含 PUT 请求的相关数据。

目前的名称混乱，模糊了 HTTP 的意图；更具描述性的名字应该是`$_QUERY`而不是`$_GET`和`$_FORM` *而不是`$_POST`(这将符合名字`$_FILES`的词源)。

*正如[在评论](https://www.sitepoint.com/on-get-and-post/#comment-873781)中指出的，也许`$_FORM`也不是最好的名字。

如果改变这一点似乎很麻烦，那么请允许我提醒一下，几年前[也有人做过类似的事情](http://www.php.net/releases/4_1_0.php)，那时候似乎没人会为此烦恼。但首先，对文档进行澄清将是朝着正确方向迈出的一步。

而我更不会评论 [`$_REQUEST`](http://www.php.net/manual/en/reserved.variables.request.php) 的龌龊。

## 分享这篇文章