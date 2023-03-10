# 使用 Flash Builder 4 和 Flex 4 SDK 构建 Flickr 浏览器

> 原文：<https://www.sitepoint.com/flickr-browser-flex/>

Flex 4 引入了大量的新特性，今天我们将看看一些最新的架构变化，这些变化使得组件开发和皮肤变得更加容易。首先，你需要下载 [Flash Builder 试用版](http://www.adobe.com/products/flashbuilder/)。这也将为您提供 Flex 4 SDK。虽然 Flash Builder 仍处于测试阶段，但它为开发人员提供了许多强大的功能，并且已经比 Flex Builder 向前迈进了一步。

你还需要 [Flash Player 10](http://www.adobe.com/support/flashplayer/downloads.html) ，出于开发目的，它应该是调试版本。要使用 Flickr API，你需要[一个 API 密钥](http://www.flickr.com/services/apps/create/apply)，然后你可以将它导入 Flash Builder。

本文将假设您对 Flex 和 ActionScript 有一定的了解，但是没有必要特别高深才能从中受益。最终，您将能够让 Flex 与 Flickr 的 API 对话，下载和显示图像，并在 Flex 4 中进行一些基本的换肤操作。您可以下载这个项目的所有完成代码作为 [Flash Builder 项目档案](https://i2.sitepoint.com/examples/flexflickrbrowser/FlickrCreativeCommonsBrowser.zip)，然后您可以将它导入 Flash Builder。

请务必在最后[参加测验](https://www.sitepoint.com/quiz/flex4/flickr-browser-flex)来测试您的开发人员证书。

## AS3 库设置

互联网上所有图片共享服务的鼻祖是 Flickr，使用其公共 API 制作 mashups 已经成为一种仪式。在本文中，我们将探索 Flickr 巨大的图像库的深度，这些图像是在 Creative Commons 2.0 许可下许可的，允许你在注明它们的属性的情况下自由使用它们。这使得 Flickr 成为 web 开发人员和设计人员的绝佳资源，因此我们的项目将成为帮助查找和浏览这些图片的工具。

我们从 Adobe 工程师编写的 [ActionScript 3 Flickr API 库](http://code.google.com/p/as3flickrlib/)开始。它是免费的，就像它所依赖的 [AS3 核心库](http://code.google.com/p/as3corelib/)一样。我们将在本项目中使用这两个工具，所以一旦您安装了 Flash Builder，请下载它们。

打开包含 AS3 核心库的归档文件，在`lib`目录中您会找到`as3corelib.swc`。将该文件复制到您的 Flash Builder 项目的`libs`目录中，Flash Builder 会完成剩下的工作。

接下来打开 Flickr 库的归档文件。其中包含一个`swc`文件，但是在我们最后一次检查时，它是为 Flex 2 编译的，所以我们将把源代码放到我们的项目中。从档案中打开`src`目录，复制你在里面找到的`com`目录。把它放到你的 Flash Builder 项目的`src`目录中，然后点击 Flash Builder 中的项目菜单，选择 Clean… 。这将确保编译器能够获得新的代码和库。

## 从 Flickr API 中检索数据

我们的第一个任务是加载 Flickr API 库并连接到 API 本身。在您的主 MXML 文件中，修改您的`<s:Application>`标签，如下所示:

```
<s:Application  xmlns:fx="http://ns.adobe.com/mxml/2009"  xmlns:s="library://ns.adobe.com/flex/spark"  xmlns:mx="library://ns.adobe.com/flex/halo"  minWidth="1024"  minHeight="768"  xmlns:flickr="com.adobe.webapis.flickr.*">
```

在此之下，添加一个`script`块并输入以下语句:

```
import com.adobe.webapis.flickr.events.FlickrResultEvent;import com.adobe.webapis.flickr.FlickrService;import com.adobe.webapis.flickr.PagedPhotoList;private static const FLICKR_API_KEY:String = "nnnnnnnnnnnnnnnnnnnnnnnnnnnnnn"; //Your Flickr API keyprivate static const COMMONS_LICENSE_ID:Number = 2;private static const MAX_RETURNED_RECORDS:Number = 50;public var flickrService:FlickrService = new FlickrService(FLICKR_API_KEY);
```

上面的代码包含了我们需要的 Flickr API 类的 import 语句，一些常量定义，以及一个新的`FlickrService`对象的实例化，我们将用它来建立所有的 Flickr API 连接。现在我们准备开始了。创建以下函数，并将其设置为在应用程序`creationComplete`事件上调用:

```
private function onComplete() : void {  flickrService.addEventListener(FlickrResultEvent.PHOTOS_SEARCH, onSearchResult);}
```

```
<s:Application  xmlns:fx="http://ns.adobe.com/mxml/2009"  xmlns:s="library://ns.adobe.com/flex/spark"  xmlns:mx="library://ns.adobe.com/flex/halo"  minWidth="1024"  minHeight="768"  xmlns:flickr="com.adobe.webapis.flickr.*"  *creationComplete="onComplete()"*>
```

当我们初始化变量`flickrService`并实例化`FlickrService`对象时，我们对 Flickr 本身进行了一个远程调用，传入了我们的 API 密钥来创建一个经过身份验证的链接。我们现在可以使用该服务来发出请求，但是在此之前，我们在`onComplete`函数中设置了一个监听器来处理搜索结果。

既然我们已经有了，我们需要搜索本身。为了允许用户输入搜索查询，我们将在脚本块下添加一个`TextInput`元素和搜索按钮，如下所示:

```
<s:HGroup width="260" height="27" textAlign="center" x="68" y="89">    <s:Label text="Search" color="0xFFFFFF"/>    <s:TextInput id="tags"/>    <s:Button name="searchBtn" label="Search"         click="searchBtnClick(event)"/></s:HGroup>
```

注意`<s:HGroup>`标签。在 Flex 3 和以前的版本中，我们使用了一个`<mx:HBox>`标签，但是现在我们可以使用组标签，它比`HBox`和`VBox`容器的开销要少。搜索按钮还包含一个对新函数`searchBtnClick`的调用，我们将在这里放置搜索代码。将此函数添加到您的脚本块中:

```
private function searchBtnClick(event:MouseEvent) : void {   flickrService.photos.search("",     this.tags.text,     "any",     "",     null,     null,     null,     null,     COMMONS_LICENSE_ID,     "owner_name",     MAX_RETURNED_RECORDS); }
```

对于任何想知道的人来说，这段代码的奇怪布局(将每个参数放在单独的一行上)只是为了便于阅读和编辑。布局实际上对代码的工作没有影响。

好的，这里我们向 Flickr API 发出第一个真正的请求。使用`photos.search`方法，我们发送一些值，这些值实际上表示“在 Creative Commons 2.0 许可下查找标签与我们的搜索查询相匹配的图像。”`MAX_RETURNED_RECORDS`参数用于限制一次命中返回的项目数。有关 API 及其所有方法的更多详细信息，请查看 [API 文档](http://www.flickr.com/services/api/)。

为此，我们需要接收函数。我们之前设置了一个事件监听器，每当我们有搜索查询的结果要处理时就调用这个函数。将以下内容添加到您的代码中:

```
private function onSearchResult(event:FlickrResultEvent) : void {   if (event.success) {     var resultsList:PagedPhotoList = event.data.photos;     photos = new ArrayCollection( resultsList.photos );   } else {     Alert.show("Flickr search error: " + event.data.error);   } } 
```

正如你在函数的第一行看到的，`FlickrResultEvent`有我们需要的所有东西，包括一个方便的指示成功的成功属性。在我们的例子中，我们检查这个属性并简单地显示一条错误消息，包括从 Flickr 发回的错误信息。如果交易成功，我们从 Flickr 接收的 XML 数据中创建一个`PagedPhotoList`对象，并使用它生成一个我们可以绑定的`ArrayCollection`。

此时，您的应用程序应该编译并运行，没有任何错误。如果您要公开`event.data.photos`的内容，您会看到一个类似于下面的 XML 包(摘自 Flickr API 文档中关于 [`flickr.photos.search`](http://www.flickr.com/services/api/flickr.photos.search.html) ):

```
<photos page="2" pages="89" perpage="10" total="881">   <photo id="2636" owner="47058503995@N01"     secret="a123456" server="2" title="test_04"    ispublic="1" isfriend="0" isfamily="0" />   <photo id="2635" owner="47058503995@N01"    secret="b123456" server="2" title="test_03"    ispublic="0" isfriend="1" isfamily="1" />   <photo id="2633" owner="47058503995@N01"    secret="c123456" server="2" title="test_01"    ispublic="1" isfriend="0" isfamily="0" />   <photo id="2610" owner="12037949754@N01"    secret="d123456" server="2" title="00_tall"    ispublic="1" isfriend="0" isfamily="0" /> </photos>
```

我们还为我们的搜索增加了一个额外的参数；sending `"owner_name"`告诉 API 发送照片所有者的全名，这样我们就可以用它来进行归属。

## 显示列表

现在我们有了一些从 Flickr API 返回的数据，我们需要一种方法来显示它。我们感兴趣的是照片本身，那么还有什么比显示缩略图列表更好的呢？在 Flex 3 中，我们可能为此使用了一个`TileList`，但是这个类已经被一个非常方便的名为`List`的类所取代；我们可以使用`List`来重建`TileList`的功能，但是有更多的选项和更好的性能。现在在你的代码中添加一个`List`，在搜索按钮下面:

```
<s:List   id="imageDisplay"   dataProvider="{photos}"   width="530"   height="400"   x="58"   y="124">   <s:layout>     <s:TileLayout       requestedColumnCount="5"       requestedRowCount="8"       horizontalGap="2"       verticalGap="2"/>   </s:layout>   <s:itemRenderer>     <fx:Component>       <mx:Image source="{'http://static.flickr.com/' + data.server +           '/' + data.id + '_' + data.secret + '_t.jpg'}"           height="100" width="100" />     </fx:Component>   </s:itemRenderer> </s:List>
```

我们通过指定一个特殊的布局类`TileLayout`来实现旧的`TileList`功能，我们向这个类提供布局的参数，比如列表中每个项目之间的水平间距。

我们使用一个内嵌的项目渲染器，它简单地将图像缩略图拉入并显示出来。您看到的组装在`Image`元素的`source`属性中的字符串是列表中给定图像的正确 URL。添加`_t.jpg`意味着我们链接到 Flickr 的缩略图，而不是完整的图片。

如果你编译并运行你的应用程序，输入一个搜索词，比如 **`monkey`** ，然后点击搜索按钮，你会看到图片出现在你的`List`框中。完成交易可能需要几秒钟，但一旦完成，我们会看到从 Flickr 检索到的符合我们标准的图片列表:它们使用标签 **`monkey`** ，并且只使用 Creative Commons 2.0 许可证。

## 分享这篇文章