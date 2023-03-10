# 将你的网站改造成一个进步的网络应用

> 原文：<https://www.sitepoint.com/retrofit-your-website-as-a-progressive-web-app/>

*这篇关于将你的网站改造成一个进步的网络应用的文章收录在我们的选集[现代 JavaScript](https://www.sitepoint.com/premium/books/modern-javascript) 中。如果你想在一个地方获得所有现代 JavaScript 的速度，注册 SitePoint Premium 并下载一份。*

最近关于渐进式网络应用(pwa)有很多讨论，许多人质疑它们是否代表了(移动)网络的未来。

我不会卷入整个原生应用与 PWA 的辩论，但有一点是肯定的:它们对增强移动和改善用户体验有很大帮助。

随着移动网络接入注定要超过所有其他设备的总和，你能忽视这一趋势吗？

![Woman in a 1950s, retro scene looking at a tablet with Progressive Web App icons](img/9a4c87d406cc6597b5eced0f08fa8b1e.png)

好消息是制作 PWA 并不难。事实上，很有可能将一个现有的网站转换成 PWA。这正是我在本教程中要做的。当你完成时，你将拥有一个像本地 web 应用一样的网站。它将离线工作，并有自己的主屏幕图标。

### 目录 

*   什么是渐进式网络应用？
*   [渐进式网络应用是渐进式改进](#progressivewebappsareprogressiveenhancements)
*   [不仅仅是应用程序](#itsnotjustapps)
*   [演示代码](#demonstrationcode)
*   [连接设备](#connectadevice)
*   [第一步:启用 HTTPS](#step1enablehttps)
*   [步骤 2:创建 Web 应用清单](#step2createawebappmanifest)
*   [步骤 3:创建一个服务工作者](#step3createaserviceworker)
*   [安装事件](#installevent)
*   [激活事件](#activateevent)
*   [获取事件](#fetchevent)
*   [奖励步骤 4:创建一个有用的离线页面](#bonusstep4createausefulofflinepage)
*   [开发工具](#developmenttools)
*   [PWA Gotchas](#pwagotchas)
*   [网址隐藏](#urlhiding)
*   [缓存过载](#cacheoverload)
*   [缓存刷新](#cacherefreshing)
*   [有用的链接](#usefullinks)
*   [评论](#comments)

## 什么是渐进式网络应用？

渐进式网络应用(简称为 **PWAs** )是网络技术中一项令人兴奋的创新。PWAs 由多种技术组成，使 web 应用程序像本地移动应用程序一样运行。开发人员和用户的优势克服了纯 web 和纯本机解决方案带来的限制:

1.  你只需要一个用开放的、标准的 W3C web 技术开发的应用程序。没有必要开发单独的本机代码库。
2.  用户可以在安装之前发现并试用您的应用。
3.  没有必要使用应用商店，遵守神秘的规则或支付费用。应用程序自动更新，无需用户干预。
4.  用户被提示“安装”，这将在他们的主屏幕上添加一个图标。
5.  启动时，PWA 会显示一个吸引人的启动画面。
6.  如果需要，可以修改浏览器 chrome 选项，以提供全屏体验。
7.  基本文件缓存在本地，因此 PWAs 的响应速度比标准 web 应用更快。(它们甚至可以比原生应用更快。)
8.  安装是轻量级的——可能只有几百 KB 的缓存数据。
9.  所有数据交换必须通过安全的 HTTPS 连接进行。
10.  PWAs 脱机工作，当连接恢复时可以同步数据。

现在还为时过早，但是[案例研究是积极的](https://developers.google.com/web/showcase/)。印度最大的电子商务网站 Flipkart 的销售转换率增长了 [70%,在线时间增长了三倍](https://developers.google.com/web/showcase/2016/flipkart),他们放弃了自己的原生应用程序，转而使用 PWA。全球最大的商业交易平台阿里巴巴也经历了类似的[转化率增长 76%](https://developers.google.com/web/showcase/2016/alibaba) 。

Firefox、Chrome 和其他基于 Blink 的浏览器都支持可靠的 PWA 技术。微软正在致力于边缘实现。苹果保持沉默，尽管 WebKit 五年计划中有[充满希望的评论。幸运的是，浏览器支持大多无关紧要…](https://trac.webkit.org/wiki/FiveYearPlanFall2015)

### 渐进式网络应用是渐进式的增强

你的应用仍然可以在不支持 PWA 技术的浏览器上运行。用户不会得到离线功能的好处，但是一切都会像以前一样继续工作。考虑到成本效益回报，没有理由*不*将 PWA 技术添加到您的系统中。

### 不仅仅是应用程序

谷歌已经领导了 PWA 运动，所以大多数教程描述了如何从头开始构建一个基于 Chrome 的、看起来像本地的移动应用。然而，你不需要一个特殊的单页应用程序或必须遵循材料界面设计准则。大多数网站可以在几个小时内被 PWA 化。这包括你的 WordPress 或者静态网站。

## 演示代码

演示代码可从 [GitHub](https://github.com/sitepoint-editors/pwa-retrofit) 获得。

它提供了一个简单的四页网站，包含一些图片、一个样式表和一个主 JavaScript 文件。该网站适用于所有现代浏览器(IE10+)。如果浏览器支持 PWA 技术，用户可以在离线时阅读之前浏览过的网页。

要运行代码，请确保安装了 [Node.js](https://nodejs.org/) ，然后在您的终端中使用以下命令启动提供的 web 服务器:

```
node ./server.js [port] 
```

在上面的代码中，`[port]`是可选的，默认为 8888。打开 Chrome 或其他基于 Blink 的浏览器，如 Opera 或 Vivaldi，然后导航到 [http://localhost:8888/](http://localhost:8888/) (或您指定的任何端口)。您也可以打开开发者工具(F12 或`Cmd/Ctrl` + `Shift` + `I`)查看各种控制台消息。

![Open website in browser](img/99eaa56a9af3c68ffbb95c51f904ceb3.png)

查看主页，也可能查看另一个主页，然后通过以下任一方式脱机:

1.  用`Cmd/Ctrl` + `C`停止网络服务器，或
2.  在开发者工具的*网络*或*应用-服务人员*标签中勾选*离线*复选框。

重新访问你之前浏览过的任何页面，它们仍然可以加载。访问一个您从未见过的页面，该页面显示有一个包含可查看页面列表的“您已脱机”页面:

![offline pages](img/bad0f76a59abffb4df29f3cfc8f6fe8a.png)

### 连接设备

您也可以在通过 USB 连接到 PC/Mac 的 Android 智能手机上查看演示页面。从左上角三点菜单的*更多工具*中打开*远程设备*面板。

![remote devices](img/54c61370e274b271778cf6b470917dd9.png)

选择左侧的*设置*，点击*添加规则*将端口 8888 转发到本地主机:8888。你现在可以在智能手机上打开 Chrome，导航到 [http://localhost:8888/](http://localhost:8888/) 。

您可以使用浏览器菜单“添加到主屏幕”。进行几次访问后，浏览器会提示您“安装”。这两个选项都会在主屏幕上创建一个新图标。浏览几页，然后关闭 Chrome，断开设备。然后就可以启动 *PWA 网站* app 了。您将看到一个闪屏，并能够查看您之前阅读的页面，尽管没有连接到服务器。

将你的网站转变成一个进步的网络应用程序有三个基本步骤…

## 步骤 1:启用 HTTPS

pwa 需要 HTTPS 连接，其原因将很快变得显而易见。不同主机的价格和流程会有所不同，但考虑到谷歌搜索对安全网站的排名更高，这些成本和努力是值得的。

上面的演示不需要 HTTPS，因为 Chrome 允许使用 localhost 或任何 127.x.x.x 地址进行测试。如果您使用以下命令行标志启动 Chrome，也可以在 HTTP 站点上测试 PWA 技术:

*   `--user-data-dir`
*   `--unsafety-treat-insecure-origin-as-secure`

## 步骤 2:创建 Web 应用程序清单

web 应用程序清单提供了有关应用程序的信息，如名称、描述和图像，操作系统使用这些信息来配置主屏幕图标、启动页面和视口。本质上，清单是一个单一的文件，可以替代页面中已经存在的大量特定于供应商的图标和主题元标记。

清单是应用程序根目录下的一个 JSON 文本文件。它必须由一个`Content-Type: application/manifest+json`或`Content-Type: application/json` HTTP 头提供。该文件可以被命名为任何名称，但在演示代码中被命名为`/manifest.json`:

```
{
  "name"              : "PWA Website",
  "short_name"        : "PWA",
  "description"       : "An example PWA website",
  "start_url"         : "/",
  "display"           : "standalone",
  "orientation"       : "any",
  "background_color"  : "#ACE",
  "theme_color"       : "#ACE",
  "icons": [
    {
      "src"           :img/logo/logo072.png",
      "sizes"         : "72x72",
      "type"          : "image/png"
    },
    {
      "src"           :img/logo/logo152.png",
      "sizes"         : "152x152",
      "type"          : "image/png"
    },
    {
      "src"           :img/logo/logo192.png",
      "sizes"         : "192x192",
      "type"          : "image/png"
    },
    {
      "src"           :img/logo/logo256.png",
      "sizes"         : "256x256",
      "type"          : "image/png"
    },
    {
      "src"           :img/logo/logo512.png",
      "sizes"         : "512x512",
      "type"          : "image/png"
    }
  ]
} 
```

您所有页面的`<head>`中都需要一个指向此文件的链接:

```
<link rel="manifest" href="/manifest.json"> 
```

主要清单属性包括:

*   **name** :显示给用户的应用程序的全名
*   **short_name** :全名空间不足的情况下的简称
*   **描述**:应用程序的详细描述
*   **start_url** :启动应用程序的相对 url(通常为`/`)
*   **范围**:导航范围——例如，`/app/`的范围会将应用程序限制在该文件夹内
*   **background_color** :用于闪屏和浏览器 chrome(如果需要)的背景颜色
*   **theme_color** :应用程序的颜色，通常与背景颜色相同，这可以影响应用程序的显示方式
*   **方位**:首选方位——`any`、`natural`、`landscape`、`landscape-primary`、`landscape-secondary`、`portrait`、`portrait-primary`、`portrait-secondary`
*   **显示**:首选视图— `fullscreen`(无 chrome)、`standalone`(看起来像一个原生应用)、`minimal-ui`(一小组 UI 控件)和`browser`(一个传统的浏览器标签)
*   **图标**:定义`src` URL、`sizes`和`type`的图像对象数组(应该定义一个图标范围)。

MDN 提供了 [Web 应用清单属性](https://developer.mozilla.org/en-US/docs/Web/Manifest)的完整列表。

Chrome 的开发工具*应用*标签的*清单*部分验证你的清单 JSON 并提供一个“添加到主屏幕”链接，该链接在桌面设备上起作用:

![PWA manifest](img/1266599f77a04e40d1541d29c28d60c6.png)

## 步骤 3:创建服务人员

服务工作者是可编程的代理，可以拦截和响应网络请求。它们是驻留在应用程序根目录下的一个 JavaScript 文件。

您的页面 JavaScript(演示代码中的`/js/main.js`)可以检查服务工人支持并注册文件:

```
if ('serviceWorker' in navigator) {

  // register service worker
  navigator.serviceWorker.register('/service-worker.js');

} 
```

如果不需要离线功能，只需创建一个空的`/service-worker.js`文件。将提示用户安装您的应用程序！

服务人员可能会令人困惑，但是您应该能够根据自己的目的修改演示代码。这是一个标准的 web worker 脚本，浏览器下载(如果可能的话)并在一个单独的线程上运行。它不能访问 DOM 或其他页面 API，但是会拦截由页面更改、资产下载和 Ajax 调用触发的网络请求。

这是你的网站需要 HTTPS 的主要原因。想象一下，如果一个第三方脚本可以从另一个域注入它自己的服务工作者，会有多混乱。它将能够检查和修改客户端和服务器之间的所有数据交换！

服务人员对三个主要事件做出反应:`install`、`activate`和`fetch`。

### 安装事件

安装应用程序时会出现这种情况。它通常用于使用[缓存 API](https://developer.mozilla.org/en-US/docs/Web/API/Cache) 缓存重要文件。

首先，我们将为以下各项定义一些配置变量:

1.  缓存名称(`CACHE`)和版本(`version`)。您的应用程序可以有多个缓存存储，但我们只需要一个。应用了一个版本号，因此如果我们进行了重大更改，将使用一个新的缓存，并忽略所有以前缓存的文件。
2.  一个离线页面的 URL ( `offlineURL`)。这是一个当用户离线并试图加载一个他们以前没有访问过的页面时显示的页面。
3.  要安装的一系列基本文件，确保网站离线运行(`installFilesEssential`)。这应该包括 CSS 和 JavaScript 之类的资产，但是我也包括了主页(`/`)和徽标。如果 URL 可以用多种方式寻址，你还应该包括像`/`和`/index.html`这样的变体。注意`offlineURL`被添加到这个数组中。
4.  可选地，所需文件的数组(`installFilesDesirable`)。如果可能的话，这些将被下载，但不会使安装在失败时中止。

```
// configuration
const
  version = '1.0.0',
  CACHE = version + '::PWAsite',
  offlineURL = '/offline/',
  installFilesEssential = [
    '/',
    '/manifest.json',
    '/css/styles.css',
    '/js/main.js',
    '/js/offlinepage.js',
   img/logo/logo152.png'
  ].concat(offlineURL),
  installFilesDesirable = [
    '/favicon.ico',
   img/logo/logo016.png',
   img/hero/power-pv.jpg',
   img/hero/power-lo.jpg',
   img/hero/power-hi.jpg'
  ]; 
```

`installStaticFiles()`函数使用基于承诺的[缓存 API](https://developer.mozilla.org/en-US/docs/Web/API/Cache) 将文件添加到缓存中。只有当基本文件被缓存时，才会生成返回值:

```
// install static assets
function installStaticFiles() {

  return caches.open(CACHE)
    .then(cache => {

      // cache desirable files
      cache.addAll(installFilesDesirable);

      // cache essential files
      return cache.addAll(installFilesEssential);

    });

} 
```

最后，我们添加一个`install`事件监听器。`waitUntil`方法确保服务人员在执行完所有包含的代码之前不会安装。它运行`installStaticFiles()`,然后运行`self.skipWaiting()`,以激活服务人员:

```
// application installation
self.addEventListener('install', event => {

  console.log('service worker: install');

  // cache core files
  event.waitUntil(
    installStaticFiles()
    .then(() => self.skipWaiting())
  );

}); 
```

### 激活事件

这发生在服务人员被激活时，无论是在安装后立即激活还是在返回时激活。您可能不需要这个处理程序，但是演示代码使用它来删除旧的缓存(如果它们存在的话):

```
// clear old caches
function clearOldCaches() {

  return caches.keys()
    .then(keylist => {

      return Promise.all(
        keylist
          .filter(key => key !== CACHE)
          .map(key => caches.delete(key))
      );

    });

}

// application activated
self.addEventListener('activate', event => {

  console.log('service worker: activate');

    // delete old caches
  event.waitUntil(
    clearOldCaches()
    .then(() => self.clients.claim())
    );

}); 
```

注意，最后一个`self.clients.claim()`调用将这个服务工作者设置为站点的活动工作者。

### 获取事件

每当发出网络请求时，都会发生这种情况。它调用`respondWith()`方法劫持 GET 请求并返回:

1.  缓存中的资产。
2.  如果#1 失败，则使用[获取 API](https://developer.mozilla.org/en/docs/Web/API/Fetch_API) (与服务工作者获取事件无关)从网络加载资产。然后，该资产被添加到缓存中。
3.  如果#1 和#2 失败，则返回适当的响应。

```
// application fetch network data
self.addEventListener('fetch', event => {

  // abandon non-GET requests
  if (event.request.method !== 'GET') return;

  let url = event.request.url;

  event.respondWith(

    caches.open(CACHE)
      .then(cache => {

        return cache.match(event.request)
          .then(response => {

            if (response) {
              // return cached file
              console.log('cache fetch: ' + url);
              return response;
            }

            // make network request
            return fetch(event.request)
              .then(newreq => {

                console.log('network fetch: ' + url);
                if (newreq.ok) cache.put(event.request, newreq.clone());
                return newreq;

              })
              // app is offline
              .catch(() => offlineAsset(url));

          });

      })

  );

}); 
```

对`offlineAsset(url)`的最后一次调用使用几个助手函数返回适当的响应:

```
// is image URL?
let iExt = ['png', 'jpg', 'jpeg', 'gif', 'webp', 'bmp'].map(f => '.' + f);
function isImage(url) {

  return iExt.reduce((ret, ext) => ret || url.endsWith(ext), false);

}

// return offline asset
function offlineAsset(url) {

  if (isImage(url)) {

    // return image
    return new Response(
      '<svg role="img" viewBox="0 0 400 300" ><title>offline</title><path d="M0 0h400v300H0z" fill="#eee" /><text x="200" y="150" text-anchor="middle" dominant-baseline="middle" font-family="sans-serif" font-size="50" fill="#ccc">offline</text></svg>',
      { headers: {
        'Content-Type': 'image/svg+xml',
        'Cache-Control': 'no-store'
      }}
    );

  }
  else {

    // return page
    return caches.match(offlineURL);

  }

} 
```

`offlineAsset()`函数检查请求是否是针对图像的，并返回一个包含文本“offline”的 SVG。所有其他请求都返回`offlineURL`页面。

Chrome 的开发工具*应用*标签的*服务人员*部分提供了关于你的工作人员的信息，带有错误和强制重新加载并使浏览器离线的设施:

![PWA service workers](img/524d33154a56c8b6d57a67a87752d5ec.png)

*缓存存储*部分列出了当前范围内的所有缓存以及它们包含的缓存资产。更新缓存时，您可能需要单击刷新按钮:

![PWA cached files](img/1038a9d0140109f068361fe49fb501ce.png)

不出所料， *Clear storage* 部分可以删除您的服务人员和缓存:

![PWA clear cached files](img/697403911efcbbb52653b03415f24d03.png)

## 第四步:创建一个有用的离线页面

离线页面可以是静态 HTML，通知用户他们请求的页面在离线时不可用。然而，我们也可以提供一个可供阅读的页面 URL 列表。

可以在我们的`main.js`脚本中访问[缓存 API](https://developer.mozilla.org/en-US/docs/Web/API/Cache) 。然而，API 使用的承诺在不受支持的浏览器中失败，并将导致所有 JavaScript 停止执行。为了避免这种情况，我们将添加代码，在加载另一个`/js/offlinepage.js` JavaScript 文件(必须存在于上面的`installFilesEssential`数组中)之前，检查离线列表元素和缓存 API 是否可用:

```
// load script to populate offline page list
if (document.getElementById('cachedpagelist') && 'caches' in window) {
  var scr = document.createElement('script');
  scr.src = '/js/offlinepage.js';
  scr.async = 1;
  document.head.appendChild(scr);
} 
```

`/js/offlinepage.js`通过版本名称定位最近的缓存，获取所有 URL 键的列表，删除非页面 URL，对列表进行排序，并将其附加到 ID 为`cachedpagelist`的 DOM 节点:

```
// cache name
const
  CACHE = '::PWAsite',
  offlineURL = '/offline/',
  list = document.getElementById('cachedpagelist');

// fetch all caches
window.caches.keys()
  .then(cacheList => {

    // find caches by and order by most recent
    cacheList = cacheList
      .filter(cName => cName.includes(CACHE))
      .sort((a, b) => a - b);

    // open first cache
    caches.open(cacheList[0])
      .then(cache => {

        // fetch cached pages
        cache.keys()
          .then(reqList => {

            let frag = document.createDocumentFragment();

            reqList
              .map(req => req.url)
              .filter(req => (req.endsWith('/') || req.endsWith('.html')) && !req.endsWith(offlineURL))
              .sort()
              .forEach(req => {
                let
                  li = document.createElement('li'),
                  a = li.appendChild(document.createElement('a'));
                  a.setAttribute('href', req);
                  a.textContent = a.pathname;
                  frag.appendChild(li);
              });

            if (list) list.appendChild(frag);

          });

      })

  }); 
```

## 开发工具

如果你觉得 JavaScript 调试很难，服务人员就没什么乐趣了！Chrome 的开发者工具的*应用*选项卡提供了一组可靠的功能，日志记录语句也输出到控制台。

在开发过程中，你应该考虑在一个*匿名窗口*中运行你的应用程序，因为在你关闭标签后，缓存的文件不会被保留。

Firefox 提供了一个 JavaScript 调试器，可以从工具菜单的*服务人员*选项访问。更好的设施很快就会得到保证。

最后，Chrome 的 [Lighthouse 扩展也提供了关于 PWA 实现的有用信息。](https://chrome.google.com/webstore/detail/lighthouse/blipmdconlkpinefehnmjammfjpmpbjk)

## 抓到你了

渐进式网络应用需要新的技术，所以建议谨慎一些。也就是说，它们是对现有网站的增强，应该不会超过几个小时，而且对不受支持的浏览器没有负面影响。

开发人员意见不一，但有几点需要考虑…

### URL 隐藏

演示站点隐藏了地址栏，除非你有游戏之类的单网址应用，否则我不建议你这么做。清单选项`display: minimal-ui`或`display: browser`可能是大多数站点的最佳选择。

### 缓存霸主

你可以缓存网站上的每一个页面和资源。这对于小型网站来说没问题，但是对于那些有数千个页面的网站来说实用吗？没有人会对你所有的内容感兴趣，设备存储空间可能会被超过。即使像演示一样只存储访问过的页面和资产，缓存也可能会过度增长。

或许可以考虑:

*   仅缓存重要页面，如主页、联系人和最新文章
*   不缓存图像、视频和其他大文件
*   定期清除旧的缓存文件
*   提供一个“存储此页面以供脱机阅读”按钮，这样用户可以选择要缓存的内容。

### 缓存刷新

该演示在从网络加载之前在缓存中查找资产。这在用户离线时很好，但也意味着即使他们在线也可能会看到旧页面。

图像和视频等资产的 URL 应该永远不会改变，因此长期缓存很少是问题。您可以使用`Cache-Control` HTTP 头确保它们至少缓存一年(31，536，000 秒):

```
Cache-Control: max-age=31536000 
```

页面、CSS 和脚本文件可以更频繁地更改，因此您可以设置一个较短的 24 小时过期时间，并确保在联机时根据服务器版本进行验证:

```
Cache-Control: must-revalidate, max-age=86400 
```

您还可以考虑缓存破坏技术，以确保旧的资产不能被使用——例如，命名您的 CSS 文件`styles-abc123.css`并在每次发布时更改散列。

缓存可能会变得复杂，所以我建议你阅读杰克·阿奇博尔德的[缓存最佳实践&最大年龄陷阱](https://jakearchibald.com/2016/caching-best-practices/)。

## 有用的链接

如果您想了解更多关于渐进式 Web 应用程序的信息，以下资源会很有用:

*   [PWA.rocks 示例应用](https://pwa.rocks/)
*   [渐进式网络应用](https://developers.google.com/web/progressive-web-apps/)
*   [你的第一个 PWA](https://codelabs.developers.google.com/codelabs/your-first-pwapp/)
*   [Mozilla 服务人员食谱](https://serviceworke.rs/)
*   [MDN 使用服务人员](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API/Using_Service_Workers)

网上也有很多文章影响了我对这个演示代码的理解。请随意修改代码，并告诉我进展如何。祝你好运！

*这篇文章由 [AJ Latour](https://github.com/ajlatour) 、[Panayiotis pvgr Velisarakos](https://github.com/pvgr)和 [Dave Maxwell](https://github.com/davemaxwell77) 进行同行评审。感谢 SitePoint 的所有同行评审员使 SitePoint 的内容尽可能做到最好！*

## 分享这篇文章