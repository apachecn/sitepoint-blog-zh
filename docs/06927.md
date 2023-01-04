# 用文件 API 加载

> 原文：<https://www.sitepoint.com/get-loaded-with-the-file-api/>

为什么头像上传会限制我们的文件大小？你知道，“请选择一个图像(最大 50 KB)。”既然 *canvas* 已经存在了一段时间，为什么照片处理网络应用程序还没有实现呢？

这两个问题的答案都归结于性能。到目前为止，我们需要一个缓慢的插件或通过服务器的路径来在浏览器中创建和修改文件。但是对于 Internet Explorer 10、Firefox 和 Chrome 用户来说，开发人员的武器库中有令人敬畏的文件 API，可以在浏览器中实现这些操作。

File API 是一个新的 JavaScript API，它允许您在 Web 应用程序中读写代表文件的二进制数据对象。简而言之，您可以将用户选择的文件读入 Web 应用程序，并从 Web 应用程序下载新的数据对象作为文件。让我们深入了解一下。

## 分解文件 API

文件 API ( [由 W3C](https://www.w3.org/TR/FileAPI/) 定义)不是一种类型，而是类型化对象、函数和方法的集合。

## FileList

文件列表是一个类型化的对象，它存在于许多地方。首先，作为类型为“file”的表单输入元素上的属性。第二，作为在 drop file 事件或 Clipboard 事件上调度的事件的一部分(作为复制和粘贴的结果)。

例如，假设您有这样一个表单输入控件:

```
<input type=file onchange="console.log(this.files.length)" multiple />

```

每当用户单击表单输入并选择一个文件时，该操作将调度 onchange 事件处理程序，并且 FileList 对象(相对于 input 元素位于 *this.files* 处)上有一些新条目。在本例中，用户选择的文件数量被打印到浏览器控制台。

尽管 FileList 对象的行为类似于本机数组——因为您可以像使用数组一样遍历其内容——但 FileList 只包含 File 对象的不可变实例(下面将进行描述)。

## 文件

File 对象代表文件列表中的单个文件。File 对象包含关于文件的只读元数据的散列，包括名称、最后修改日期、大小和类型。它还用作引用，可以传递给 FileReader 以读取其内容。

## 二进制大对象

Blob 接口公开了文件的原始二进制数据。远程数据可以通过 XHRRequest 作为 blob 对象——如果 *xhr.responseType* 设置为“blob”的话。新的 blob 对象是 blob API 的一个实例，并且包括诸如 blob.slice(i，i+n)之类的本机方法，这些方法可用于将大的 Blob 对象分割成较小的 Blob 对象。(当谈到 JavaScript 对象类型时，我使用短语“blob 接口”,而“Blob 对象”指的是 Blob 接口的单个实例。)

此外，使用 blob 接口构造函数，微小的 Blob 对象可以通过该接口再次合并在一起。

```
new Blob([blob, blob,...])
```

你可以在 [BlobBuilder](http://ie.microsoft.com/testdrive/HTML5/BlobBuilder/) 演示中找到一个例子，它加载一个 MP3 文件，然后将它分成不同的音频文件(音轨),每个文件都应用到它们自己的<音频>标签上进行播放。在演示中，用户可以通过合并音轨以另一种顺序重建 MP3，甚至可以下载新的 blob 对象作为 MP3。

注意:W3C 已经否决了 BlobBuilder，转而支持 Blob。两者产生相同的结果，但方式不同。此外，WebKit 的 BlobBuilder 在 Internet Explorer 和 Firefox 之间有所不同，所以最好先对 Blob 进行特征检测。

## 文件阅读器

FileReader 接口获取一个文件或 Blob 对象并读取其内容——文件或 Blob 对象只是对存储在本地计算机上的内容的引用。FileReader 可以针对读取各种类型的文件进行优化，例如，文本(UTF-8)、ArrayBuffer(二进制)或 base64 data-uri。下面的示例显示了在给定 blob 对象实例的情况下，如何使用 FileReader 获取文件的文本。

```
	var reader = new FileReader();
	reader.onload = function(e){
	       console.log(e.target.result);
	}
	reader.readAsText(blob);
```

类似地，您可以使用最适合文件类型的 read 方法来读取其他内容:readAsArrayBuffer(非常适合处理大型二进制文件)或 readAsDataURL(如果您希望将内容快速嵌入到 DOM 对象中，如图像或音频文件，则非常适合)。

FileReader 包括几个事件监听器:onerror、onloadstart、onabort 和 onprogress(这对创建大文件的进度条和捕捉问题很有用)。

## URI 计划

URI 方案是 URIs 文件中的代表对象。资源可以是文件或 Blob，其对应的 URL 称为对象 URL。给定 Blob 或文件引用，可以使用 createObjectURL 创建对象 URL。例如:

```
var objecturl =  window.URL.createObjectURL(blob)
```

返回一个引用资源对象的 URL，类似于“blob:http % 3A//test . com/666 e 6730-f45c-47 C1-8012-CCC 706 f 17191”。

该字符串可以放在任何可以放置典型 URI 的位置，例如，如果对象 URI 属于图像，则可以放在图像标签的 src 上。对象资源和文档一样持久，所以刷新页面，它就没了。

## FileSaver

FileSaver 接口公开了将 blobs 写入用户下载目录的方法。实现相当简洁明了:

```
window.saveAs(blob, "filename")
```

然而，目前没有一个浏览器有这个功能。只有 Internet Explorer 10 支持简单的替代方式:[navigator . mssaveoropenblob](http://msdn.microsoft.com/en-us/library/ie/hh772332%28v=vs.85%29.aspx)以及[navigator . mssaveoblob](http://msdn.microsoft.com/en-us/library/windows/apps/hh441122.aspx)。但是正如你将看到的，有 shim 可以创建类似的功能，尽管没有流畅的用户体验。

所以这是关于文件 API 的底层细节，作为一个抽象的概念需要消化很多东西。但是不用担心。接下来是烹饪课，教你如何将这些原料变成你可以咀嚼的东西。

## 入党，树立榜样

我想我要为这个派对打扮一番，并在 http://adodson.com/graffiti 的[创建我自己的小照片处理工具。看看这个。从您的文件系统中选择一个图像，在屏幕上涂写，然后下载您的杰作——所有这些都使用文件 API(加上一些 canvas 方法和指针事件)。](http://adodson.com/graffiti)

![figure 1](img/865baa169127b8ac2e2b9f92e32c8e0e.png)
**图一。涂鸦文件 API App**

## 实现涂鸦应用

即使有花哨的图片，这个应用程序也有一个相当简单的前提:

*   使用 File + FileList 和 FileReader 选择一个图像作为背景。
*   将图像加载到 canvas 标签中，并使用 HTML5 Canvas API 操作图像。
*   使用 Blob(或 BlobBuilder)、saveAs(或 saveBlob)或带有对象 URL 的 anchor tag hack 下载新图像。

## 步骤 1:选择文件

用户可以通过多种方式为该应用程序选择图像。

*   使用表单输入选择文件
*   拖放文件
*   从剪贴板复制并粘贴文件

这些方法中的每一种都连接其侦听器来触发一个名为 readFile()的自定义函数，该函数使用 FileReader 的一个实例来提取文件数据并将用户图像添加到画布中。这是函数的代码。

```
	// readFile, loads File Objects (which are also images) into our Canvas
	// @param File Object
	function readFile(file){
	  // Create a new FileReader Object
	  var reader = new FileReader();
	  // Set an onload handler because we load files into it asynchronously
	  reader.onload = function(e){
	    // The response contains the Data-Uri, which we can then load into the canvas
	    applyDataUrlToCanvas( reader.result );
	  };
	  reader.reaAsDataURL(file);
	}
```

这里，readFile 获取文件引用(稍后显示)并创建 FileReader 对象的新实例。这个函数以 DataURL 的形式读取数据，但是它也可以以二进制或者甚至 ArrayBuffer 的形式读取数据。

要连接该函数，您可以通过前面提到的方法之一使用文件引用作为参数。

### 使用表单输入选择文件

FileAPI 目前没有定义一个本地方法来触发文件选择。但是使用 type=file 的旧的可信表单输入可以很好地完成这项工作:

```
<input type="file" name="picture" accept="image/png, image/jpeg"/>
```

尽管表单输入元素很笨拙，但它确实有一些新的附加属性，非常适合这个应用程序。

accept 属性提示哪些文件类型是可接受的。在这个例子中，这是 PNGs 和 JPEGs。这留给设备来适当地处理(例如，Windows 默认打开用户图片库，并且只显示这些类型的文件)。

*multiple* 标签让用户一步选择一个或多个文件。

接下来，您需要将事件侦听器绑定到表单输入的 change 事件，以便用户的选择自动触发 readFile:

```
	document.querySelector('input[name=picture]').onchange = function(e){
	     readFile(e.target.files[0]);
	}
```

这段代码只接受第一个用户选择的文件(不管是否使用了 multiple 属性)，然后调用 readFile，将文件作为第一个参数传入。

### 输入一点风格

这里显示的表单输入框不太符合我的审美，也可能不符合你的审美。因此，将它绝对放置在您选择的另一个不透明度为零的元素上(但要准备好它具有固定的宽度，这可能会干扰其他元素)。或者稍微复杂一点:在位于屏幕之外的输入框中调度一个定制的点击事件。点击此处阅读更多讨论内容[。](http://stackoverflow.com/questions/210643/in-javascript-can-i-make-a-click-event-fire-programmatically-for-a-file-input)

### 拖放文件

可以从文件资源管理器中拖动文件，并使用类似于表单输入的事件模型。当用户在画布上释放图像时，发生“放下”事件。该事件包含一个名为 dataTransfer 的属性，该属性有一个名为 files 的子属性。在下面的代码中，e.dataTransfer.files 是 FileList 的一个实例(如前所述，FileList 包含一个文件引用列表)，第一个文件项是 readFile 的参数。Webkit、Firefox 和 Internet Explorer 10 都支持这一功能。这里有一个例子:

```
	// stop FireFox from replacing the whole page with the file.
	canvas.ondragover = function () { return false; };
	// Add drop handler
	canvas.ondrop = function (e) {
	  e.preventDefault(); e = e || window.event;
	  var files = e.dataTransfer.files;
	  if(files){
	    readFile(files[0]);
	  }
	};
```

### 复制和粘贴文件数据

当用户将内容粘贴到文档中时，可以访问剪贴板数据。这些数据可以是文本和图像的混合，而不是像在表单输入控件或拖动文件中那样只有文件引用的文件列表。

在下面的代码中，剪贴板数据被遍历，对应于 type 和 kind 属性的条目(分别为“*/image”和“file”)被过滤掉。该项的文件实例是使用 getAsFile()获得的，它被传递给 readFile。

```
	// paste Clipboard data
	// Well not all of it just the images.
	document.onpaste = function(e){
	  e.preventDefault();
	  if(e.clipboardData&&e.clipboardData.items){
	    // pasted image
	    for(var i=0, items = e.clipboardData.items;i<items.length;i++){
	      if( items[i].kind==='file' && items[i].type.match(/^image/) ){
	        readFile(items[i].getAsFile());
	        break;
	      }
	    }
	  }
	  return false;
	};
```

第一步到此结束。我展示了三种获取文件引用和将文件数据加载到文档中的方法。很想知道有没有其他方法，请发评论。

## 步骤 2:将图像加载到画布上

步骤 1 中的 readFile 函数将生成的数据 url 传递给另一个自定义函数 applyDataUrlToCanvas。这个函数在画布上绘制一个选中的图像。它是这样工作的:

*   找出图像的宽度和高度。
*   找到图像的方向。
*   在画布上画出最适合的图像。

### 找出宽度和高度

使用 DOM Image 函数，可以很容易地确定任何图像的尺寸。这是一种简便的技术，大概是这样的:

```
	var img =  new Image();
	img.onload = function(){
	  // img.width
	  // img.height
	}
	img.src = dataURL;
```

### 找到方向

不幸的是，有一个宽度和高度的问题，这是一个很大的问题:照片可能是在纵向模式下拍摄的，并保存在横向模式下，或者图像可能是颠倒的。

一些相机不是以正确的方向保存图像，而是在图像的 [EXIF 数据](http://en.wikipedia.org/wiki/Exchangeable_image_file_format)中提供方向属性。这可以从图像的二进制数据中读取。

将数据 url 转换成二进制字符串很容易:

```
	var base64 = dataUrl.replace(/^.*?,/,'');
	var binary = atob(base64);
```

幸运的是，有一个由 Jacob Seidelin 编写的开源 [EXIF 客户端库](http://www.nihilogic.dk/labs/exif/)，它将 EXIF 数据作为对象返回。是的，太棒了！

```
	<script src="http://www.nihilogic.dk/labs/exif/exif.js"></script>
	<script src="http://www.nihilogic.dk/labs/binaryajax/binaryajax.js"></script>
	<script>
	var exif = EXIF.readFromBinaryFile(new BinaryFile(binary));
	//exif.Orientation
	</script>
```

Orientation 属性是 1–8 范围内的一个整数，对应于四次旋转和四次翻转(相当多余)。

### 将图像绘制到画布上

发现方向值后，您可以在画布上旋转和绘制。如果你想看我的算法，只要钻研源代码，你可以在[http://adodson.com/graffiti/](http://adodson.com/graffiti/)和[https://github.com/MrSwitch/graffiti](https://github.com/MrSwitch/graffiti)找到。

## 步骤 3:下载图像

最后一步是下载修改后的图像。在 FileAPI 的指令集中，您可以使用 Blob 接口在客户机中创建文件。用 Internet Explorer 10 中的新 msSaveBlob 或其他现代浏览器中的 download attribute hack(即将推出)来包装它，它们一起使您能够在客户端下载文件。

在[演示](http://adodson.com/graffiti)中亲自尝试一下。点击下载按钮。

该演示使用 Internet Explorer 10 中的 canvas.toBlob 方法来获取当前在 canvas 标记中绘制的图像的文件引用。对于 Chrome 和 FireFox 来说， [toBlob shim](https://github.com/eligrey/canvas-toBlob.js/) 非常好用。

```
	canvas.toBlob(function( file ){
	  // Create a blob file,

	  // then download with the FileSaver
	}
```

### 制作 Blob 对象实例的副本

我们应该可以跳过这一步，但是由于所有浏览器的一个怪癖，您不能直接从 canvas.toBlob 返回的 Blob 实例中使用 FileSave API，您需要复制它。

用于创建新 Blob 引用的 BlobBuilder 接口在 Internet Explorer 10、Chrome 和 FireFox 中受支持。但是这个接口已经被 Blob 构造函数所取代，而 Blob 构造函数目前支持有限。

首先，去掉 BlobBuilder 的供应商前缀:

```
	// Shim the BlobBuilder with the vendor prefixes
	window.BlobBuilder || (window.BlobBuilder = window.MSBlobBuilder||window.MozBlobBuilder||window.WebKitBlobBuilder);
```

接下来，让您的代码面向未来，并测试 Blob 构造函数。否则，构建一个 BlobBuilder 来构建 blob **。**(最好将方法包装在一个 try-catch 中。)Blob 在目前的 Chrome for Android 浏览器中漏洞百出。这是代码。

```
	var blob;
	if('Blob' in window){
	  try{
	    // The new Blob interface
	    blob = new Blob([file],{ "type" : "image/png"});
	  catch(e){}
	}
	if(!blob){
	  try{
	    // The deprecated BlobBuilder interface
	    var bb = new BlobBuilder();
	    bb.append( file );
	    blob = bb.getBlob("image/png");
	  }
	  catch(e){}
	}
```

### 使用 FileSaver 下载 Blob

FileSaver API 也是一个尚未被任何当前浏览器采用的标准。然而，在 Internet Explorer 10 的情况下，您可以使用 msSaveBlob 函数(这很棒)，对于 Chrome 和 FireFox，您至少可以使用供应商前缀来保护它们不受未来影响。因此，saveAs 函数需要一些大规模的调整:

```
window.saveAs || (window.saveAs == window.navigator.msSaveBlob || window.webkitSaveAs || window.mozSaveAs || window.msSaveAs /** || URL Download Hack **/ );
```

这种回退(在[https://gist.github.com/3552985](https://gist.github.com/3552985)中有完整描述)使用新图像的对象 URL 来填充 FileSaver 接口。对于支持锚标记上的下载属性的浏览器，shim 将 href 定义为对象 URL，然后调度 click 事件以强制它下载或在新的选项卡中打开。哦，我们编织了多么复杂的网。

最后，给定 blob 和文件名，saveAs 方法启动下载:

```
	var name = 'Graffiti.png';
	if(window.saveAs){
	  // Move the builder object content to a blob and
	  window.saveAs(blob, name);
	}
	else{
	  // Fallover, open as DataURL
	  window.open(canvas.toDataURL());
	}
```

这里，如果 saveAs shim 不兼容，fallover 将打开一个带有 [base64 数据 URL](http://en.wikipedia.org/wiki/Data_URI_scheme) 的新选项卡。这在 Internet Explorer 9 中可以工作，但是 Internet Explorer 8 被限制为 DataURI 的长度为 32 KB。

## 包扎

如果您还没有尝试过文件 API，我强烈建议您尝试一下。FileAPI 为浏览器开发类似桌面的应用程序打开了许多潜力。您可能希望对保存文件的位置有更多的控制，甚至覆盖现有的文件。但是现在，安全方面还是比较谨慎的，所以你不太可能很快看到这样的功能。规格仍在不断变化，但我在本文中强调的是，浏览器供应商已经投入了大量资金，即使有变化，也不太可能有太大变化。请不要引用我的话。

如果你需要支持旧的浏览器，看看我的用于 Internet Explorer 的 [dropfile.js](https://github.com/mrswitch/dropfile) shim，它填充了 FileReader 并创建了一个 base64 数据 URI，以及 [Downloadify](https://github.com/dcneiner/Downloadify) 用于替换 FileSaver 的基于 Flash 的 shim。

## 资源

*   [“保存/下载 JavaScript 生成的数据”](http://hackworthy.blogspot.com.au/2012/05/savedownload-data-generated-in.html)
*   [在 W3C 提交 API 草案](https://www.w3.org/TR/FileAPI/)
*   [“IE10 中的新 Blob 构造函数”](http://blogs.msdn.com/b/ie/archive/2012/07/20/new-blob-constructor-in-ie10.aspx)
*   [“通过 BlobBuilder 创建文件”](http://blogs.msdn.com/b/ie/archive/2012/01/27/creating-files-through-blobbuilder.aspx)
*   [“在 JavaScript 中使用文件，第 1 部分:基础知识”](http://www.nczonline.net/blog/2012/05/08/working-with-files-in-javascript-part-1/)

*本文最初发表于[http://msdn.microsoft.com/en-us/magazine/jj835793.aspx](http://msdn.microsoft.com/en-us/magazine/jj835793.aspx)，经允许在此转载。*

## 分享这篇文章