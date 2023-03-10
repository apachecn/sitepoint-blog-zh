# 如何创建脱机 Web 应用程序

> 原文：<https://www.sitepoint.com/offline-web-application-tutorial/>

大多数浏览器厂商都在 HTML5 中引入了离线 web 应用功能。它允许在线应用程序工作，即使用户失去或断开他们的互联网连接。例如，即使您找不到 wi-fi 热点，您也可以在您的网络邮件客户端撰写邮件。

这篇文章中提供的例子可以在 Firefox 3.x 上运行，在某种程度上也可以在其他浏览器上运行——包括 IE8。然而，实现和特性集是不同的。离线技术处于不断变化的状态，所以我不建议在生产环境中使用这些代码。

创建支持脱机的应用程序有三个方面:指定哪些文件是必需的，确定浏览器何时脱机，以及在本地保存数据。

## 缓存清单

您的浏览器在离线时无法访问文件，因此您需要指定哪些资源是必需的，以便可以缓存这些资源。这是在缓存清单中实现的——它是基本文件的简单列表，例如

```
 CACHE MANIFEST
styles.css
jquery-1.4.min.js
offline.js
index.html 
```

将此文件另存为“offline.manifest ”,并在您页面的`html`标签中链接到它:

```
 <html manifest="offline.manifest"> 
```

请注意，offline.manifest 应该由 text/cache-manifest MIME-type 提供。

当您加载文件时，浏览器会询问您是否允许将数据存储在您的 PC 上。

## 如何检测浏览器何时离线

JavaScript 代码可以使用`navigator.onLine`检测浏览器何时在线或离线。大多数浏览器都支持该属性，联机时返回 true，脱机时返回 false。

Firefox 还允许你给窗口对象的`online`和`offline`事件附加处理程序，例如

```
 // standard event listeners
window.addEventListener("online", function() { ... });
window.addEventListener("offline", function() { ... });

// or jQuery alternative
$(window).bind("online offline", function() { ... }); 
```

## 在本地保存数据

DOM 存储是脱机保存数据的最简单的方法之一。您可以使用以下任一方式:

*   `window.sessionStorage`在会话期间保留值，或
*   `window.localStorage`长期保持价值。

这两个对象提供了相同的方法:

*   `setItem(string name, string value)`:添加或更新商店中的值
*   `getItem(string name)`:从存储中获取一个命名值
*   `removeItem(string name)`:从存储中删除一个命名值
*   `length`:存储值的数量
*   `key(long index)`:索引处的键名
*   `clear()`:清空店铺

示例:

```
 // set a session value
window.sessionStorage.setItem("key", "my data");

// get a session value - returns "my data"
window.sessionStorage.getItem("key"); 
```

## 把所有的放在一起

查看 [**示例页面……**](https://blogs.sitepointstatic.com/examples/tech/offline/index.html)

直到你在 Firefox 中选择文件>离线工作，它才变得非常有趣。状态将立即变为“脱机”。然后，您可以输入一些数据，并单击“保存数据”按钮。数据将存储在本地，而不是发布到服务器。刷新页面，您输入的值将重新出现。

如果你想仔细看看代码，请[下载文件](https://blogs.sitepointstatic.com/examples/tech/offline/offline-example.zip)。

## 分享这篇文章