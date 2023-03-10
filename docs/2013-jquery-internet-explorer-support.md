# 2013 年 jQuery 对 Internet Explorer 的支持

> 原文：<https://www.sitepoint.com/2013-jquery-internet-explorer-support/>

![jquery2-support-for-internet-explorer](img/09daecc48b171e89afd71eb3bb7f32b6.png)

这篇文章展望了 jQuery 2.0 的前景，以及为什么当被弃用/删除的 jQuery 函数产生“函数未定义”(或类似)错误时，您的代码可能会“中断”。[这个官方帖子](http://blog.jquery.com/2012/06/28/jquery-core-version-1-9-and-beyond/)声明 **jQuery 2.0 将移除对 IE 6/7/8** 古怪的支持，如 borked 事件模型、IE7“属性”、HTML5 垫片等。也建议如果需要 IE 6/7/8 支持，选择 1.9；否则可以用 2.0。随着 jQuery 插件开始崩溃，我想我们也会看到很多人将 jQuery 的早期版本迁移到 jQuery 1.9。

## 期待 jQuery 2.0

jQuery 2.x 将更加精简([支持 IE 的数百行代码将被删除](http://www.jquery4u.com/?p=16542))。然而，jQuery 1.9.x 将保持对 IE 的支持，所以您可能会看到类似这样的内容:

```
<!--[if lt IE 9]>
<script src="jquery-1.9.x.js"</script>
 < ![endif]-->
 <!--[if gte IE 9]>
<script src="jquery-2.x.js"</script>
 <![endif]-->
```

支持旧版本的 Internet Explorer 有支持和反对的理由。根据我作为承包商在不同公司工作的经验。

*   **IE6** 已死，不再支持(0.3%)。
*   **IE7** 接近死亡(1.0%)。大多数政府部门现在都在运行 IE8+版本，但系统映像升级的过程非常缓慢，因此网站仍然需要支持 IE7，但随着统计数据的下降，他们慢慢地说“不支持 IE7”。
*   **IE8/IE9** 仍受支持(6.4%，5.9%)。
*   **IE10** (0.8%)的使用量竟然比 IE7 少。

[数字来源。](http://www.w3schools.com/browsers/browsers_explorer.asp)

[![browser-stats-ie-2013](img/74be5ef87eea61279112ad7f89c7f795.png)](https://gs.statcounter.com/#browser_version_partially_combined-ww-monthly-201201-201301-bar)

## 对源代码感兴趣？

**源代码 jQuery 1 . 9 . 1:**【https://code.jquery.com/jquery-1.9.1.js】
**源代码 jQuery 2.0 Beta:**[https://code.jquery.com/jquery-2.0.0b1.js](https://code.jquery.com/jquery-2.0.0b1.js)

## 那么，移除对 IE 的支持是否让 jQuery 变得更快了呢？

**是和否**

使用[jsperf–jQuery 1 . 9 . 1 与 2.0.0 beta](http://jsperf.com/jquery-1-7-2-vs-jquery-1-8/13) 的基准测试表明:

*   ID/类别选择器速度更快
*   交互稍慢
*   CSS getter/setter 相同
*   。查找()稍快
*   不存在的 ID/类稍快
*   过滤下一个/上一个稍慢

文件大小从 **91kb 降至 81kb 缩小版**。

## 分享这篇文章