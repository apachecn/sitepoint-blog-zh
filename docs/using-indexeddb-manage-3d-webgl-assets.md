# 使用 IndexedDB 管理 3D WebGL 资产

> 原文：<https://www.sitepoint.com/using-indexeddb-manage-3d-webgl-assets/>

在这篇文章中，我想分享我在 3D WebGL Babylon 中开发对 IndexedDB 的**支持时所学到的一切。JS 游戏引擎**。事实上，从 1.4.x 开始，我们现在支持存储&加载包含我们的 3d 网格和它们的 JSON 场景。PNG 或者。来自 IndexedDB 的 JPG 纹理作为斑点。

这篇文章是围绕我自己在这个话题上的经历而写的。它是基于我解决在与 IDB 合作时遇到的各种问题的方式。然后你会发现一些关于你在使用 IndexedDB 时必须注意的事情的**解释&提示**。我还将分享我们在 3d WebGL 引擎中使用它的方式和原因。尽管如此，这篇文章可能对所有关注 IndexedDB 的人都有帮助。3d 游戏只是作为其用途的一个例子。

[![iNDEXEDdb2](img/244e13942d8294a8d996475f889ca062.png "iNDEXEDdb2")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/3515.iNDEXEDdb2_5F00_3F090A28.png)

*   [indexed db 简介](#1)
*   为什么在我们的游戏场景中使用 IndexedDB？
*   [了解 IndexedDB &的执行流程，处理异常](#3)
    *   [打开数据库](#3-1)
    *   [在所有浏览器中处理图像斑点存储](#3-2)
    *   [处理额度达到](#3-3)
*   [我学到的一些开发过程中使用的&技巧](#4)
    *   [如何在各种浏览器中清理/删除索引数据库](#4-1)
    *   你知道 in private/匿名浏览吗？
    *   [如何检查资源是否真的从数据库加载](#4-3)
    *   [IE、火狐& Chrome](#4-4) 的一些内部细节
*   我们如何在巴比伦使用它。JS

## 指数化介绍 b

IndexedDB 是一个使用键/值机制的非关系数据库。是 noSQL DB。你可以把它看作是由浏览器处理的第三代存储。第一个是 cookies，第二个是本地存储。

[![HTML5 Powered with Graphics, 3D & Effects, Performance & Integration, and Offline & Storage](img/0954e3d4017cb0e3fc31c1085ea638e2.png "HTML5 Powered with Graphics, 3D & Effects, Performance & Integration, and Offline & Storage")](https://www.w3.org/html/logo/)

这是一个 W3C 的规范，目前在 T2 的候选推荐中。它由大多数现代浏览器实现:IE10+，Chrome/Opera & Firefox。更好的是，自 IE10、Firefox 16 & Chrome 24/Opera 15 以来，该规范在无前缀版本中得到支持。看起来它已经可以投入生产使用了！这就是为什么我们今天在我们的网站上使用它:【https://www.babylonjs.com 

我不会涉及 IndexedDB 的基础知识，因为网上有这方面的好资源。然而，我已经花了很多时间来识别最新的文档和解释良好的教程。事实上，随着该规范的发展，您在网上找到的大多数文章都将被弃用。

如果你不想在这些过时的内容上浪费时间，下面是我推荐的 4 篇文章:

1—**W3C 规范**本身:【https://www.w3.org/TR/IndexedDB/】T2。它真的包含了一切，而且相对容易阅读。我经常阅读该规范，以真正理解它是如何解决我的一些问题的。有时，我们会忘记 W3C 规范可能是最好的文档。；-)
2—[与**雷蒙·卡姆登**的 IndexedDB](https://net.tutsplus.com/tutorials/javascript-ajax/working-with-indexeddb/) 一起工作。这是最近的，解释得很好的&非常适合初学者。我的文章可能是这篇文章的补充，因为我将把图像存储为 blob，这不是本文所讨论的。
3—[在我们的 **MSDN** 上索引 DB](https://msdn.microsoft.com/en-us/library/ie/hh673548(v=vs.85).aspx) 。它包含一些有趣的细节和一个大教程。
4—[在 **MDN** 上使用 IndexedDB](https://developer.mozilla.org/en-US/docs/IndexedDB/Using_IndexedDB) 。MDN 上一如既往的好文档。

因此，如果你对 IndexedDB 一无所知，请至少阅读第二个<sup>和</sup>链接。

之后，根据我的经验，让我分享你应该记住的最大警告:**真正理解 IndexedDB 是完全异步的，并且是基于事务的**。您需要等待异步读/写操作完成，也需要等待异步事务完成，然后才能确保代码中的一切正常。我将在下面用一些小图表来说明。

## 为什么在我们的游戏场景中使用 IndexedDB？

暑假的时候已经开始考虑用 IndexedDB 了。我在家里用我那令人难以置信的 2MB ADSL 线，每次我需要从我们的网站重新加载一个场景时，我都很沮丧。有些场景可能需要 5 分钟以上的时间来加载。我当时在想:“*既然我已经下载了所有的资源，为什么还要重新下载呢？*

你可能会说这是**浏览器缓存的工作。T** 没错。大多数时候，浏览器会完美地完成工作。但是在某些情况下，缓存将被**无效或删除**:缓存配额已经达到，用户正在删除他的网页内容缓存，或者仅仅是因为浏览器使用了启发式算法。你的游戏内容可能会因此受到影响，因为默认情况下，它将与从网络上下载的所有其他内容一起存在。

我想要更好的游戏体验。作为一名游戏玩家，我可以在游戏首次推出时下载资源。但是我不想浪费时间重新下载，因为我的浏览器决定清理一些缓存。当我在玩游戏时，我想马上玩。通过将游戏的数据隔离到 IndexedDB 中，我们很少会陷入各种缓存清理场景。然后我们获得了更大的独立性。

此外，我们最近在 BabylonJS 中发布了一个**增量加载器。这意味着场景将几乎立即加载，我们将根据摄像机当前的观察位置按需加载资源。这种方法的一个小问题是资源(网格几何图形&纹理)将首先从网络服务器下载并注入 3d 引擎。我们将受到网络延迟的影响。增量几何不会立即显示，而是在玩家移动相机几秒钟后突然出现。使用我们的 IndexedDB 方法，我们可以在后台预加载数据库中的资源，并通过增量加载器几乎立即加载它们。然后，我们将消除网络延迟问题。这仍然是我们需要努力的地方，但是我们现在已经具备了在未来版本中构建它的所有要素。**

最后，能够将资产存储在 **IndexedDB 中支持离线场景**。你现在可以想象一个游戏从网上加载，然后在没有任何连接的情况下完美运行！你只需要**结合 HTML5 应用缓存 API 和 IndexedDB** 。

为了说明这一点，请点击下图，浏览在线演示:

[![image](img/71587114c1b81e1510412e90ed436bad.png "image")](https://david.blob.core.windows.net/babylonjsoffline/index.html "BabylonJS IndexedDB + Offline demo")

加载“*心脏*场景，按下后退按钮，然后加载“*欧米茄粉碎机*场景。通过这样做，您将在 IndexedDB 中保存这两个场景。现在，尝试关闭网络适配器以脱机。你应该**能够导航到主页并启动两个场景，即使没有任何网络连接！**

我将在本文的最后一部分解释如何构建这样一个演示。

## 理解 IndexedDB 的执行工作流&处理异常

首先，请注意我为巴比伦写的所有代码。JS 在 GitHub 上有: [babylon.database.js](https://github.com/BabylonJS/Babylon.js/blob/master/Babylon/Tools/babylon.database.js) 。请随意看一看，以便更好地理解下面的解释。

此外，我的第一个建议是:**注册 W3C 规范**描述的所有可能的事件，并在开发过程中在其中放一些简单的 *console.log()* 来理解执行管道。

### 打开数据库

让我们首先回顾一下当您打开索引数据库时将会/可能会发生什么。

[![Drawing1](img/833ce001089e2a6312830d90f64665ed.png "Drawing1")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/0407.Drawing1_5F00_35D9FFE9.png)

我犯的第一个错误是认为*on upgradeneed*事件之后没有 *onsuccess* 事件。我相信只有当数据库已经存在并成功打开时， *onsuccess* 才会提高。因此，我将我的成功回调放在两个事件处理程序中。它逻辑上被触发了两次，但我期望它只被触发一次。总之，只在 *onsuccess* 事件处理程序中调用最终的回调函数。

此外，如果用户在被提示时拒绝访问数据库，您可以从*onupgraderended*转到 *onerror* 。例如，下面是 Internet Explorer 中显示的请求:

[![image](img/084d2be89d37230633bf9efb1580f511.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/2072.image_5F00_4991018C.png)

如果用户点击*不是这个站点*，你将陷入*错误*处理程序。

你可以通过阅读*巴比伦* *来检查我的代码。* *数据库* *。* *原型* *。**GitHub 上的[open async()](https://github.com/BabylonJS/Babylon.js/blob/master/Babylon/Tools/babylon.database.js)*函数。

### 在所有浏览器中处理图像斑点存储

为了更好的理解这一部分，你可以在 [GitHub](https://github.com/BabylonJS/Babylon.js/blob/master/Babylon/Tools/babylon.database.js) 上查看*Babylon . database . prototype . _ savimageintodbasync()*函数中包含的我的代码。

也请看看这篇文章:[把图像和文件存储在 IndexedDB](https://hacks.mozilla.org/2012/02/storing-images-and-files-in-indexeddb/) 中，作者是 Robert Nyman。这有点过时，但它很好地解释了如何将图像作为 blob 类型存储在 IDB 中。

我的函数的全局概念是将 3d 网格的纹理存储在 IndexedDB 中。为此，我首先使用 [XHR2](https://www.w3.org/TR/XMLHttpRequest2/) 下载它们，并请求**响应类型为 blob** 。然后，我基本上使用与上述文章相同的方法。

然而，在测试这种方法时，我发现 IE10+和 Firefox 很好地支持将图像作为 blob 存储在 IndexedDB 中，但 Chrome 还不支持。如果你试图在数据库中保存一个 blob 结构，Chrome 会产生一个 ***DataCloneError*** 。

覆盖 Chrome 的具体情况，而不做 UA 嗅探(这是不好的！)，我在保护拯救行动。如果失败，错误代码为 25，我知道 UA 不支持存储 blob。因为我已经通过 XHR 下载了数据，所以我只是用一个 *createObjectURL* 填充 HTML 图像元素。但是对于将来的调用，我将标志*isUASupportingBlobStorage*设置为 false，以表明在 IDB 中缓存图像对于该浏览器不可用。

我在考虑通过使用一些现有的使用文件系统 API 的 polyfills 或者通过用 base64 编码图像进行存储来更好地覆盖 Chrome 的情况。然后我发现这个 stackoverflow 线程在讨论同一个问题:[为离线 web 应用程序存储图像数据(客户端存储数据库)](https://stackoverflow.com/questions/14113278/storing-image-data-for-offline-web-application-client-side-storage-database)。但由于目前有一个 bug 已经打开，以便在 Chrome 的未来版本中实现这个功能:[问题 108012: IndexedDB 应该支持存储文件/Blob 对象](https://code.google.com/p/chromium/issues/detail?id=108012)，而且似乎很快就会发布，我决定让 Chrome 回到默认的图像缓存系统。

最后，您会注意到，在一般情况下，如果出现错误(XHR 错误或任何其他错误)，我将使用传统方式通过使用 HTML 图像元素及其 *src* 属性来加载图像。这样，我最大化了在保存过程中加载纹理的机会。

### 已达到处理限额

这一个值得一个小的模式来理解发生了什么！它将向您确认为什么理解 **IndexedDB 是基于交易的**很重要。

首先，让我们谈谈浏览器中的默认配额。默认情况下， **IE10+** 允许您在请求用户超过这个限制之前存储 10 MB。您可以在选项中更改该值。然后，它有一个每个域 250 MB 的最终最大限制，您不能更改此值。因此，我们在这里有 2 个可能的情况来达到配额，我们需要在我们的代码中处理它。

[![image](img/501b4ec722b32df73e3652707a387e60.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/7658.image_5F00_763C4C16.png)

当你达到第一个 **50 MB** 限额时，火狐会警告你，然后它就没有最大限额了。对于 **Chrome** ，答案就没那么简单了，但你可以在这里找到处理配额的方法:[https://developers . Google . com/Chrome/white papers/storage # temporary](https://developers.google.com/chrome/whitepapers/storage#temporary)

现在，为了理解如何正确处理配额，让我们回顾一个简单的案例。如果你浏览我们的网站:[https://www.babylonjs.com](https://www.babylonjs.com)，你会注意到有几个场景可供测试。其中一个叫做**平 2009** 。

[![image](img/8696a3012eb91cce99caddc7f6c0fa66.png "image")](https://www.babylonjs.com/)

这个场景有一个名为 *Flat2009.babylon* 的 **29 MB** 的 JSON 文件。场景文件当然是引擎下载的第一个文件。那么，当你第一次浏览我们的网站时，你可能会先尝试这个场景。到底会发生什么？

它将通过 XHR 请求加载 JSON 场景，并尝试将其保存到 IndexedDB 中。就拿 IE11 做浏览器吧。由于它具有默认的 10 MB 的**第一警告限制，仅通过下载这个独特的场景就已经达到该限制。我的第一个猜测是写请求操作应该失败，因为 29 MB > 10 MB。嗯，事情不是这样的。为了更好地理解，请查看下图:**

[![IndexedDB_Quota](img/5f4efcdf0c31dbc73b04ff1f81dbefed.png "IndexedDB_Quota")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/3716.IndexedDB_5F00_Quota_5F00_635493E7.png)

第一行代码创建了事务。从这个事务中，我们启动了 write **请求**，把新下载的场景放到“*场景*商店中。事实上，名为“ *addRequest* 的请求将首先成功。事实上，从逻辑上讲，您的浏览器应该能够将 29 MB 的场景写入数据库。但是当达到限额时，浏览器会提示用户询问他是否允许浏览器超过默认限额。如果用户拒绝，事务将被中止，文件将从数据库中删除。

还是那句话，结论和之前一样。**您的最终成功处理程序必须从事务**的 *oncomplete* 处理程序中调用，而不是从请求的 *onsuccess* 处理程序中调用。

你可以通过阅读 [GitHub](https://github.com/BabylonJS/Babylon.js/blob/master/Babylon/Tools/babylon.database.js) 上***Babylon . database . prototype . _ saveseneintodbasync()***的代码来回顾这个逻辑。最重要的部分在这里:

```
// Open a transaction to the database var transaction = that.db.transaction(["scenes"], "readwrite"); // the transaction could abort because of a QuotaExceededError error transaction.onabort = function (event) { try { if (event.srcElement.error.name === "QuotaExceededError") {
           that.hasReachedQuota = true;
       }
   } catch (ex) { }
   callback(sceneText);
;
transaction.oncomplete = function (event) {
   callback(sceneText);
;
```

您需要测试“***”quotaexceederror***”，以确保交易因配额而中止。在我的例子中，我设置了一个标志*已经达到配额*，因为没有必要在数据库中尝试进一步的写操作，这将不再起作用。

## 我在开发过程中学到和使用的一些技巧

让我在这里分享一些我在开发过程中使用的技巧，可能对你也有用。

### 如何在各种浏览器中清理/删除索引数据库

您可能需要删除测试过程中创建的数据库，以便从零开始重新启动。

#### 微软公司出品的 web 浏览器

进入"*互联网选项*"–>"*设置*"–>"*缓存和数据库*，选择您想要删除的域名。

[![image](img/3db68fbe05ec378b56b199b18792b5a3.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/8512.image_5F00_1A458DCB.png)

#### 铬

导航到 *chrome://settings* ，进入*高级设置*。点击*清除浏览数据…* 按钮。最后，点击*清除浏览数据*按钮，如下图:

[![image](img/16bcd565a764860bc53fa44fd03870eb.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/0564.image_5F00_2741FE8D.png)

或者你可以直接在这里删除与你的域名关联的文件夹:*% AppData % \ Local \ Google \ Chrome \ User Data \ Default \ indexed db*

#### 火狐浏览器

你需要进入这个文件夹:*% AppData % \ Roaming \ Mozilla \ Firefox \ Profiles \ your profile id \ indexed db*，删除与你的域名关联的文件夹。

### 你知道 in private/匿名浏览吗？

如果你使用浏览器的 InPrivate 或匿名模式浏览你的网站，IndexedDB 将被禁用(顺便说一下，就像 localStorage & cookies 一样)。 **window.indexedDB 将未定义**。这对于在有/没有 IndexedDB 的情况下进行一些测试可能是有用的。例如，对我来说，在不启用 IndexedDB 的情况下测试一个支持 WebGL 的浏览器是很有用的。

### 如何检查资源是否真的是从数据库加载的

在测试期间，我总是想知道我的数据库逻辑是否工作正常，资源是否真的是从我的数据库加载的，而不是直接从 web 上加载的。我发现了一个非常简单的方法来检查:使用 IE11 的 F12 开发栏。自己测试一下:

–使用 IE11，导航至[https://www.babylonjs.com](https://www.babylonjs.com)

–按 F12 并选择“ ***网络*** ”面板，按下“ ***总是从服务器刷新*** ”按钮。我们现在要求浏览器绕过他的缓存，总是试图从 web 服务器下载资产。现在按下 ***播放*** 按钮开始捕捉:

[![image](img/770034352d10beecca559163322ef41f.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/6281.image_5F00_4FBC13DA.png)

–尝试加载“ ***心*** ”场景。第一次，您应该看到这样的痕迹:

[![image](img/2cb60e2b459a53686aa15469e1ee91e2.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/7870.image_5F00_3F463390.png)

使用 XHR 请求下载项目。

–返回主页，重新加载完全相同的场景。您现在应该只看到一个 HTTP 请求发出:

[![image](img/9107a19f7d61c02efe9781b1516aa2f6.png "image")](https://blogs.msdn.com/cfs-file.ashx/__key/communityserver-blogs-components-weblogfiles/00-00-01-10-46-metablogapi/1563.image_5F00_3BEEDEAC.png)

发送唯一的 XHR 请求来检查清单文件。我们现在可以肯定，其他的一切都来自我们的本地索引数据库。

### IE、Firefox 和 Chrome 的一些内部细节

最后一个技巧:我发现 Aaron Powell 的这篇文章读起来非常有趣:[浏览器如何存储索引数据库数据](https://www.aaron-powell.com/posts/2012-10-05-indexeddb-storage.html)。你会了解到 IE 用的是 ESE ( [可扩展存储引擎](https://en.wikipedia.org/wiki/Extensible_Storage_Engine))实现 IndexedDB，Firefox 用的是 [SQLite](https://www.sqlite.org/) & Chrome 用的是 [LevelDB](https://code.google.com/p/leveldb/) 。

在同一篇文章中，我还知道了火狐和 Chrome 的数据库被藏在哪里。

## 我们如何在巴比伦使用它。射流研究…

我们的主要目标是**让它在我们的游戏引擎**中使用起来非常简单，并尽可能少地影响其余的代码。我的任务是将我的逻辑注入到两个加载函数中，加载纹理和 JSON 场景文件。

如果你想知道如何使用 Babylon 来启用对 IndexedDB 的支持。JS，从阅读我在维基上写的教程开始:[https://github . com/BabylonJS/Babylon . JS/wiki/Caching-the-resources-in-indexed db](https://github.com/BabylonJS/Babylon.js/wiki/Caching-the-resources-in-IndexedDB "https://github.com/BabylonJS/Babylon.js/wiki/Caching-the-resources-in-IndexedDB")

使用非常简单。加一个**。清单文件**到您的。巴比伦场景，并指出版本号的资产，如果你想缓存场景，纹理或两者兼而有之。

我已经做了**吨的单元测试**来确保我的代码覆盖了所有可能的情况。事实上，因为我是第一个被调用来处理资产的人，所以如果我的代码失败，什么都不会显示或呈现。**处理 I/O 一直是一个关键部分**。

在我们的网站上，大多数场景都被配置为离线使用场景及其纹理:[www.babylonjs.com](https://www.babylonjs.com/)。比如，你可以试试“*心*的场景。场景被描述成 *heart.babylon* 并且相关联的清单文件则是 *heart.babylon.manifest* 。其中一个场景被配置为只缓存纹理。这是汽车场景。这是因为 JSON 文件 *TheCar.babylon* 超过 93 MB。IE11 和 Chrome 无法将这样大小的文件存储到数据库中。然后，我决定避免试图缓存它。

最后，使用 Babylon 构建一个完全离线的功能演示。JS 喜欢这个:[巴比伦。JS 离线演示](https://david.blob.core.windows.net/babylonjsoffline/index.html "https://david.blob.core.windows.net/babylonjsoffline/index.html")，你需要把我们的数据库逻辑耦合到 HTML5 应用缓存 API。我已经在这里介绍了它在 2d 画布游戏中的用法:[使你的 HTML5 画布游戏现代化第 2 部分:离线 API，拖放&文件 API](https://blogs.msdn.com/b/davrous/archive/2012/04/17/modernizing-your-html5-canvas-games-part-2-offline-api-drag-n-drop-amp-file-api.aspx "https://blogs.msdn.com/b/davrous/archive/2012/04/17/modernizing-your-html5-canvas-games-part-2-offline-api-drag-n-drop-amp-file-api.aspx")

该方法与 3d WebGL 游戏完全相同。在本例中，我在 HTML5 清单文件中放入了缩小版的 Babylon。JS 加上主页上使用的一些图片。更重要的是:我还在其中包含了. babylon.manifest 文件。我终于获得了这个简单的小型缓存清单文件，名为 ***babylon.cache*** :

```
CACHE MANIFEST
 Version 1.1
CACHE: 
abylon.js
and.minified-1.1.1.js
ndex.html
ndex.css
ndex.js
creenshots/heart.jpg
creenshots/omegacrusher.jpg
ssets/BandeauEmbleme.png
ssets/Bandeauhaut.png
ssets/BtnAbout.png
ssets/BtnDownload.png
ssets/gradient.png
ssets/Logo.png
ssets/SpotDown.png
ssets/SpotLast.png
cenes/Heart/Heart.babylon.manifest
cenes/SpaceDek/SpaceDek.babylon.manifest
NETWORK: 
```

的确，如果你不把。 *babylon.manifest* 文件加载到缓存清单中，当引擎试图检查它们的值时，将引发 404 错误。默认情况下巴比伦。JS 假设这意味着您想要从 web 下载资产。

最后，由于我们的方法，现在想象一下这个[巴比伦。JS 离线演示](https://david.blob.core.windows.net/babylonjsoffline/index.html "https://david.blob.core.windows.net/babylonjsoffline/index.html")代表你的 3d 游戏的主菜单，也就是说每个场景都是你游戏的一个特定关卡。如果你想只更新其中一个等级，你只需要改变包含在相关的 *.babylon.manifest* 文件中的版本。**我们的 3d 游戏引擎将只更新数据库中的这个特定关卡**。这是只使用 HTML5 应用缓存 API 做不到的。**有了 AppCache，就没有增量更新**。您被迫重新下载缓存清单文件中指定的所有内容。这将意味着更新你的游戏的一个级别将意味着从网页上重新安装游戏到 HTML5 缓存中。

我希望我们的方法和技巧能激励你们中的一些人在网络上更好地使用 IndexedDB！欢迎在评论中分享你的反馈。

*最初发布:[https://blogs . msdn . com/b/dav rous/archive/2013/09/24/using-indexed db-to-handle-your-3d-web GL-assets-sharing-feedbacks-amp-tips-of-Babylon-js . aspx](https://blogs.msdn.com/b/davrous/archive/2013/09/24/using-indexeddb-to-handle-your-3d-webgl-assets-sharing-feedbacks-amp-tips-of-babylon-js.aspx)。经作者许可，在此转载。*

## 分享这篇文章