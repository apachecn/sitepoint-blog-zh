# 使用 HTML5 应用程序缓存时要避免的常见陷阱

> 原文：<https://www.sitepoint.com/common-pitfalls-avoid-using-html5-application-cache/>

[应用缓存](https://developers.whatwg.org/offline.html)，也称为 AppCache，最近一直是 web 开发人员的热门话题。AppCache 使您能够允许网站访问者在离线时浏览您的网站。你甚至可以在用户电脑的缓存中存储网站的部分内容，比如图像、样式表或网络字体。这可以帮助你的网站加载更快，从而减少服务器的负载。

要使用 AppCache，您需要创建一个文件扩展名为“appcache”的清单文件，例如:`manifest.appcache`。在这个文件中，您可以列出所有想要缓存的文件。要在您的站点上启用它，您必须在您的网页上的`html`元素中包含对该清单文件的引用，如下所示:

```
<html lang="en" manifest="manifest.appcache">
```

下面是一个示例清单文件:

```
CACHE MANIFEST
# 23-01-2015 v0.1
/style.css
/logo.gif
/script.js

NETWORK:
*

FALLBACK:
/server/ /fallback.html
```

除了 AppCache 的好处之外，为了防止破坏用户体验和破坏应用程序，您还应该避免一些常见的陷阱。

## 从不在清单文件中列出清单文件

如果您将清单文件本身包含在应用程序缓存清单中，它会陷入某种循环，从而几乎不可能通知您的网站有新的缓存文件可用，它应该下载并使用新的清单文件而不是旧的。因此，始终注意不要犯以下错误:

```
CACHE MANIFEST
# 23-01-2015 v0.1

manifest.appcache
page2.css
```

## 非缓存资源不会加载到缓存页面上

这是第一次使用 AppCache 时非常常见的错误。这就是清单文件中的`NETWORK`标志的用处。清单文件的`NETWORK`部分指定了 web 应用程序需要在线访问的资源。

在`NETWORK`标志下指定的 URL 基本上是“白名单”，即当互联网连接可用时，在该标志下指定的文件总是从服务器加载。例如，下面的代码片段确保加载包含在`/api/`子树中的资源的请求总是从网络而不是从缓存中加载。

```
NETWORK:

/api
```

## 始终在中设置应用程序类型清单。htaccess 您的服务器

清单文件应始终在正确的媒体类型`text/cache-manifest`下提供。如果没有设置媒体类型，那么 AppCache 将不起作用。

它应该总是在生产服务器的`.htaccess`中配置。这一点在大多数讲授 AppCache 的教程中都提到过，但是当许多开发人员将他们的 web 应用程序从开发服务器转移到生产服务器时，他们却忽略了这一点。

在 Apache 的`.htaccess`文件中输入以下内容:

```
AddType text/cache-manifest .manifest
```

如果您正在将您的应用程序上传到 Google App Engine，您可以通过将以下代码添加到您的`app.yaml`文件来完成相同的任务:

```
- url: /public_html/(.*\.appcache)
  static_files: public_html/\1
  mime_type: text/cache-manifest
  upload: public_html/(.*\.appcache)
```

## 避免由于找不到文件而删除整个清单

如果清单文件中指定的单个文件没有找到或无法下载，则整个清单文件都将被丢弃。这是 AppCache 的一种奇怪行为，在设计使用 AppCache 的 web 应用程序时应该记住这一点。

例如:

```
CACHE MANIFEST
# 23-01-2015 v0.1
/style.css
/logo.gif
/script.js
```

如果`logo.gif`被删除，AppCache 将无法找到被删除的图像文件，因此清单文件中的任何内容都不会被执行。

## 即使在线，也从 AppCache 加载数据

一旦您的 web 浏览器保存了缓存清单文件，即使用户已连接到互联网，也会从缓存清单本身加载文件。此功能有助于提高网站的加载速度，并有助于减少服务器负载。

## 在清单文件更新之前，服务器上的更改不会发生

从上一点中您知道，即使用户在线，数据也是从 AppCache 加载的，所以在更新清单文件之前，您对网站或服务器中的文件所做的更改不会生效。

您必须在更新网站后更新清单文件，否则您的用户将永远看不到更改，但他们将只能看到以前缓存的数据。您可以在清单文件的注释中更新版本号或日期，以强制用户的 web 浏览器下载清单文件的新版本。例如，如果在对您的网站进行更改之前，以下文件曾经是您的清单文件:

```
CACHE MANIFEST
# 23-01-2015 v0.1
```

可以将其更改为类似下面的代码块，这样用户的浏览器就可以下载清单文件的新副本。

```
CACHE MANIFEST
# 23-01-2015 v0.2
```

请注意，`#`前面的行是一个没有执行的注释行。

## 清单文件必须由与主机相同的来源提供

虽然清单文件可以保存对要从其他域缓存的资源的引用，但它应该从与宿主页面相同的来源提供给 web 浏览器。如果不是这样，清单文件将无法加载。例如，以下清单文件是正确的:

```
CACHE MANIFEST
# 23-01-2015 v0.2

https://cdnjs.cloudflare.com/ajax/libs/jquery/2.1.3/jquery.js
```

在这里，我们已经指定了要存储在用户浏览器缓存中的内容，这些内容是从另一个域引用的，这非常好。

## 相对 URL 相对于清单的 URL

需要注意的一件重要事情是，您在清单中提到的相对 URL 是相对于清单文件的，而不是相对于您引用清单文件的文档。如果在清单和引用不在同一路径时出现此错误，资源将无法加载，进而清单文件也将无法加载。

如果您的应用程序结构如下所示:

```
css/style.css
js/main.js
img.jpg
index.html
manifest.appcache
```

那么您的清单文件应该类似于:

```
CACHE MANIFEST
# 23-01-2015 v0.2

css/style.css
js/main.js
img.jpg
```

## 以编程方式检查清单的状态

您可以通过测试`window.applicationCache.status`，以编程方式检查您的应用程序是否使用了缓存清单的更新版本。下面是一些示例代码:

```
function onUpdateReady() {
  alert('found new version!');
}

window.applicationCache.addEventListener('updateready', onUpdateReady);

if (window.applicationCache.status === window.applicationCache.UPDATEREADY) {
  onUpdateReady();
}
```

在网站上运行上述代码可以让您知道 AppCache 清单的新更新何时可用。请注意`UPDATEREADY`是一个定义好的状态。您甚至可以使用`onUpdateReady()`函数中的`swapCache()`方法用新的清单文件替换旧的清单文件:

```
window.applicationCache.swapCache();
```

## 结论

AppCache 是一项有用的技术，但是正如我们已经看到的，在项目中实现它时应该小心。开发人员应该有选择性地选择清单文件中应该包含的内容。理想情况下，清单文件应该包含静态内容，比如样式表、脚本、web 字体和图像。但是，对于清单文件中包含的内容，您总是最佳的判断者。Appcache 是一把双刃剑，使用时一定要小心！

上面讨论的大部分内容已经在其他地方讨论过了，还有一些额外的要点。您可以查看以下资源了解更多信息:

*   MDN 上的应用程序缓存 Gothas
*   杰克·阿奇博尔德的《应用缓存是个混球》
*   杰克·阿奇博尔德的离线食谱

## 分享这篇文章