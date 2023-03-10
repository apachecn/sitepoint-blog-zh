# 如何用 JavaScript 获取 URL 参数

> 原文：<https://www.sitepoint.com/get-url-parameters-with-javascript/>

**URL 参数(也称为查询字符串参数或 URL 变量)用于通过 URL 将少量数据从页面发送到页面，或从客户端发送到服务器。它们可以包含各种有用的信息，比如搜索查询、链接推荐、产品信息、用户偏好等等。**

在本文中，我们将向您展示如何使用 JavaScript 解析和操作 URL 参数。

## 获取 URL 参数

在现代浏览器中，由于有了 [URLSearchParams 接口](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams)，这变得容易多了。这定义了许多实用方法来处理 URL 的查询字符串。

假设我们的 URL 是`https://example.com/?product=shirt&color=blue&newuser&size=m`，我们可以使用`window.location.search`获取查询字符串:

```
const queryString = window.location.search;
console.log(queryString);
// ?product=shirt&color=blue&newuser&size=m 
```

然后我们可以使用`URLSearchParams`解析查询字符串的参数:

```
const urlParams = new URLSearchParams(queryString); 
```

然后我们在结果上调用它的任何方法。

例如，`URLSearchParams.get()`将返回与给定搜索参数相关联的第一个值:

```
const product = urlParams.get('product')
console.log(product);
// shirt

const color = urlParams.get('color')
console.log(color);
// blue

const newUser = urlParams.get('newuser')
console.log(newUser);
// empty string 
```

## 其他有用的方法

### 检查参数是否存在

您可以使用`URLSearchParams.has()`来检查某个参数是否存在:

```
console.log(urlParams.has('product'));
// true

console.log(urlParams.has('paymentmethod'));
// false 
```

### 获取参数的所有值

您可以使用`URLSearchParams.getAll()`返回与特定参数相关的所有值:

```
console.log(urlParams.getAll('size'));
// [ 'm' ]

//Programmatically add a second size parameter.
urlParams.append('size', 'xl');

console.log(urlParams.getAll('size'));
// [ 'm', 'xl' ] 
```

### 迭代参数

`URLSearchParams`还提供了一些熟悉的`Object`迭代器方法，允许你迭代它的键、值和条目:

```
const
  keys = urlParams.keys(),
  values = urlParams.values(),
  entries = urlParams.entries();

for (const key of keys) console.log(key);
// product, color, newuser, size

for (const value of values) console.log(value);
// shirt, blue, , m

for(const entry of entries) {
  console.log(`${entry[0]}: ${entry[1]}`);
}
// product: shirt
// color: blue
// newuser:
// size: m 
```

## 浏览器支持

[浏览器对`URLSearchParams`的支持不错](https://caniuse.com/#search=URLSearchParams%28%29)。在撰写本文时，所有主流浏览器都支持它。

[<picture><source type="image/webp" srcset="https://caniuse.bitsofco.de/image/urlsearchparams.webp">![Data on support for the urlsearchparams feature across the major browsers from caniuse.com](img/7339ba660585b88cef4f22702906e346.png)</picture>](http://caniuse.com/#feat=urlsearchparams)