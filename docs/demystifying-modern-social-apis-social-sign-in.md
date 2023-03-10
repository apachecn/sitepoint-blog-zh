# 揭开现代社交 API 的神秘面纱:社交登录

> 原文：<https://www.sitepoint.com/demystifying-modern-social-apis-social-sign-in/>

本文将让您深入了解我们许多人在 Web 项目中包含的专有 JavaScript 库的内部工作方式。在 Live Connect JavaScript API 和脸书 JavaScript SDK 中可以找到的社交分享按钮和联合身份验证只是你可能遇到的两个例子。

在本文中，您将了解 OAuth 2.0 用户认证方法，使用 XMLHttpRequest 2 进行跨源资源共享(CORS)和 REST。最后，我将演示一个工作应用程序，它允许用户在浏览器中连接和操作他们的 SkyDrive 照片。

## 入门指南

大约两年前，我被要求在一个网站上添加 Windows Live 和脸书连接按钮，就像图 1 中显示的两个按钮一样。

![Social Sign-in Buttons](img/2ea1c1aa40cf97dbe23f37b0fcb92aa9.png "Social Sign-in Buttons")
**图一。社交登录按钮**

将这些按钮添加到 Web 页面需要两个库，每个提供者一个，再加上一点 JavaScript 将它们连接起来。这两个库都有一些让它们工作的魔力，尽管我怀疑我写的 200 KB 的 JavaScript 是否都被使用了。在我被要求实现第三个服务之前，我打开了 Fiddler 并开始检查线路上发生了什么。经过一番探索，我找到了查阅文件的方法，在我意识到这一点之前，我已经具备了撰写一篇有见地文章的前提。所以，喝杯茶，吃块饼干，享受阅读吧。

## 术语汇编

当我们谈到将一个 Web 应用程序与其他 Web 服务连接起来时，首先让自己熟悉这些角色是很有用的。

应用程序(也称为客户端)是您使用的 Web 应用程序或网站。用户是使用你的应用的最终用户。提供商是您的应用将要连接到的 Web 服务，例如 Windows Live 或脸书。授权服务器是提供者的用户登录服务。

## 这些技术

两个常见的行业标准用于认证用户和安全地签署后续的 API 请求:OAuth 1.0 和 OAuth 2.0。这些底层技术的实现没有什么不同，但是提供者之间的 URL 和细微差别有所不同。因此，许多提供商都有自己的 JavaScript 库来支持他们的 API，如**表 1** 所示。

| **供应商** | **OAuth 版本** |
| Windows Live API | Two |
| 脸书图 | Two |
| 谷歌应用编程接口 | Two |
| 推特 | 1.0a |
| 美国 Yahoo 公司(提供互联网的信息检索服务) | 1.0a |
| 商务化人际关系网 | 1.0a |
| Dropbox | One |

**表 1。** **热门社交网站使用的 API 技术**

本文主要关注 OAuth 2.0，不要被它的名字所迷惑。OAuth 2.0 和 OAuth 1.0 是非常不同的协议。此外，OAuth 1.0 已经被许多支持 OAuth 2.0 的 Web 服务弃用。

## OAuth2:身份验证

以下是 OAuth.net 对 OAuth2 的描述:“一个开放的协议，允许以简单和标准的方式从 web、移动和桌面应用程序进行安全授权。。。。OAuth 是一种发布受保护数据并与之交互的简单方法。这也是人们授予您访问权限的一种更安全、更可靠的方式。我们保持简单，以节省您的时间。”

我认为 OAuth2 是一种认证机制，它让应用程序基于提供商的 Web 服务为用户获取一个*访问令牌*。然后，应用程序可以使用这个访问令牌代表用户查询或修改提供商的数据。

### 启动 OAuth 2

启动身份验证过程始于打开一个新的浏览器窗口，该窗口指向提供商网站上的一个特殊 URL。这里提示用户登录并同意与应用程序共享某些功能。这个过程如图 2 所示，其中提供者是 https://a.com，客户端是 https://b.com/.。查看地址栏中的 URL，在最后一个窗口中应该会看到 access_token。**图 3** 显示了 Windows Live 的登录窗口示例。在图中，应用程序 adodson.com 请求访问 SkyDrive 照片和文档。

![OAuth2 Flow](img/818797319dbba3a9d345cb42f0cf10bd.png "OAuth2 Flow")
**图二。** **OAuth2 流**

![OAuth 2 Consent Screen Hosted by Windows Live](img/729879eacf35bed3671892ea64e167c9.png "OAuth 2 Consent Screen Hosted by Windows Live")
**图三。** **由 Windows Live 托管的 OAuth 2 同意屏幕**

**图 3** 中的网址是:

```
https://oauth.live.com/authorize?client_id=00001111000&scope=wl.photos&response_type=
  token&redirect_uri=https://b.com/redirect.html
```

这个特殊的 URL 由授权页面的初始路径和四个必需的键值参数组成:

*   应用程序所有者注册应用程序时由提供商提供的 client_id。(在[https://manage.dev.live.com/](https://manage.dev.live.com/)注册您的 Windows Live。)
*   范围，这是一个逗号分隔的字符串列表，表示应用程序可以访问的服务。我在[https://adodson.com/hello.js/#ScopeandPermissions](https://adodson.com/hello.js/)为各种提供商维护了一个可能的范围列表。
*   response_type=token 属性，翻译过来就是“嘿，立即返回访问令牌。”
*   redirect_uri 属性，它是用户登录或取消后重定向窗口的地址。此 URL 必须与预配时的 client_id 属于同一来源。

还有一个可选的 state 参数，它是一个字符串，当包含它时，它只是在身份验证提供者的响应中返回。

### 接收访问令牌

用户通过身份验证并同意与应用程序共享后，浏览器窗口将被重定向到 redirect_uri 参数中定义的页面。例如:

```
https://adodson.com/graffiti/redirect.html#access_token=EwA4Aq1DBAAUlbRWyAJjK5w968Ru3Cyt
```

附加到 URL 位置哈希(#)的是一些凭据:

*   access_token 唯一的字符串，可用于查询提供者的 API。
*   expires _ in access _ token 有效的数字(秒)。
*   state 可以选择性地传递给 state 参数并返回的字符串。

使用 window.location 对象可以相对容易地读取凭证。例如，访问令牌可以如下提取:

```
var access_token =
	  (window.location.hash||window.location.search).match(/access_token=([^&amp;]+)/);
```

获得访问令牌后，下一步是使用它。

## OAuth2 历史记录

OAuth 2.0 是由微软和脸书的一些聪明人在 2010 年设计的，作为一种代表用户与其他应用程序安全共享数据服务的手段。除了 SSL 之外，它不需要依赖服务器或复杂的加密算法。

自成立以来，OAuth2 已经成为事实上的方法，第三方应用程序通过 Windows Live 或脸书验证其用户，然后利用这些庞大的数据仓库共享数据。该标准已经通过谷歌的服务、LinkedIn 和 SalesForce 扩散开来，Twitter 也在推特上表达了它的兴趣。如您所见，OAuth2.0 得到了高度认可。

## 原生应用

response_type=token 的另一个参数是 response_type=code。使用“code”会提示提供者返回一个短期授权码，而不是访问令牌。该代码与客户端密码(在注册应用程序时分配)一起使用，然后应用程序必须进行服务器到服务器的调用以获得访问令牌。这种方法绕过了对 redirect_uri 施加的域限制，但它确保它是同一个应用程序。因此，在使用无域的本机应用程序时，需要使用“代码”。使用服务器端认证流不同于本文中描述的纯客户端流，但它仍然是 OAuth2 的一部分。你可以在 IETF-OAuth2 上了解更多细节。

## 跨产地资源共享(CORS)

成功获得访问令牌的应用程序现在能够向提供者的 API 发出签名的 HTTP 请求。

从一个域访问另一个域的资源被称为跨源资源共享，或 CORS。这样做并不像从同一个域中访问内容那么简单。必须考虑遵守浏览器强加的[同源策略](https://en.wikipedia.org/wiki/Same_origin_policy)。这种策略对试图访问其当前浏览器窗口的域名和端口号之外的内容的脚本应用条件。如果不满足条件，浏览器将引发 SecurityError 异常。

### XHR2

JavaScript API 的新版本， [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/DOM/XMLHttpRequest/Using_XMLHttpRequest) 2 (XHR2)支持使用 CORS 的能力。启用该功能有两个部分:在客户端，请求必须使用 XHR2 接口，服务器必须用 Access-Control-Allow-Origin 头进行响应。

#### 客户端 JavaScript

以下代码说明了客户端中使用 XHR2 的 HTTP 请求:

```
var xhr = new XMLHttpRequest();
	xhr.onload = function(e){
	  // contains the data
	  console.log(xhr.response);
	};
	xhr.open('GET', “https://anotherdomain.com”);
	xhr.send( null );
```

#### 访问控制 HTTP 头

提供者用 Access-Control-Allow-Origin 头进行响应，满足用户浏览器中的安全策略。例如，指向 Windows Live API 的 HTTP 请求 URL 可能会创建以下 HTTP 请求和响应:

```
REQUEST
	GET https://apis.live.net/v5.0/me?access_token=EwA4Aq1DBAAUlbRWyAJjK5w968Ru3Cy
	...
	RESPONSE
	HTTP/1.1 200 OK
	Access-Control-Allow-Origin: *
	...
	{
	  "id": "ab56a3585e01b6db",
	  "name": "Drew Dodson",
	  "first_name": "Drew",
	  "last_name": "Dodson",
	  "link": "https://profile.live.com/cid-ab56a3585e01b6db/",
	  "gender": "male",
	  "locale": "en_GB",
	  "updated_time": "2012-11-05T07:11:20+0000"
	}
```

浏览器安全策略不会拒绝此 CORS 请求，因为提供商通过提供 HTTP 头 Access-Control-Allow-Origin: *，允许了此请求。星号(*)通配符表示允许来自任何 Web 应用程序的所有 HTTP 请求从该 Web 服务读取响应数据。

我看过的所有社交登录提供商——例如 Live Connect API 和脸书的 Graph API——当然都会在它们的响应中返回这个头。

## XHR 2 浏览器支持

并不是所有流行的浏览器都支持现在标准的带有 CORS 头的 XMLHttpRequest。但是都支持 JSONP！JSONP 只是通过嵌入式脚本标记的“src”属性调用 API 来解决跨域的安全问题。

如果 URL 中提供了“回调”参数，所有好的 API(如 SkyDrive API)都会用函数调用“填充”它们的 Javascript 对象响应。

首先，我们可以通过窥探新 XHR 接口的属性来进行特性检测，如下例所示。

```
If( “withCredentials” in new XMLHttpRequest() ){
	   // browser supports XHR2
	   // use the above method
	}
	else {
	   // Use JSONP, add an additional parameter to the URL saying return a callback
	   jQuery.getJSON(url + '&amp;callback=?', onsuccess);
	}
```

上面的代码依赖于 [jQuery 的 getJSON](https://api.jquery.com/jQuery.getJSON/) 方法作为后备，也做得很好。

## REST:表征状态转移

到目前为止，您已经了解了如何通过行业标准 OAuth2 对用户进行身份验证，以及如何使用 XMLHttpRequest 和访问控制头进行跨源资源共享。接下来，我将介绍什么是对 Web 上的服务器和数据集的基本访问和交互。

在前一节的代码中，您看到了一个简单的 HTTP 请求和响应。这与 HTML 页面及其资产的服务方式没有什么不同。然而，当在应用程序中执行以与 Web 服务互操作时，我们将这种机制称为表述性状态转移，或 REST。

要使用访问令牌签署 REST 请求，只需在查询字符串参数中包含该令牌，如下例所示:

```
https://apis.live.net/v5.0/me?access_token=EwA4Aq1DBAAUlbRWyAJjK5w968Ru3C
```

## 连接网络公司

现在，有了到目前为止涵盖的技术和术语，让我们通过一个应用程序的演示来验证所有这些理论。不久前，我创建了一个名为 Graffiti 的照片编辑应用程序(见**图 4** )。我觉得它是社交改头换面的完美竞争者，这样用户可以将他们的照片从 SkyDrive 加载到 canvas 元素上，并在浏览器中操作他们的在线照片。你可以在 https://adodson.com/graffiti/[的](https://adodson.com/graffiti/)看到演示，也可以在 https://github.com/MrSwitch/graffiti/[的](https://github.com/MrSwitch/graffiti/)查看代码。

在应用中，我重新创建了 SkyDrive JavaScript SDK 中的一些函数，如 WL.login、WL.filePicker 和 WL.api()。如果您不熟悉这些方法，请不要担心，因为我会边走边解释它们的作用。

![Graffiti App with Photos from a SkyDrive Album](img/8d0b2106e47456bc752a84f264990501.png "Graffiti App with Photos from a SkyDrive Album")
**图 4。** **带有 SkyDrive 相册照片的涂鸦应用**

基本上，新功能包括以下几项:

*   **getToken()** 验证用户并存储用户的访问令牌，以便与 SkyDrive 交互。这类似于 WL.login()函数。
*   **httpRequest()** 用于查询 SkyDrive API 并获取结果，以便可以构建导航，例如在**图 4** 中。这类似于 WL.api 和 WL.filePicker。

让我们更详细地看一下每一个。

### 认证

Graffiti 的身份验证过程旨在按需工作。当用户的操作需要签名的 API 请求时，身份验证过程就开始了。

在 SkyDrive API 中，身份验证处理程序是 WL.login。它应用于 Graffiti 应用程序的所有代码，并优先于任何 API 请求，就像它的对应物一样。您可以在这里看到一个典型的调用:

```
btn.onclick = function(){
	  getToken("wl.skydrive", function(token){
	    // … do stuff, make an API call with the token
	  });
	}
```

下面的代码中显示的 getToken 函数跟踪存储的令牌，并在需要授权时触发身份验证流。通过新的 [HTML5 localStorage 特性](https://msdn.microsoft.com/en-us/magazine/dn175490.aspx#the-localstorage-attribute)，接收到的令牌被持久化以供后续调用，该特性在现代浏览器中可用，并允许开发人员通过键值对来读写持久化信息(在本例中是我们的 auth-token 数据)。

最初不存在令牌，因此 window.authCallback 被分配给回调，并在访问令牌可用时被调用。window.open 方法为提供者的授权页面创建一个弹出窗口。将文本“WINDOWS_CLIENT_ID”替换为您的应用 ID。

```
function getToken(scope, callback){
	  // Do we already have credentials?
	  var token = localStorage.getItem("access_token"),
	    expires = localStorage.getItem("access_token_expires"),
	    scopes = localStorage.getItem("access_scopes") || '';
	  // Is this the first sign-in or has the token expired?
	  if(!(token&amp;&amp;(scopes.indexOf(scope)&gt;-1)&amp;&amp;expires&gt;((new Date()).getTime()/1000))){
	    // Save the callback for execution
	    window.authCallback = callback;
	    // else open the sign-in window
	    var win = window.open( 'https://oauth.live.com/authorize'+
	      '?client_id='+WINDOWS_CLIENT_ID+
	      '&amp;scope='+scope+
	      '&amp;state='+scope+
	      '&amp;response_type=token'+
	      '&amp;redirect_uri='+encodeURIComponent
	         (window.location.href.replace(//[^/]*?$/,'/redirect.html')),
	         'auth', 'width=500,height=550,resizeable') ;
	    return;
	  }
	  // otherwise let’s just execute the callback and return the current token.
	  callback(token);
	}
```

getTokenfunction 本身并不工作。用户同意后，弹出的浏览器窗口返回到 redirect.html 页面，路径中带有新的访问令牌。这个 HTML 文档如下面的代码所示。

```
<!DOCTYPE html>
	<script>
	  var access_token =
	    (window.location.hash||window.location.search).match(/access_token=([^&amp;]+)/);
	  var expires_in =
	    (window.location.hash||window.location.search).match(/expires_in=([^&amp;]+)/);
	  var state = (window.location.hash||window.location.search).match(/state=([^&amp;]+)/);
	  if(access_token){
	    // Save the first match
	    access_token = decodeURIComponent(access_token[1]);
	    expires_in = parseInt(expires_in[1],10) + ((new Date()).getTime()/1000);
	    state = state ? state[1] : null;
	    window.opener.saveToken( access_token, expires_in, state );
	    window.close();
	  }
	</script>
```

redirect.html 页面的完整网址包含访问令牌、状态和到期参数。redirect.html 页面中的脚本(如前所示)使用正则表达式从 window.location.hash 对象中提取参数，然后通过调用自定义函数 saveToken 将这些参数传递回父窗口对象(window.opener)。最后，这个脚本执行 window.close()来删除弹出窗口，因为不再需要它了。下面是 saveToken 的代码:

```
function saveToken(token, expires, state){
	  localStorage.setItem("access_token", token );
	  localStorage.setItem("access_token_expires", expires );
	  // Save the scopes
	  if((localStorage.getItem("access_scopes") || '').indexOf(state)===-1){
	  state += "," + localStorage.getItem("access_scopes") || '';
	  localStorage.setItem("access_scopes", state );
	  }
	  window.authCallback(token);
	}
```

saveToken 函数将 access_token 凭据存储在 localStorage 中。最后，触发保存在 window.authCallback 的回调。

很整洁，是吧？这段冗长的代码取代了 Live Connect JavaScript API 的 WL.login 函数。OAuth2 的流程一开始有点紧张和混乱，但是我认为一旦你看到它的运行，你会更好地欣赏它。

接下来，让我们重新创建查询 SkyDrive API 的方式。

### httpRequest:查询 SkyDrive

Graffiti 应用程序还要求用户能够查询 SkyDrive 并选择一个文件绘制到画布上。WL.filpicker 方法是 SkyDrive JavaScript API 的等效方法。然而，filePicker 是一个 UI 方法，而对 SkyDrive 的 REST 调用通常由 WL.api 方法处理。(**图 4** 举例说明了 Graffiti 的 filePicker-esq UI。)

我创建了两个函数来将 HTTP 请求过程与 UI 分开。在下面的代码中，函数 httpRequest 模拟 WL.api('get '，..)方法:

```
function httpRequest(url, callback){
	  // IE10, FF, Chrome
	  if('withCredentials' in new XMLHttpRequest()){
	    var r = new XMLHttpRequest();
	    // xhr.responseType = "json";
	    // is not supported in any of the vendors yet.
	    r.onload = function(e){
	      callback(JSON.parse(r.responseText});
	    }
	    r.open("GET", url);
	    r.send( null );
	  }
	  else{
	    // Else add the callback on to the URL
	    jsonp(url+"&amp;callback=?", callback);
	  }
	}
```

httpRequest 函数最初通过检测 XHR API 实例中是否存在 withCredentials 属性来测试 XHR2 是否存在。对于不支持 XHR2 跨源功能的浏览器，可以使用 JSONP(查看 [jQuery.getJSON](https://api.jquery.com/jQuery.getJSON/) )。

xhr.onload 处理程序将响应字符串转换为 JavaScript 对象，并将其作为第一个参数传递给回调处理程序。httpRequest 函数很容易启动。

```
httpRequest(“https://apis.live.net/v5.0/me?access_token=EwA4Aq1DBAAUlbRWyAJjK5w968Ru3Cy”,
	  callback);
```

调用 httpRequest 并随后将缩略图放在屏幕上的函数是 createAlbumView，就是这个方法重新创建了类似 WL.filePicker 的功能，例如:

```
createAlbumView("me/albums", "SkyDrive Albums");
```

以下是 createAlbumView 的代码:

```
function createAlbumView(path, name){
	  // Get access_token from OAuth2
	  getToken("wl.skydrive", function(token){
	    // Make httpRequest
	    // Retrieve all items from path defined in arguments
	    httpRequest('https://apis.live.net/v5.0/'+path+'?access_token='+token, function(r){
	      // Create container
	      // …
	      // Loop through the results
	      for(var i=0;i&lt;r.data.length;i++){
	        // Create thumbnail and insert into container
	        createThumbnail(r.data[i], container);
	      }
	    });
	  });
	}
```

当提供了一个相册的路径名(例如“me/albums”)时，createAlbumView 用在该地址找到的项目填充导航屏幕。虽然相册的初始列表可以在“我/相册”中找到，但 createAlbumView 是递归的。它找到的相册项目形成了新路径，因此使整个 SkyDrive 可导航。以下代码显示了该项如何公开其类型以及应用程序处理它的不同方式:

```
function thumbnail_click (item){
	  if( item.type === "photo" ){
	    applyRemoteDataUrlToCanvas( item.source );
	  }
	  else if(item.type === "album"){
	    createAlbumView(item.id+'/files', item.name);
	  }
	}
```

作为图像的项目直接返回到 Graffiti 的画布元素中。

## 注销

本文旨在揭开封装在专有 JavaScript 库中的魔法的神秘面纱。您已经看到了三个模仿 SkyDrive 的 JavaScript API 的函数。

*   getToken 模拟 WL.login
*   httpRequest 模拟 WL.api('get '，…)
*   createAlbumView 模拟 WL.filePicker()

使用 SkyDrive JavaScript SDK 只是一个例子。脸书连接 JavaScript SDK 和其他工作方式非常相似。也许现在你可以看到这些库是什么；采用的技术和巧妙技巧的集合。

这个故事还没有结束。还有更多利用 XMLHttpRequest 的方法。在第 2 部分中，我将通过扩展涂鸦应用程序来编辑相册、将涂鸦艺术作品上传到 SkyDrive 以及在用户的活动订阅源上共享信息来介绍和说明这些内容。大官！

在那之前，如果你想支持一个在网络上聚合许多社交 API 的项目，请看看[https://adodson.com/hello.js/](https://adodson.com/hello.js/)并在 GitHub 页面上分享你的想法。

感谢阅读。

## 参考

*   [涂鸦源代码](https://github.com/MrSwitch/graffiti)
*   [OAuth 2 简介](https://oauth.net/2/)
*   [Windows Live Connect API](https://msdn.microsoft.com/en-us/magazine/dn175490.aspx#standards)
*   [XMLHTTPRequest 对象](https://msdn.microsoft.com/en-us/library/ie/ms535874(v=vs.85).aspx)
*   [检测对 XHR2 的支持](https://stackoverflow.com/questions/1641507/detect-browser-support-for-cross-domain-xmlhttprequests)
*   [SkyDrive API](https://msdn.microsoft.com/en-us/library/live/hh826522.aspx)
*   [HelloJS 库](https://adodson.com/hello.js)

本文是来自 Internet Explorer 团队的 HTML5 技术系列的一部分。通过 3 个月的免费 BrowserStack 跨浏览器测试@ [尝试本文中的概念。即](https://modern.ie/)。

## 分享这篇文章