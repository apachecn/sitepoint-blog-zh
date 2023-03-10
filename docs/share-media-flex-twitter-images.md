# 使用 Flex 在 Twitter 上共享媒体，第二部分:图像

> 原文：<https://www.sitepoint.com/share-media-flex-twitter-images/>

**在本系列关于使用 Flex 在 Twitter 上共享媒体的第一部分[中](https://www.sitepoint.com/article/share-media-flex-twitter/)，我们讨论了如何使用 Flash Catalyst 的测试版为 Flex 应用程序创建接口。然后，我们使用 Flash Builder 4 的测试版为浏览器创建了一个 Twitter 应用程序，它使用 PHP 来代理对 Twitter API 的调用。在本文中，我们将通过添加上传照片到流行的 Flickr 图片托管服务的功能来增强 Flex 应用程序，然后将照片的简短链接集成到 Twitter 帖子中。**

当你完成教程后， **[通过参加我们的文章测试](https://www.sitepoint.com/quiz/adobe/share-media-flex-twitter-images/)来测试你的知识！**

##### 上传过程

在我们构建的应用程序中，上传照片和缩短链接可以通过多种方式完成。我们必须首先上传图像，检索它的链接，然后将该链接提交给 URL 缩短服务。如果我们将整个过程自动化，让它在用户发布他们的推文时就开始工作，我们就冒着长时间延迟的风险；这是因为它需要对不同的服务器进行三次调用。相反，我们选择了以下流程:

1.  用户上传照片

3.  照片的链接被插入到发布表单中推文的末尾

5.  然后，用户发送消息

这篇文章的所有代码都包含在新文件`cheepcheep_image_flashbuilder.fxp`中，你可以[从](https://www.sitepoint.com/examples/flextwitter-part2/flextwitter_images.zip)下载它以及来自[上一篇文章](https://www.sitepoint.com/article/share-media-flex-twitter/)的所有资源。

##### 浏览文件

ActionScript `FileReference`类让我们能够从 Flash 应用程序上传和下载文件。它的`browse`方法使用操作系统对话框定位文件；每个`FileReference`实例将引用用户系统中的一个文件。确保将`net`的名称空间添加到`Application`标签中:

```
<s:Application ... 
  xmlns:net="flash.net.*"/>
```

在应用程序中已经存在的`<fx:Declarations></fx:Declarations>`标记之间创建一个`FileReference`实例:

```
<net:FileReference id="fileReference" select="fileSelected(event)" complete="fileAccessed(event)" />
```

我们已经为它的`select`和`complete`事件设置了函数调用，稍后我们将添加它们。在应用程序底部推文的`TextInput`字段下，我们添加了一个按钮；这使我们能够浏览要上传的文件，我们将调用函数`browsePhoto`，如下所示:

```
private function browsePhoto(evt:MouseEvent):void { 
  var arr:Array = []; 
  arr.push(new FileFilter("Images", ".gif;*.jpeg;*.jpg;*.png")); 
  fileReference.browse(arr);         
}
```

这个函数创建一个数组，并用一个`FileFilter`对象填充它，以限制用户可以选择的文件扩展名。然后它调用`FileReference`实例的 browse 方法，将数组作为参数传递。这将启动一个操作系统对话框，打开最近浏览的目录。一旦用户选择了一个文件，`select`事件的函数就会被触发。它加载文件，这又会触发`complete`事件，在我们的应用程序中向用户显示文件名。它在我们添加到浏览按钮旁边的一个新的`Label`组件中。如果需要，您可以使用此功能显示图像预览:

`private function fileSelected(evt:Event):void {
 fileReference.load();
}`

`private function fileAccessed(evt:Event):void {
 fileName.text = "File selected: " + fileReference.name;
}`

##### 向 Flickr 认证

有很多免费的在线照片托管服务。我们选择了 Flickr，因为他们有一个记录良好的 API，并且已经有了第三方的`ActionScript`库。Flickr 确实要求在应用程序可以使用其服务之前进行应用程序身份验证和用户许可。您需要的第一件东西是应用程序的 API 密匙，可以免费获得；该密钥还将有一个匹配的“秘密”用于认证。点击此处了解更多信息:

*   Flickr API:[http://www.flickr.com/services/api/](http://www.flickr.com/services/api/)
*   Flickr API 键:[http://www.flickr.com/services/api/misc.api_keys.html](http://www.flickr.com/services/api/misc.api_keys.html)

ActionScript 3 Flickr 库可以在:[http://code.google.com/p/as3flickrlib/](http://code.google.com/p/as3flickrlib/)找到。该库的最后一个版本没有上传到 Flickr 的方法，无论是在该库还是在 SWC。虽然在项目的代码库中有一个上传方法的版本，但我们在让它工作时发现了一些问题。我们已经为 Flash Builder 项目提供了该库的副本，该项目有一个有效的上传方法；到目前为止，这是图书馆中我们唯一遇到问题的部分。Flickr 库使用来自 http://code.google.com/p/as3corelib/[的额外 MD5 和网络工具，你也需要下载，我们已经在 Flash Builder 项目中包含了 SWC。](http://code.google.com/p/as3corelib/)

Flickr 的用户身份验证过程确实包含一些编程步骤，但是应该足够简单。最终，您需要在 Flickr 上为应用程序的每个用户生成一个令牌，并在每次调用 Flickr 时将该令牌存储在用户的计算机上。此令牌是用户凭据的安全表示形式；我们可以使用它来执行上传等任务，而不必知道这些凭证。当用户在 Flickr 上向您的应用程序授予权限时，会生成令牌。

我们将使用 ActionScript `SharedObject`在本地存储授权令牌，然后在我们浏览文件时测试它是否存在。如果应用程序不存在，我们将要求用户对其进行身份验证，并在检索时写入令牌和用户帐户 ID。检查下面的代码，您会看到我们正在从 Flickr 库导入必要的类，创建一个`SharedObject`实例和变量来保存我们将在认证过程中使用的多个值:

```
import com.adobe.webapis.flickr.events.FlickrResultEvent;  
import com.adobe.webapis.flickr.FlickrService;  
import com.adobe.webapis.flickr.methodgroups.Auth;  
import com.adobe.webapis.flickr.methodgroups.Upload;  

private var appSO:SharedObject;  

private var flickr:FlickrService;  
private var flickrApiKey:String = "yourFlickrApiKey";  
private var flickrSecret:String = "yourFlickrApiKeySecret";  
private var flickrFrob:String = "";  
private var flickrAuthToken:String = "";  
private var flickrNsid:String = "";  
private var authorizationURL:String = "";
```

在下面的`getFlickrLoginCredentials`函数中，我们试图读取应用程序的`SharedObject`，如果它不存在，就创建它。然后，我们测试 Flickr 授权令牌是否已经存储。如果它不存在，我们创建一个`FlickrService`的实例，用 API 键初始化它，添加秘密，分配一个处理程序，并调用一个`getFrob`方法。这将调用 Flickr 来检索获取授权令牌所需的“frob”——在用户连接到 Flickr 并授予对我们的应用程序的访问权限之后:

```
public function getFlickrLoginCredentials():void {  
  appSO = SharedObject.getLocal("TestFlickrTwitter");  
  if ( appSO.data.flickrAuthToken == null ) {  
    flickr = new FlickrService(flickrApiKey);  
    flickr.secret = flickrSecret;  
    flickr.addEventListener( FlickrResultEvent.AUTH_GET_FROB, onGetFrob );  
    flickr.auth.getFrob();  
  } else {  
    flickrAuthToken = appSO.data.flickrAuthToken;  
    flickrNsid = appSO.data.flickrNsid;  
    browsePhoto();  
  }  
}
```

`getFlickrLoginCredentials`函数的另一半是一个`else`子句；它从`SharedObject`中读取 Flickr 授权令牌和 Flickr 用户 id，并调用我们之前描述的`browsePhoto`方法。我们已经将浏览照片按钮改为现在调用`getFlickrLoginCredentials`,这样如果用户试图上传照片，我们将对用户进行身份验证。

下面是`onGetFrob`函数，当我们从上面函数中的`getFrob`调用接收到一个结果时，就会调用这个结果处理程序。这将“frob”存储为一个局部变量，并创建稍后需要的授权 URL:

```
private function onGetFrob( evt:FlickrResultEvent ):void {  
  flickrFrob = evt.data.frob;  
  authorizationURL = flickr.getLoginURL(flickrFrob, "write");  
  currentState = "flickrAuthorise";  
}
```

上面的函数调用了我们为名为`flickrAuthorise`的应用程序创建的附加状态。我们将使用它来显示我们在 Flash Catalyst 中构建的新组件；该组件是一个弹出窗口，包含一条给用户的消息，说明他们需要连接到 Flickr 来授权应用程序。该组件还包含两个按钮:第一个按钮打开一个新的浏览器窗口并连接到 Flickr 授权页面，第二个按钮从 Flickr 获取授权令牌。

您会注意到应用程序中列出的新状态:

```
<s:states>  
  <s:State name="twitterLogin"/>  
  <s:State name="twitterDisplay"/>  
  <s:State name="flickrAuthorise"/>  
</s:states>
```

看看应用程序中的可视对象，您会看到用于在几种状态下控制它们的各种属性。以`browsePhotoBtn`为例；它使用`includeIn`属性来指定它将出现在哪个州。还设置了一个额外的属性来改变按钮在`flickrAuthorise`状态下的 alpha，这样当授权弹出窗口出现时这个属性会变暗。大多数其他可视对象都具有相同的属性以获得相同的效果:

```
<s:Button x="180" y="670" label="Browse for Photo" click="getFlickrLoginCredentials()" id="browsePhotoBtn" includeIn="flickrAuthorise,twitterDisplay" alpha.flickrAuthorise="0.4"/>
```

单击授权组件中的第一个按钮`CustomComponent2`，调用主应用程序中的函数`authoriseFlickr`。这将启动一个新的浏览器窗口，调用在`onGetFrob`函数中创建的授权 URL。它还启用了授权组件中的第二个按钮:

```
public function authoriseFlickr():void {  
  var urlRequest:URLRequest = new URLRequest(authorizationURL);  
  navigateToURL(urlRequest, "_blank");  
  customcomponent21.button3.enabled = true;  
}
```

然后，用户必须在 Flickr 网站上通过几个步骤来授权应用程序。他们将看到的最后一个屏幕会告诉他们可以关闭浏览器窗口；然后，他们应该单击授权组件上的第二个按钮，这将调用主应用程序中的`onGetTockenClick`函数。这又调用了`FlickrService`的`getToken`方法。在`onGetTockenClick`函数中指定了一个处理函数`onGetToken`。`onGetToken`将从 Flickr 接收一个对象，该对象包含授权令牌和关于用户的一些细节；我们使用该函数在本地存储令牌和用户 id，也存储在`SharedObject`中。我们也切换回主`twitterDisplay`状态:

```
public function onGetTokenClick():void {  
  flickr.addEventListener(FlickrResultEvent.AUTH_GET_TOKEN, onGetToken);  
  flickr.auth.getToken(flickrFrob);          
}  

private function onGetToken(evt:FlickrResultEvent):void {  
  flickrAuthToken = evt.data.auth.token;  
  flickrNsid = evt.data.auth.user.nsid;  
  appSO.data.flickrAuthToken = flickrAuthToken;  
  appSO.data.flickrNsid = flickrNsid;  
  appSO.flush();  
  currentState = "twitterDisplay";  
}
```

这将在用户第一次尝试浏览照片时发生，因此他们需要在认证后再次浏览，因为此步骤被此过程中断。一旦我们找到我们的照片，我们需要上传它，以便我们可以在我们的推文中包含它的链接。

##### 上传照片

Flickr 库中经过调整的 upload 类确保了必要的身份验证、上传过程和上传完成事件都得到处理。我们在 Twitter post 表单下方的应用程序底部添加了一个按钮来上传图片，并为这个按钮创建了一个名为`uploadFlickr`的函数。该函数为 upload complete 事件创建一个监听器，并创建另一个`FlickrService`类的实例，向其添加凭证。然后这被用作上传类实例的参数，该类用于上传我们的`fileReference`，它是在我们浏览文件时创建的。我们还添加了对`CursorManager`的`setBusyCursor`方法的调用，以便在上传过程中向用户提供简单的反馈形式——因为`FlickrService`没有这个特性:

```
private function uploadFlickr():void {   
  fileReference.addEventListener(DataEvent.UPLOAD_COMPLETE_DATA,uploadCompleteHandler);           
  flickr = new FlickrService(flickrApiKey);   
  flickr.secret = flickrSecret;   
  flickr.token = flickrAuthToken;   
  var uploader:Upload = new Upload(flickr);   
  uploader.upload(fileReference);   
  CursorManager.setBusyCursor();   
}
```

上传将从 Flickr 返回一个 XML 包，其中包含上传图像的 ID，我们可以利用它来构建一个链接，以便在我们的 tweet 中使用。由于 Twitter 有 140 个字符的限制，我们需要解决这个问题，因为 Flickr 的 URL 往往有点长。我们将通过使用 URL 缩短服务 [bit.ly](http://bit.ly) 来解决这个问题，它有一个 REST API，我们可以在我们的应用程序中使用。您需要使用 bit.ly 创建一个帐户来接收 API 密钥；这是帐户自动发行的，您的密钥在您的帐户详情页面上。

##### 创建到图像的缩短链接

`uploadCompleteHandler`被指定为`uploadFlickr`中的结果处理器，并将接收事件的数据属性中的 XML。我们在函数中将它转换成一个 XML 对象，并构造一个包含字符串、Flickr 用户 ID 和照片 ID 的 URL。我们还创建了变量来存储 bit.ly 登录和 API 密钥，这两者都是 rest 调用所必需的:

```
[Bindable] private var photoUrl:String = "";   
private var bitlyLogin:String = "yourBitlyAccountName";   
private var bitlyApiKey:String = "yourBitlyApiKey";   

private function uploadCompleteHandler(evt:DataEvent):void {   
  CursorManager.removeBusyCursor();   
  var xData:XML = new XML(evt.data);   
  photoUrl = "http://www.flickr.com/photos/"+flickrNsid+"/"+xData.photoid;   
  bitlyService.send();   
}
```

一旦构建了图像的地址，`uploadCompleteHandler`函数就调用`HTTPService`的`send`方法。记住`HTTPService`标签需要嵌套在`<fx:Declarations>`标签集中。`HTTPService`标签的`url`属性需要向 bit.ly 传递许多参数，所有这些参数都绑定到应用程序变量。我们已经为`HTTPService`标签的`result`事件设置了一个处理程序:

```
<mx:HTTPService id="bitlyService" method="GET" url="http://api.bit.ly/shorten?version=2.0.1&amp;longUrl={photoUrl}&amp;login={bitlyLogin}&amp;apiKey={bitlyApiKey}&amp;format=xml" result="bitlyService_resultHandler(event)"/>
```

我们允许 Flash Builder 创建一个默认的处理程序来处理 bit.ly 结果；缩短的 URL 包含在服务返回的 XML 包中，它为我们转换成 ActionScript 对象。该函数计算 tweet 的`TextInput`字段中当前文本的长度，或者追加缩短的 URL，或者截断文本，然后追加 URL:

```
protected function bitlyService_resultHandler(evt:ResultEvent):void   
{   
  var bitlyURL:String = evt.result.bitly.results.nodeKeyVal.shortUrl;   
  var combineTextURL:String = textinput1.text + " " + bitlyURL;   
  if ( combineTextURL.length < 140) {   
    textinput1.text = combineTextURL;   
  } else {    
    var excessChars:Number = 140 - combineTextURL.length;   
    textinput1.text = textinput1.text.substr(0,(textinput1.text.length)-Math.abs(excessChars)) + " " + bitlyURL;   
 }   
}
```

上传了图片并为其创建了一个简短的链接，我们现在准备完成我们的推文并将其发布到 Twitter 上。

从 Flash 应用程序中直接上传是 Flash Player 9 引入的一个经常被请求的功能。将图片与文本连接起来是对 Twitter 的一个巨大增强，我们相信你会希望利用照片共享服务的流行，作为你的应用程序所提供的社交体验的一部分。Flickr API 文档对门外汉来说有点吓人，尤其是认证过程。幸运的是，开发人员可以使用 ActionScript 库来简化这个过程。

很可能还有其他方法来对本文中描述的应用程序流程进行排序。Twitter 不是一站式商店，他们的服务是为发布最少的短信而建立的。我们依靠三个服务调用来生成我们的单个 Twitter 帖子；挑战在于以一种用户容易的方式将它们结合在一起，同时提供合理的性能时间。

确保您 [**下载了这篇文章的代码**](https://www.sitepoint.com/examples/flextwitter-part2/flextwitter_images.zip) 并尝试一下。

如果你感到自信， **[参加我们的文章测试](https://www.sitepoint.com/quiz/adobe/share-media-flex-twitter-images/)来测试你的知识吧！**

## 分享这篇文章