# JavaScript setInterval()函数示例

> 原文：<https://www.sitepoint.com/setinterval-example/>

JavaScript setInterval 函数可用于使用常规的基于时间的触发器来自动化任务。**澄清一下， [setInterval()](http://www.w3schools.com/jsref/met_win_setinterval.asp) 是一个原生的 JavaScript 函数。**

这个函数非常类似于 [jQuery setTimeout()函数](http://jquery4u.com/jquery-functions/settimeout-example/)。

## 基本 setInterval()示例

```
setInterval(function() {
      // Do something every 5 seconds
}, 5000);
```

**提示:**如果你的变化图像从一个 PHP 脚本中动态加载变量，你将需要添加一些随机数到脚本中，以便它在所有浏览器中强制刷新。您可以通过使用以下代码生成一个随机数来实现这一点。

```
$(document).ready(function()
{
    var refreshId = setInterval( function() 
    {
        var r = (-0.5)+(Math.random()*(1000.99));
        $('#img-container').load('images/gallery/best/random.php?'+r);
    }, 5000);
});
```

**提示:**您可能还必须使用 ajax 方法而不是 load，以防止 AJAX 请求被缓存。
**提示:**或者，你可以粘贴 header("Cache-Control: no-cache，must-revalidate ")；靠近 random.php 文件的顶部，以防止浏览器缓存。

## 分享这篇文章