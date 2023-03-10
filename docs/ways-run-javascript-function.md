# 运行 JavaScript 函数的不同方式

> 原文：<https://www.sitepoint.com/ways-run-javascript-function/>

我回顾了一些工作，认为写一篇关于从 HTML 元素内部加载 JavaScript 函数(特别是 foo())的不同方法的文章会很好。看看下面的一些例子。

## 1–裸锚

*   在某些浏览器中，鼠标光标在悬停时可能不会改变。
*   CSS 可以用来解决这个问题:}

## 2–锚散列 href

*   鼠标悬停在#处显示链接
*   在 IE6、IE7 上，页面可能会移动到顶部

```
 [...](#) 
```

## 3–伪锚点

*   点击链接时调用函数的老方法。
*   出于可用性和可访问性的原因，不推荐使用伪协议 hrefs。
*   现在，由于可用 API 的涌入，人们开始考虑反向编程。
*   它很乱，很长，人们在状态栏中看到它，它没有任何意义。
*   Opera 不喜欢 href = " JavaScript:[任何东西]"

```
 [...](javascript:foo()) 
```

## 4–锚伪空

*   使用 javascript:有些人认为任何东西都是不好的做法。
*   伪协议 hrefs 可以导致 IE 启用预期页面被替换的等待状态，并自动禁用资源饥渴活动。
*   JavaScript:[任何东西]用于 bookmarklets。
*   **[什么是 JavaScript:void(0)；？](http://www.jquery4u.com/?p=16679)**

```
 [...](javascript:void(0)) 
```

## 5–锚点返回错误

*   返回 false 会导致不计算 href="#"。
*   比使用伪协议更安全的方法，例如页面跳转。
*   回报有时是不可靠的。

```
 [...](#) 
```

## 6–锚定漂亮的 url/jQuery

*   当用户将鼠标悬停在链接上时，会看到#some-real-url。
*   如果 JavaScript 被禁用，他们会看到一些信息。

```
 [...](#some-real-url) 

$(document).on('click', 'a.mylink', function(e)
{
     //prevent the page from going to href
     e.preventDefault();

     //run the function
     foo();
});
```

## 分享这篇文章