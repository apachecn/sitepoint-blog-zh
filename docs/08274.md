# 加载 jQuery 库的最佳方式(带后备)

> 原文：<https://www.sitepoint.com/load-jquery-library-with-fallback/>

以下是我认为加载 jQuery 库和 jQuery UI 库的**最好和最可靠的方法。现在每个人都使用 Google CDN 来加载最新版本的 jQuery 库，这导致许多浏览器缓存这个文件。如果你的网页也这样做，很有可能浏览者已经缓存了这个文件，因此在他们开始浏览你的网页之前就少了一个需要加载的文件。**

如果 Google CDN 宕机(不太可能)或者您很有可能从本地主机进行开发，那么会有一个**回退来加载 jQuery** 库的本地版本。

## 加载 jQuery 库

```
<!-- Grab Google CDN's jQuery, with a protocol relative URL; fall back to local if offline -->
<script src="//ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js"></script>
<script>window.jQuery || document.write('</script><script src="js/ext/jquery-1.7.2.min.js">< /script>')</script>
```

## 加载 jQuery 库

```
<!-- Grab Google CDN's jQuery UI, with a protocol relative URL; fall back to local if offline -->
<script type="text/javascript" src="//ajax.googleapis.com/ajax/libs/jqueryui/1/jquery-ui.min.js"></script>
<script>window.jQuery.ui || document.write('</script><script src="js/ext/jquery-ui.min.js">< /script>')</script>
```

## 其他方法

[使用普通 JavaScript 动态加载 jQuery 库](http://www.jquery4u.com/javascript/dynamically-load-jquery-library-javascript/)

## 分享这篇文章