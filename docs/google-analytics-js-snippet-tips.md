# 新的 Google Analytics.js 片段提示

> 原文：<https://www.sitepoint.com/google-analytics-js-snippet-tips/>

谷歌分析片段又一次变得更大更好了…让我们来看看…

## 默认新 Analytics.js 脚本

这目前还在测试阶段。

我看到 [HTML5 样板文件](https://github.com/h5bp/html5-boilerplate/blob/master/index.html)被巧妙的重命名为变量..现在是 b，o，I，l，e，r …呵呵..

不要破坏你当前的追踪对象，只需将 ga 重命名为你一直使用的名称，比如 __gaTracker。

```
(function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
(i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
})(window,document,'script','//www.google-analytics.com/analytics.js','__gaTracker');
__gaTracker('create', 'UA-XXXX-Y');
__gaTracker('send', 'pageview');
```

## 在本地主机上测试

在某些情况下，您可能希望从本地主机上运行的 web 服务器测试 analytics.js。要设置 analytics.js cookies，您需要使用以下命令禁用默认 cookie 域:

```
ga('create', 'UA-XXXX-Y', {
  'cookieDomain': 'none'
});
```

在你的网络应用上追踪你的散列网址。可以这样设置页面 url。将它添加到您的页面模板中。

运筹学

不要忘记从主代码片段中删除 send。所以现在看起来是这样的:

要捕获一个新的事件，只需在您想要跟踪事件时添加它

```
ga('send', 'event', 'category', 'action', 'label', value);
```

其中标签和值是可选的。

举个例子，

```
ga('send', 'event', 'register', 'currentUser');
```

**GA 基础配置:**[https://developers . Google . com/analytics/dev guides/collection/analytic sjs/](https://developers.google.com/analytics/devguides/collection/analyticsjs/)

**GA 高级配置:**[https://developers . Google . com/analytics/dev guides/collection/analytics js/Advanced？hl=es](https://developers.google.com/analytics/devguides/collection/analyticsjs/advanced?hl=es)

## 分享这篇文章