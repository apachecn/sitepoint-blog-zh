# 用 Google 库 API 加载 jQuery

> 原文：<https://www.sitepoint.com/load-jquery-google-libraries-api/>

[![google load jquery](img/c8dacdaf165c74d500d010dec98eee08.png "google-load-jquery")](https://www.sitepoint.com/wp-content/uploads/jquery4u/2010/11/google-load-jquery.png)

通常，如果您想使用 jQuery，您需要下载最新的包，保存。js 文件，然后在页面标题中包含该链接。这可能会非常耗时，尤其是当您需要测试不同版本的 jQuery 与您正在开发的插件或代码的兼容性时。每次他们发布新版本(通常每隔几个月左右)都要升级到最新版本，这也很烦人。谷歌又来拯救我们了。)通过提供一个库 API，它可以加载几乎任何 JavaScript 库的最新版本(或任何版本)。这样可以节省您每次使用库时将它们下载到自己的主机上的时间。[使用普通 JavaScript](http://www.jquery4u.com/javascript/dynamically-load-jquery-library-javascript/) 动态加载 jQuery 库。

> <address>库 API 为您的应用提供了稳定、可靠、高速、全球可用的访问所有最流行的开源 JavaScript 库的能力。您的应用程序可以使用我们非常灵活的加载器`google.load()`或直接的、基于路径的脚本访问。</address>

## 使用 Google API 动态加载 jQuery

**第一步** [获取你的 Google API 密匙](http://code.google.com/apis/loader/signup.html "get your google api")

**第二步** 把这段代码放在你的头中:

或者您可以使用直接链接:

## 无附加条件包括，最新版本

```
//GOOGLE APIs 
```