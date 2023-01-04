# 使用 Flex 在 Twitter 上共享媒体，第三部分:视频

> 原文：<https://www.sitepoint.com/share-media-flex-twitter-video/>

**这是我们使用 Flex 系列在 Twitter 上分享媒体的第三部分，也是最后一部分(阅读[第一部分](https://www.sitepoint.com/article/share-media-flex-twitter)和[第二部分](https://www.sitepoint.com/article/share-media-flex-twitter-images))。在第一篇文章中，我们向您展示了如何在 Flash Builder 中使用 Flex 框架构建 Twitter 应用程序，将 Photoshop 插图导入 Flash Catalyst 来构建界面及其交互。第二篇文章向您介绍了将图片上传到 Flickr 所需的过程和库，并使用 bit.ly API 来缩短这些图片的链接。在本系列的最后一篇文章中，我们将扩展该应用程序，将视频上传到 Flickr，并使用一些内置的 Flex 功能从视频中抓取截图并上传。**

一旦你完成了，一定要在我们的[文章测验](https://www.sitepoint.com/quiz/adobe/share-media-flex-twitter-video)中测试你的知识！

在上一期文章中，我们讨论了在推特上发布图片的潜在方式。我们认为最简单的方法是将它们上传到 Flickr，使用 bit.ly API 创建一个指向 Flickr 页面的最小化链接，然后将该链接包含在 tweet 中。这种方法效果很好，所以我们将对视频做同样的事情。

在我们开始之前，您应该下载[代码档案](https://i2.sitepoint.com/examples/flextwitter-part3/flextwitter3.zip)以便跟进。这一次我们将使用的文件是`cheepcheep_video_flashbuilder.fxp`，在`Flex projects`文件夹中(应用程序的以前版本也在那里，所以您可以看到发生了什么变化)。将该文件导入 Flash Builder，您就可以开始使用了。

#### 将视频上传到 Flickr

我们的第一个任务是修改`FileReference`元素使用的过滤器，以包含 Flickr 支持的视频文件扩展名。我们已经在上一篇文章中创建的`FileFilter`中添加了 avi、wmv、mov、mpg、mp4、m4v 和 3gp。虽然这些扩展都得到 Flickr 的官方支持，但仍然存在 Flickr 无法对某些文件进行编码的风险；这是因为视频可以用各种不同的编解码器进行压缩，并且只支持最常用的编解码器(如 H.264)。我们建议您查看 Flickr 文档以了解支持的内容。

Flickr 允许你上传长达 90 秒、150 兆字节的免费视频，或者 500 兆字节的专业视频。意识到视频文件可能比图像大，我们调整了原来的界面，添加了一个额外的`Label`组件来显示所选文件的大小。我们还增加了一个按钮，如果选择了一个视频文件，可以查看视频选项。稍后我们将更详细地查看这个按钮；现在只需注意它在默认情况下是禁用的。

一旦选择了一个文件，就调用`fileAccessed`函数，该函数已经被修改以获取额外的信息:文件的大小(我们用新的`Label`显示)及其扩展名。`FileReference`确实有一个类型属性，但是它不可靠(例如，它不能确定 GIF 文件的扩展名)，所以我们使用自己的变量。为了获得扩展名，我们在每个句点处拆分文件名，并获取结果数组的最后一个元素。我们还在主应用程序的顶部添加了数组，用于存储照片和视频文件的可接受扩展名:

```
private var photoExtensions:Array = ["gif","jpeg","jpg","png"]; 
private var videoExtensions:Array = ["avi","wmv","mov","mpg","mp4","m4v","3gp"]; 

... 

private function fileAccessed(evt:Event):void { 
  photoFileSize.text = (Number(fileReference.size)/100) + "kb"; 
  photoFileName.text = fileReference.name; 
  var filename:Array = fileReference.name.split("."); 
  flickrUploadType = filename[filename.length - 1]; 
  photoUploadBtn.enabled = true; 
}
```

成功上传到 Flickr 会调用`uploadCompleteHandler`函数。我们修改了该函数，将上传媒体的 ID 赋给一个新的`flickrUploadID`变量。我们还测试文件的扩展名，看它是否在可接受的视频扩展名列表中。如果是，我们启用新的**视频选项**按钮。该函数的其余部分从 Flickr 获取上传的 URL，并将其提交给我们的 bit.ly 服务，与上一篇文章没有什么变化:

```
private function uploadCompleteHandler(evt:DataEvent):void { 
  CursorManager.removeBusyCursor(); 
  var xData:XML = new XML(evt.data); 
  flickrUploadID = xData.photoid;  
  if ( videoExtensions.indexOf(flickrUploadType) != -1 ) { 
    videoOptionsBtn.enabled = true; 
  } 
  photoUrl = "http://www.flickr.com/photos/"+flickrNsid+"/"+xData.photoid; 
  bitlyService.send(); 
}
```

#### 从 Flickr 获取视频

我们将使用**视频选项**按钮切换到我们添加到应用程序中的新状态(`videoOptions`)。这个状态显示了我们用 Flash Catalyst 创建的一个新组件:它的目的是显示上传的视频，使用户能够抓取视频的快照作为上传的图像。然而，在切换到这种状态之前，我们需要从 Flickr 检索视频以便回放。因此，我们将编写一个函数，当按钮被单击时向 Flickr 发送请求，另一个函数在收到响应时切换应用程序的状态。

我们称之为`showVideoOptions`的第一个函数使用上一篇文章中使用的相同 Flickr ActionScript 库调用 Flickr API 的`getSizes`方法。它还设置了一个事件侦听器来处理 API 响应:

```
private function showVideoOptions():void {  
  flickr = new FlickrService(flickrApiKey);  
  flickr.addEventListener(FlickrResultEvent.PHOTOS_GET_SIZES, handleVideoData);  
  flickr.photos.getSizes(flickrUploadID);  
}
```

现在让我们来看看我们的回调函数`handleVideoData`函数。如果 Flickr 仍在处理上传，传递给该函数的`FlickrResultEvent`将包含一个错误，通知我们视频还没有找到。另一方面，如果视频*已经被*处理，结果将包含一个照片大小的数组。其中一个“照片大小”其实是 MP4 视频。

我们的第一个任务是查看收到的数据是否包含错误。我们可以使用内置函数`hasOwnProperty`来完成这项工作。如果我们发现一个错误，我们向用户显示一个`Alert`,通知他们视频仍在处理中。否则，我们切换到`videoOptions`状态来显示新的组件，然后遍历`photoSizes`数组，查找“Site MP4”的值(这是 Flickr 分配给 MP4 视频的“size”字符串)。然后，我们将数组索引的 source 属性传递给自定义组件的`videoLocation`属性，并调用组件的`setVideoPlayer`方法:

```
private function handleVideoData(evt:FlickrResultEvent):void {  
  if ( evt.data.hasOwnProperty("error") ) {  
    Alert.show("It appears that Flickr is still processing your video,   
    please wait another minute and try again", "Video not available");  
  } else {  
    currentState = "videoOptions";  
    var len:Number = evt.data.photoSizes.length;  
    for (var i:Number = 0;i<len;i++) {  
      if ( evt.data.photoSizes[i].label == "Site MP4" ) {  
        customcomponent31.videoLocation = evt.data.photoSizes[i].source;  
        customcomponent31.setVideoPlayer();  
        break;  
      }  
    }  
  }  
}
```

#### 显示视频

```
CustomComponent3 has a VideoElement component positioned in the component's layout using a Group. VideoElement is a new component introduced with Flex 4: it's basically just a chrome-less version of the VideoPlayer component. It's really handy for grabbing video frames, as there's no need to strip away the player controls. The Group element is necessary for reasons I'll explain shortly.我们从`handleVideoData`调用的`setVideoPlayer`函数将视频的 URL 分配给`VideoElement`组件的 source 属性。这将使组件下载并播放视频。我们还使用了一个设置为`indeterminate`模式的`ProgressBar`组件，给用户一个文件正在下载的视觉提示。为了在下载完成后隐藏进度条，我们需要向组件的`init`函数添加一个新的事件监听器。监听器调用`videoLoadProgress`函数，该函数隐藏进度条并启用快照:

```
private function init():void {  
  ...  
  videoPlayer.addEventListener(ProgressEvent.PROGRESS,videoLoadProgress);  
  ...  
}  

...  

private function videoLoadProgress(evt:ProgressEvent):void {  
  if ( evt.bytesLoaded == evt.bytesTotal || videoPlayer.playing ) {  
    videoProgress.visible = false;  
    snapshotBtn.enabled = true;  
  }  
}
```

我们还需要一个事件处理程序，以便在视频播放完成后启用 **Replay** 按钮。我们将向`init`函数添加另一个监听器(使用`videoElement.COMPLETE`事件)并创建一个名为`enableReplayBtn`的方法，该方法简单地将按钮的`enabled`属性值设置为`true`。看起来仅仅给`videoElement`本身添加一个内联监听器会更容易，因为我们的`enableReplayBtn`函数只有一行代码。然而，为了保持代码的可读性和可维护性，让所有的侦听器在同一个地方更有意义。
拍摄快照
**快照**按钮调用`frameGrab`函数，我在下面列出了这个函数。为了拍摄视频快照，我们使用了 Flex 内置的`ImageSnapshot` helper 类的一个函数。`captureImage`不是直接从视频中抓取一帧，而是返回你指向的元素所显示的当前图像。这就是为什么我们使用`VideoElement`而不是`VideoPlayer`的原因:我们更希望避免在截图中抓取玩家控件。
`ImageSnapshot`类可以从任何实现`flash.display.IBitmapDrawable`类的组件中捕获。这包括 Flex UIComponents，但是*不包括`VideoPlayer`或者`VideoElement`；这就是为什么我们把`VideoElement`包在`Group`里的原因。*
我们将由`captureImage`函数获取的数据赋给一个变量(这样我们以后可以从主应用程序中访问它)，使用`as`关键字将它类型化为`ByteArray`。然后，我们将该变量分配给一个新的`Image`组件的 source 属性，以显示快照的预览。最后，我们启用**上传**按钮:

```
private function frameGrab(evt:Event):void {  
  var imageSnap:ImageSnapshot = ImageSnapshot.captureImage(videoGroup);  
  snapshotPreview.source = imageSnap.data as ByteArray;  
  uploadSnapshotBtn.enabled = true;  
}
```

有了这些代码，用户就可以观看视频，随心所欲地重播，并在播放时拍摄快照。接下来，我们将添加允许他们将当前快照上传到 Flickr 的功能。

#### 上传 ByteArray 作为图像

我们正在使用的 Flickr 库的`Upload`类被设计为从用户的文件系统接收上传文件。对于我们的照片上传，我们使用了`FileReference`元素来完成。然而，我们捕获的快照不是一个文件，而是内存中的一个值，不能被`FileReference`元素访问。那我们怎么上传呢？

没有简单的解决方案，但对我们来说幸运的是，另一个人遇到了这个问题，并修改了 Flickr 库中的`Upload`类，以添加所需的功能([http://blog.dannypatterson.com/?p=250](http://blog.dannypatterson.com/?p=250))。我们从那篇博文中复制了`uploadBytes`方法，并将其添加到`Upload.as`文件中(位于`com.adobe.webapis.flickr.methodgroups`)。这个功能允许我们上传我们的`ByteArray`,就像它是一幅图像一样。我们需要稍微修改一下函数，添加一个事件监听器，以便在上传完成时触发。这个监听器调用另一个新函数，我们也已经添加到了`Upload.as` : `uploadBytesComplete`。我们用它来调度事件，这样一旦图像上传完成，我们就可以在我们的应用程序中捕获它:

```
public function uploadBytes(...) : Boolean {   
  ...   
  loader.addEventListener(Event.COMPLETE, uploadBytesComplete);   
  ...   
}   

private function uploadBytesComplete(event:Event):void {   
  var result:FlickrResultEvent = new FlickrResultEvent(PHOTOS_UPLOAD_BYTES_COMPLETE );   
  MethodGroupHelper.processAndDispatch( _service, URLLoader( event.target.loader ).data, result,"photoid", MethodGroupHelper.parseUploadBytesResult );       
}
```

`uploadBytes`方法的行为与 Flickr `upload`方法几乎完全相同。但是，还需要向它传递几个参数:一个图像标题、一个描述、一个标签列表和一个“public”标志。这些参数对于`upload`也是可用的，但是它们是可选的，为了简洁起见我们省略了它们。

为了利用这个增加的功能，我们在主应用程序文件中编写了一个新函数:`uploadVideoCap`。这个函数与`uploadFlickr`非常相似，除了我们用`uploadBytes`代替`upload`。你会注意到我们正在设置`flickr.permission`为“写”这是`uploadBytes`功能正常工作所必需的:

```
public function uploadVideoCap(capData:ByteArray):void {   
  flickr = new FlickrService(flickrApiKey);   
  flickr.secret = flickrSecret;   
  flickr.token = flickrAuthToken;     
  flickr.permission = "write";     

  flickr.addEventListener(   
    FlickrResultEvent.PHOTOS_UPLOAD_BYTES_COMPLETE,   
    videoCapUploaded    
  );   

  var uploader:Upload = new Upload(flickr);   
  uploader.uploadBytes(capData, "Video snapshot", "From Flex", "twitter,test,video,snapshot", true);   
  CursorManager.setBusyCursor();           
}
```

当截图上传完成后，我们调用`videoCapUploaded`函数，该函数获取 Flickr URL 并将其提交给我们在上一篇文章中创建的 bit.ly 服务:

```
private function videoCapUploaded(evt:FlickrResultEvent):void {   
  CursorManager.removeBusyCursor();   
  photoUrl = "http://www.flickr.com/photos/"+flickrNsid+"/"+evt.data.photoid;   
  bitlyService.send();           
}
```

回到我们的定制组件，我们已经创建了一个名为`uploadSnapshot`的函数来调用`uploadVideoCap`。然后，`uploadSnapshot`被绑定到**上传**按钮，组件的`init`函数中有一个事件监听器。为了从组件内部调用`uploadVideoCap`函数，我们需要使用`mx.core.FlexGlobals.topLevelApplication` : 
 `   
private function uploadSnapshot(evt:Event):void {   
  mx.core.FlexGlobals.topLevelApplication.uploadVideoCap(imageByteArray);   
  closeState();   
}`来指定它的路径

我们快完成了！现在我们所需要的是一个关闭`videoOptions`状态的方法，以确保我们停止视频播放并将应用程序切换回主`twitterDisplay`状态。`closeState`方法本身是不言自明的。注意，它有一个可选的`evt:Event`参数。这是因为我们还将调用该方法作为组件的关闭按钮的事件处理程序(在这种情况下，它将被隐式传递一个`Event`参数)。该事件处理程序在组件的`init`函数中声明:

`private function init():void {   
  ...   
  closeBtn.addEventListener(MouseEvent.CLICK,closeState);   
  ...   
}   

...   

private function closeState(evt:Event = null):void {   
  videoPlayer.stop();   
  mx.core.FlexGlobals.topLevelApplication.currentState = "twitterDisplay";       
}` 

就是这样！这是一个有趣的应用程序。虽然它肯定是功能性的，但可以添加许多功能来完善它:例如，您可以将 Twitter 文本输入限制在 140 个字符以内，或者改进视频快照交互。`ImageSnapshot`功能如此简单，以至于很容易想象在其他视频应用中使用它:例如，添加注释以反馈给摄像机操作员或编辑。

希望本系列已经向您展示了使用 Flash Catalyst 和 Flash Builder 设计和构建富互联网应用程序是多么容易。您还应该对如何与一些主要的 web APIs 进行交互有所了解，这对开发您自己的社交媒体应用程序非常有用！

你还在等什么？走出去，建立一些很酷的东西！但在此之前，你为什么不参加我们的[文章测试](https://www.sitepoint.com/quiz/adobe/share-media-flex-twitter-video)来测试一下你学到了什么？

## 分享这篇文章

```