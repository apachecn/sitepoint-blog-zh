# 如何使用 HTML5 数据属性

> 原文：<https://www.sitepoint.com/use-html5-data-attributes/>

回到过去的 XHTML/HTML4 时代，开发人员在存储与 DOM 相关的任意数据时几乎没有选择。你可以创造自己的属性，但这是有风险的；您的代码将是无效的，浏览器可能会忽略数据，如果名称成为标准的 HTML 属性，将会导致问题。

因此，大多数开发人员依赖于`class`或`rel`属性，因为它们是唯一允许相当灵活的字符串的属性。例如，假设我们正在创建一个小部件来显示消息，比如 Twitter 时间表。理想情况下，JavaScript 应该是可配置的，不需要改变它的代码——所以我们在`class`属性中定义用户的 ID，例如

```
<div id="msglist" class="user_bob"></div>
```

我们的 JavaScript 代码将寻找 ID 为 **msglist** 的元素。一点字符串解析会发现一个以 **user_** 开头的类，假设“鲍勃”是 ID，并显示来自该用户的所有消息。

假设我们想要定义邮件的最大数量，并忽略超过 6 个月(180 天)的邮件:

```
<div id="msglist" class="user_bob list-size_5 maxage_180"></div>
```

我们的`class`属性变得越来越繁琐；很容易出错，JavaScript 解析更复杂。

## HTML5 数据属性

幸运的是，HTML5 引入了自定义数据属性。您可以使用前缀为`data-`的任何小写名称，例如

```
<div id="msglist" data-user="bob" data-list-size="5" data-maxage="180"></div>
```

自定义数据属性:

*   是字符串——可以存储任何可以用字符串编码的东西，比如 JSON。类型转换必须在 JavaScript 中处理。
*   应该仅在不存在合适的 HTML5 元素或属性时使用。
*   是页面私有的。与微格式不同，它们应该被外部系统(如搜索引擎索引机器人)忽略。

## JavaScript 解析#1: getAttribute 和 setAttribute

每个浏览器都允许你使用`getAttribute`和`setAttribute`方法获取和修改`data-`属性，例如

```
var msglist = document.getElementById("msglist");

var show = msglist.getAttribute("data-list-size");
msglist.setAttribute("data-list-size", +show+3);
```

它可以工作，但是应该只作为旧浏览器的后备。

## JavaScript 解析#2: jQuery data()方法

从 1.4.3 版本开始，jQuery 的`data()`方法已经解析了 HTML5 数据属性。您不需要指定`data-`前缀，因此可以编写等价的代码:

```
var msglist = $("#msglist");

var show = msglist.data("list-size");
msglist.data("list-size", show+3);
```

但是，要注意 jQuery 巧妙地试图将数据转换成合适的类型(布尔值、数字、对象、数组或 null ),并避免接触 DOM。与`setAttribute`不同，`data()`方法不会物理地改变`data-list-size`属性——如果您在 jQuery 之外检查它的值，它仍然是‘5’。

## JavaScript 解析#3:数据集 API

最后，我们有 HTML5 数据集 API，它返回一个 DOMStringMap 对象。您应该注意到，数据属性名称是通过去掉前缀`data-`、删除连字符并转换成 camelCase 来映射的，例如

| 属性名 | 数据集 API 名称 |
| 数据用户 | 用户 |
| 数据最大值 | maxage(最大值) |
| 数据列表大小 | listSize |

我们的新代码:

```
var msglist = document.getElementById("msglist");

var show = msglist.dataset.listSize;
msglist.dataset.listSize = +show+3;
```

所有现代浏览器都支持 datalist API，但 IE10 及以下版本不支持。有一个 [shim 可用](https://github.com/Modernizr/Modernizr/wiki/HTML5-Cross-browser-Polyfills#dataset-property-for-use-with-custom-data--attributes),但是如果你正在为旧的浏览器编码，使用 jQuery 可能更实用。

## 分享这篇文章