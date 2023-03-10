# 时髦的 Flickr Flex 小部件

> 原文：<https://www.sitepoint.com/funky-flickr-flex-widgets/>

web widget，或称徽章，是一种小型的可嵌入元素，您可以将其添加到您的网站中，以显示来自另一个 web 服务的内容。但是让网络部件正常工作可能是一件麻烦的事情。构建小部件最简单的方法之一是构建一个编译 Flash 对象的 [Flex](http://www.adobe.com/products/flex/) 应用程序。

在本文中，我将向您展示如何创建一个我们称之为 FlickrTag 的小 Flex 小部件。这个小工具可以嵌入到任何网页上，并会显示一组符合特定搜索条件的 Flickr 照片，集中在一个特定的位置；例如，您可以使用这个小部件显示加利福尼亚州弗里蒙特附近的狗的图片。你也可以使用代码作为你自己的小部件的基础——不管你使用 Flickr 还是其他服务，原理都是一样的。

有许多事情要做，所以让我们开始吧。第一步是安装 [Flex Builder 3](http://www.adobe.com/go/flex_trial) ，这是 Flex 的一个开发环境。您可以下载免费的 [Flex SDK](http://www.adobe.com/products/flex/flexdownloads/index.html#sdk) ，并从命令行编译这些代码。但是相信我，使用 Flex Builder 3 更简单，Adobe 提供 60 天免费试用。你还需要一个 [Flickr API 键](http://www.flickr.com/services/api/misc.api_keys.html)，这样你的小工具就可以检索图像。

##### 展示一些照片

安装 Flex Builder 后，让我们创建一个新的 Flex Builder 项目，并看看我们的 FlickrTag 小部件的第一个版本。第一个版本将使用 REST 请求从 Flickr 检索照片列表，并将它们排列成一个宽的图像带。在接下来的版本中，我们将有一些弹出功能，显示更多关于照片的信息。

这是我们需要的所有 MXML 电码。您可以在 flickrmap1.mxml 中查看[完整列表:](https://www.sitepoint.com/examples/flex-flickrtag/flickrtag1.txt)

```
<?xml version="1.0" encoding="utf-8"?> 

<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml" 

    layout="absolute" creationComplete="onStartup()"  

    width="660" height="100" 

    horizontalScrollPolicy="off" verticalScrollPolicy="off"> 

<mx:Script> 

<![CDATA[ 

// ... We'll fill this in soon 

]]> 

</mx:Script> 

<mx:HTTPService id="flickrSearch" resultFormat="e4x"  

    result="onFlickrResult(event)" /> 

<mx:HBox id="hbPictures" cornerRadius="15" width="660" height="100"  

    borderColor="#00FF00" borderThickness="3" borderStyle="solid"  

    backgroundColor="#CCFFCC" paddingBottom="10" paddingTop="10" 

    paddingLeft="10" paddingRight="10" horizontalGap="5" 

    horizontalScrollPolicy="off" verticalScrollPolicy="off"> 

</mx:HBox> 

</mx:Application>
```

在文件的顶部，我们有包装整个 Flex 应用程序的`<mx:Application>`标签。这个标签规定当应用程序被加载时，我们应该调用`onStartup`方法。这个方法可以在`<mx:Script>`标签中找到，我们还导入了一些必需的类，并设置了一些变量来存储图像数据和一个常量来存储 Flickr API 键:

```
<mx:Script> 

<![CDATA[ 

import mx.core.UIComponent; 

import mx.controls.Image; 

import mx.rpc.events.ResultEvent; 

private const FLICKR_KEY:String = 'Your Flickr API Key'; 

private var images:Array = []; 

private var imageItems:Array = []; 

private function onStartup() : void { 

  flickrSearch.url = createFlickrURL(); 

  flickrSearch.send(); 

  for( var imgItem:int = 0; imgItem < ( hbPictures.width - 30 ) / 80; imgItem++ ) { 

    var newImage:Image = new Image(); 

    newImage.data = null; 

    newImage.width = 75; 

    newImage.height = 75; 

    imageItems.push( newImage ); 

    hbPictures.addChild( newImage ); 

  } 

}
```

这个`onStartup`方法使用`createFlickrUrl`方法来设置 Flickr 搜索查询的 URL:

```
private function createFlickrURL( ) : String { 

  var query:String = 'http://api.flickr.com/services/rest/?api_key='+ 

      FLICKR_KEY;  

  query += '&method=flickr.photos.search&extras=geo'; 

  query += '&text='+parameters.text; 

  query += '&lat='+parameters.lat; 

  query += '&lon='+parameters.lon; 

  query += '&radius=30'; 

  query += '&per_page=100&page=1&sort=date-posted-desc'; 

  return query; 

}
```

`createFlickrUrl`函数使用一些在小部件创建代码中指定的参数。我们一会儿会看到如何指定它们。

为了结束这段代码，我们来看一下`onFlickrResult`方法，当 Flickr 发回 XML 响应时会调用这个方法:

```
private function onFlickrResult( event:ResultEvent ) : void { 

  for each( var photo:XML in event.result..photo ) { 

    var smallPhotoUrl:String = 'http://static.flickr.com/'+photo.@server+ 

        '/'+photo.@id+'_'+photo.@secret+'_s.jpg'; 

    var bigPhotoUrl:String = 'http://static.flickr.com/'+photo.@server+ 

        '/'+photo.@id+'_'+photo.@secret+'.jpg'; 

    images.push( {  

      source:smallPhotoUrl, 

      bigSource:bigPhotoUrl, 

      title:photo.@title, 

        latitude:photo.@latitude, 

        longitude:photo.@longitude 

      } ); 

  } 

  setPictures(); 

}
```

`onFlickrResult`方法查找所有的照片标签，并将图片位置、标题、纬度和经度存储在 images 数组中的一个对象中。

一旦`onFlickResult`方法咀嚼完所有的照片，`setPictures`方法编译从 feed 返回的 Flickr 图像的随机样本，并将它们放入一个盒子中，`hbPictures`:

```
private function setPictures() : void { 

  for each( var ii:Image in imageItems ) { 

    var imgIndex:int = int( Math.random() * images.length ); 

    ii.data = images[ imgIndex ]; 

    ii.source = images[ imgIndex ].source; 

  } 

}  

]]> 

</mx:Script>
```

那么，搜索词、纬度和经度来自哪里呢？嗯，它们来自我们指定的包含 Flash 文件的源 URL。我们的 HTML 模板使用 JavaScript 来嵌入元素，因此我们需要调整由 Flex Builder 3 管理的`index.template.html`文件中的 JavaScript，该文件位于项目文件夹的`html-template`文件夹中。您可以通过双击文件或右键单击项目面板中的文件并选择打开来编辑`index.html.template`文件。

您可以在下面看到模板的更新代码:
`...
} else if (hasRequestedVersion) {
 AC_FL_RunContent(
   "src", "${swf}?text=dog&lat=37.57228&lon=-122.0747",
   "width", "${width}",
   "height", "${height}",
   "align", "middle",
   "id", "${application}",
   "quality", "high",
   "bgcolor", "${bgcolor}",
   "name", "${application}",
   "allowScriptAccess","sameDomain",
   "type", "application/x-shockwave-flash",
   "pluginspage", "http://www.adobe.com/go/getflashplayer"
 );
}
...`

我们已经改变了参数`src`的值。在这种情况下，我指定我们应该查找的文本是 dog，而纬度和经度表示 Fremont 的位置。当我们从 Flex Builder 3 启动它时，我们应该会看到一条图像，如下图所示。

![Our first version of FlickrTag](img/3cf0369bf7e7dd1dacbd6bce3a326310.png)

这个小部件的最大问题是下载的大小。查看位于项目输出文件夹中的`FlickrTag.swf`文件；默认情况下，这是项目文件夹中的一个名为`bin-debug`的文件夹。即使功能有限，这个小部件的容量也在 400KB 左右，与其说是一个小部件，不如说是一个*小部件。*

为了精简它，我们将使用 Flash 9 的一个新功能，称为运行时共享库(RSL)。这意味着我们将把我们的代码从 Flex 框架代码中分离出来，前者很小，而后者相当大。我们的访问者第一次访问时，他们会下载我们的代码和 Flex 框架代码；之后，他们只需下载我们的代码并使用缓存在播放器中的框架版本。幸运的是，迁移到 RSLs 非常容易。第一步是进入项目菜单下的项目属性面板，并选择 Flex 构建路径。您将看到如下所示的对话框。

![The Project Settings dialog showing the Flex Build Path settings](img/c334e10a357e17e9b1783ef5dfd25df7.png)

在这里，我们选择 Flex 构建路径选项卡，并在其中选择库路径选项卡。一旦我们到达那里，我们就展开`framework.swc`文件，这是一个大的 Flex 框架库。单击链接类型项目，然后单击编辑按钮。
调出库路径项选项对话框，如下所示。

![The Library Path Item Options dialog](img/e95d9633df9c076fbe1301e462418b72.png)

在这个对话框中，我们将`framework.swc`代码合并到我们的 Flash 应用程序中，或者将其链接为 RSL。我们想要取消选中使用与框架相同的链接复选框，然后选择运行时共享库，如上所示。

完成后，我们可以启动小部件，并再次查看小部件的 SWF 文件的大小，发现它已经显著减小。随着我们向它添加更多的功能，它确实应该保持在这个大小。

Flex，结合 RSL 的使用，是快速构建 Flash 小部件的方法。

##### 添加映射

下一步是将地图添加到小部件中，以便当用户选择它时，它会显示照片的拍摄地点。为此，我将使用高级闪存组件的 UMap 组件[，该组件对于非商业用途是免费的。UMap 组件的优势在于，与 Google 和 Yahoo 地图组件不同，它的许可允许您在任何域上使用该组件。](http://www.afcomponents.com/)

下载并解压缩 UMap 组件。然后，为了添加组件，将下载档案中的`Flex Components`文件夹中的`.swc`文件复制到我们的 FlickrTag 项目文件夹中的`libs`文件夹中。

安装了地图的 FlickrTag 应用程序的完整代码可以在 [flickrtag2.mxml](https://www.sitepoint.com/examples/flex-flickrtag/flickrtag2.txt) 中看到。

首先，我们通过调整 mx:Application 标签来增加小部件的高度:

```
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml"   

    layout="absolute" creationComplete="onStartup()" width="660"   

    height="400" horizontalScrollPolicy="off"   

    verticalScrollPolicy="off">
```

在`<mx:Script>`标签中，我们需要导入一些地图类并添加一个新变量:
` 
import com.afcomponents.umap.overlays.Marker;  
import com.afcomponents.umap.types.LatLng;  
import com.afcomponents.umap.gui.*;  
import com.afcomponents.umap.events.MapEvent;  
import com.afcomponents.umap.core.UMap;  

private var map:UMap = null;`

我们需要调整`onStartup`函数，为图像添加一个鼠标点击事件监听器:

```
private function onStartup() : void {  

  flickrSearch.url = createFlickrURL();  

  flickrSearch.send();  

  for( var imgItem:int = 0; imgItem < ( hbPictures.width - 30 ) / 80; imgItem++ ) {  

    var newImage:Image = new Image();  

    newImage.data = null;  

    newImage.width = 75;  

    newImage.height = 75;  

    newImage.addEventListener(MouseEvent.CLICK,onImageClick);  

    imageItems.push( newImage );  

    hbPictures.addChild( newImage );  

  }  

}
```

我们还需要添加一些新的映射函数:

```
private function createMap() : void {  

  map = new UMap();  

  var uic:UIComponent = new UIComponent();  

  uic.setStyle( 'top', 0 );  

  uic.setStyle( 'left', 0 );  

  uic.width = mapArea.width;  

  uic.height = mapArea.height;  

  mapArea.addChild( uic );  

  map.setSize( mapArea.width, mapArea.height );  

  map.addEventListener(MapEvent.READY, onMapReady);  

  uic.addChild( map );    

}         

private function onMapReady( event:MapEvent ) : void {  

  map.setZoom( 12 );  

  centerMap();  

}  

private function centerMap() : void {  

  var lat:Number = Number( imgSelected.data.latitude );  

  var lng:Number = Number( imgSelected.data.longitude );  

  map.setCenter( new LatLng( lat, lng ) );  

  var m:Marker = new Marker( { position:new LatLng( lat, lng ) } );  

  map.addOverlay( m );  

}  

private function onImageClick( event:Event ) : void {  

  var clkImg:Image = event.currentTarget as Image;  

  imgSelected.data = clkImg.data;  

  imgSelected.source = clkImg.data.bigSource;  

  imgTitle.text = clkImg.data.title;  

  if ( mapArea.getChildren().length == 0 ) {  

    createMap();  

  } else {  

    centerMap();  

  }  

} 
```

最后，我们添加了一些新的界面元素:

```
<mx:HBox id="hbDetail" top="110" cornerRadius="15" width="660"   

    height="290" borderColor="#00FF00" borderThickness="3"   

    borderStyle="solid" backgroundColor="#CCFFCC" paddingBottom="10"  

    paddingTop="10" paddingLeft="10" paddingRight="10"   

    horizontalGap="5" horizontalScrollPolicy="off"  

    verticalScrollPolicy="off">  

  <mx:Image id="imgSelected" width="270" height="270"   

      horizontalAlign="center" verticalAlign="middle" />  

  <mx:VBox width="100%" height="100%"   

      horizontalScrollPolicy="off" verticalScrollPolicy="off">  

    <mx:Label id="imgTitle" fontWeight="bold" fontSize="14" />  

    <mx:Canvas id="mapArea" height="230" width="360"   

        horizontalScrollPolicy="off" verticalScrollPolicy="off"   

        clipContent="true" />  

  </mx:VBox>  

</mx:HBox>
```

用户界面现在分为两个框。顶部的框是一组图像，底部的框是地图和所选图像的放大版本。地图一旦初始化，就以所选图像的经纬度坐标为中心，并有一个标记来确认位置。你可以在下面看到结果。

![Our second version, now with a map](img/82c5d516347194a480958ceffe747683.png)

这看起来很漂亮，但是它太大了，不能包含在网页中。理想情况下，只有小部件的顶部是可见的。然后，当用户单击它时，下拉菜单应该出现在网页中的文本上。这样，该控件在被使用之前可以只占用很少的空间。

##### 构建弹出版本

实现下拉菜单转换阶段需要一些小的改变。为此，我们将为 Flex 应用程序创建两种状态。第一个是条形状态，只显示图像的水平列表。第二个是显示图像列表和图像细节窗口的细节状态。

这两种状态在`<mx:State>`标签中定义，两种状态之间的转换在`<mx:Transition>`标签中定义。在`<mx:Application>`标签中定义了一个初始状态。在这种情况下，我将淡入细节窗口，但您可以使用任何您喜欢的效果来使应用程序变得生动。

你可以在 flickrtag3.mxml 中看到[完整的代码。](https://www.sitepoint.com/examples/flex-flickrtag/flickrtag3.txt)

下面是我们将添加到 mx:Application 元素中的内容:

```
<mx:Application xmlns:mx="http://www.adobe.com/2006/mxml"    

    layout="absolute" creationComplete="onStartup()" width="660"    

    height="400" backgroundAlpha="0" horizontalScrollPolicy="off"    

    verticalScrollPolicy="off" currentState="bar">
```

下面是我们如何在`<mx:Script>`元素中改变`onImageClick`函数:

```
private function onImageClick( event:Event ) : void {   

  var clkImg:Image = event.currentTarget as Image;   

  imgSelected.data = clkImg.data;   

  imgSelected.source = clkImg.data.bigSource;   

  imgTitle.text = clkImg.data.title;   

  if ( mapArea.getChildren().length == 0 ) {   

    createMap();   

  } else {   

    centerMap();   

  }   

  currentState = 'detail';   

}
```

我们的状态和转换定义如下:

```
<mx:states>   

  <mx:State name="bar">   

    <mx:SetProperty target="{hbDetail}" name="visible" value="false" />   

  </mx:State>   

  <mx:State name="detail">   

    <mx:SetProperty target="{hbDetail}" name="visible" value="true" />   

  </mx:State>   

</mx:states>   

<mx:transitions>   

  <mx:Transition fromState="bar" toState="detail">   

  <mx:Sequence>   

    <mx:SetPropertyAction target="{hbDetail}" name="visible" />   

    <mx:Fade target="{hbDetail}" alphaFrom="0" alphaTo="1"    

        duration="200" />   

  </mx:Sequence>   

  </mx:Transition>   

  <mx:Transition fromState="detail" toState="bar">   

  <mx:Sequence>   

    <mx:Fade target="{hbDetail}" alphaFrom="1" alphaTo="0"    

        duration="200" />   

    <mx:SetPropertyAction target="{hbDetail}" name="visible" />   

  </mx:Sequence>   

  </mx:Transition>   

</mx:transitions>
```

为了避免一个大的灰色矩形，我们应该调整`index.template.html`文件，将小部件的`wmode`参数设置为`transparent`，这样小部件背景是透明的:

```
AC_FL_RunContent(   

  "src", "${swf}?text=dog&lat=37.57228&lon=-122.0747",   

  "width", "${width}",   

  "height", "${height}",   

  "align", "middle",   

  "id", "${application}",   

  "quality", "high",   

  "bgcolor", "${bgcolor}",   

  "wmode", "transparent",   

  "name", "${application}",   

  "allowScriptAccess","sameDomain",   

  "type", "application/x-shockwave-flash",   

  "pluginspage", "http://www.adobe.com/go/getflashplayer"   

);
```

除了状态和转换之外，应用程序其余部分的代码保持完全相同。

现在棘手的部分开始了:我们如何将控件精确地放置在 HTML 页面上，以便让其余的内容围绕 Flex 应用程序流动？秘诀是在 HTML 中用条的宽度和高度绝对定位一个`div`元素。

为了演示这项技术，我们可以对`index.html.template`文件做一些修改。就在开头的`<body>`标签后，加上以下内容:
` 
<p>Add a paragraph of text here...</p>  
<div id="widget"><div>`

然后就在结束的`<body>`标记之前，添加以下内容:

```
</div></div>   

<p>Add a paragraph of text here...</p>   

<p>Add a paragraph of text here...</p>   

<p>Add a paragraph of text here...</p>
```

这将使我们的小部件充满内容。注意，小部件现在位于一个`div`元素中，该元素嵌套在一个 ID 为`inner`的父`div`元素中。

然后我们需要添加一些 CSS:

```
#widget {   

  height:100px;   

  width:660px;   

  position:relative;   

}   

#widget div {   

  position:absolute;   

  top:0;   

  left:0;   

}
```

我们将外部`div`的高度限制为 100 像素，这样它可以包含*栏*模式下的小部件，但不会遮挡页面内容。绝对定位包含小部件的`div`将允许它在单击图像时弹出到页面内容上。

从 Flex Builder 3 中启动它，您将看到一个与下图类似的屏幕。

![The widget in bar mode](img/130422e607602643e56979989aa2e349.png)

这显示了工具栏模式下的小部件。从这里，我可以单击其中一个图像，应用程序的细节部分淡入 HTML 页面的其余部分。你可以在这里看到:

![The widget in detail mode floating over the content](img/bdad80e29516b9fa1bd3638910c7efa5.png)

这种方法的好处在于，您可以在页面上创建一个 Flash 应用程序，它只占用最小的空间，直到用户想要点击它。从那里你可以扩展应用程序来占据大量的空间并真正展示它的价值。

##### 从这里去哪里

这篇文章真的只是一个起点。您可以将在此学到的知识(关于访问 web 服务、映射和创建扩展的 Flash 应用程序)用于您自己的工作中。但除此之外，您应该开始看到使用 Flex 作为一种更快地构建 Flash 小部件的方式的潜力，以及提供比单独使用 Flash 更好的可维护性。

## 分享这篇文章