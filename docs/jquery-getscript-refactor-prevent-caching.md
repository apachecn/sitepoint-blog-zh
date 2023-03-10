# jQuery。重构 getScript()以防止缓存

> 原文：<https://www.sitepoint.com/jquery-getscript-refactor-prevent-caching/>

jQuery 的一个特性。getScript()的功能在于它包含了每个 ajax 脚本调用的唯一 id(时间戳之类的)。当我运行 setTimeout 来获取一个脚本时，这给我带来了一个问题，但它正在重新加载同一个脚本…这不好。所以我决定重构 jQuery 的。getScrip()来防止缓存，所以**它只加载脚本一次**。

### 新的 getScript 函数

```
//getScript refactor to prevent caching
(function () {
    $.getScript = function(url, callback, cache)
    {
        $.ajax({
                type: "GET",
                url: url,
                success: callback,
                dataType: "script",
                cache: cache
        });
    };
})();
```

**为了证明它的工作**，我测试了 HTTP 请求加载时间，看看圆圈，你可以看到脚本现在在大约 7 毫秒内加载缓存版本。


让我们仔细看看原作。getScript()函数以及我们如何使用额外的参数调用新函数。

```
//normal no cached version
$.getScript('js/script.js', function()
{
    //do something after script loaded
});
```

### 如何调用新的？getScript()函数

如果您希望缓存它，只需在末尾包含 true 即可。

```
//cache = true
$.getScript('js/script.js', function()
{
    //do something after script loaded
}, true);
```

**或**

```
// turn on cache
$.ajaxSetup({ cache: true });
$.getScript(url, callback);
// turn cache back off
$.ajaxSetup({ cache: false });
```

## 分享这篇文章