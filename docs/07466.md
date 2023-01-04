# 如何在不知道 ID 的情况下清除 setInterval()

> 原文：<https://www.sitepoint.com/clear-setinterval-knowing-id/>

**问题**
声明了一个 setInterval()而没有保留对它的引用(从函数调用 setInterval()返回，返回注册事件的 id 号)。

像这样:

```
var interval = setInterval(function() {
    console.log('i');
}, 1000);
console.log(interval);
```

你可以看到它分配了一个随机数作为事件的 id。然后，当我们清除间隔时，它会使用该 id。

只需拨打电话:

```
clearInterval(interval);
```

**一个困难的解决方案**
现在这不是一个好的编码实践(有很多原因),但是如果你真的需要清除这个时间间隔，我们可以简单地做一个循环来找到事件的 id 来停止所有正在运行的 setInterval()事件。我做了一个简单的练习给你看。[https://jsfiddle.net/WqCws/](https://jsfiddle.net/WqCws/)

```
for(i=0; i<100; i++)
{
    window.clearInterval(i);
}
```

我很确定这对 setTimeout()和 clearTimeout()也是有效的；

[https://jsfiddle.net/WqCws/embedded/](https://jsfiddle.net/WqCws/embedded/)

## 分享这篇文章