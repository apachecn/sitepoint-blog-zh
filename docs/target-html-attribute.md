# 目标(HTML 属性)

> 原文：<https://www.sitepoint.com/target-html-attribute/>

### 描述

target 属性已被弃用，它作为一种布局机制(如框架集的布局机制)已不再常见。但是，如果您发现自己必须维护基于框架集的网站，您可能需要在框架或窗口中打开链接，而不是在链接源所在的框架或窗口中打开链接。

### 例子

这里，`a`元素的`target`属性被设置为`"_top"`:

```
<p>You can try our <a href="cakes.html" *target="_top"*>lovely range of cakes</a>.</p>
```

### 价值

该属性可以采用以下任何值:

*   `"_blank"`在
    全新窗口中加载内容

*   `"frame name"`以自定义名称将内容加载到
    框架中

*   `"_parent"`加载
    中的内容，父`frameset`为当前
    `frame`

*   `"_self"` 在同一帧中加载内容(该属性通常不是必需的，因为这是默认行为，除非  元素另有指定。在这种情况下，您需要使用`"_self"`来覆盖规范；比如`<base *target="searchresults"*/>`。)

*   `"_top"`加载顶层`frameset`的内容(实际上是整个浏览器窗口)，不管当前`frame`下面有多少嵌套层

## 分享这篇文章