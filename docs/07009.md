# 我要用。大小()或。Javascript 中的长度？

> 原文：<https://www.sitepoint.com/size-length-javascript/>

> 我用。大小()或。Javascript 中的长度？

让我们仔细看看…

。size()只是调用。长度(在下面的 jQuery 源代码中清楚地显示了这一点),所以我们正在保存一个函数调用

```
//https://code.jquery.com/jquery-latest.js
// The number of elements contained in the matched element set
        size: function() {
                return this.length;
        },
```

**。length()已经被证明比。size()** (清楚显示的是一个[jsperf http://jsperf.com/size-vs-length](http://jsperf.com/size-vs-length)测试)。那为什么？jQuery 中的 size()函数？我最初的猜测是他们把它抽象化了，这样如果用不同的方法计算。将来需要长度，它可能提供向后兼容的 API。

你可能见过的常见用法:

```
//check if a DOM element is present
if ($('#id').length > 0) { ... }
```

简而言之，我用。直到有人给我一个充分的理由。

## 分享这篇文章