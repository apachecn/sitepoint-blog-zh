# HTML5 文件拖放，分析，读取和上传

> 原文：<https://www.sitepoint.com/html5-file-drag-drop-read-analyze-upload-progress-bars/>

这是忙碌的一周。我们发现了新的 HTML5 APIs 如何帮助我们打开、阅读和上传用户拖放到浏览器窗口中的文件。本文总结了浏览器支持的技术和当前水平。

## HTML5 API 支持

在附加事件处理程序之前，您的 JavaScript 代码应该检查 File、FileList 和 FileReader 对象是否存在。在撰写本文时，Chrome、Firefox 和 Opera 的最新版本都支持这些功能:

```
 if (window.File && window.FileList && window.FileReader) { ... } 
```

尽管 Opera 支持这些对象，但它们只能通过标准的文件输入来使用——而不是拖放。因此，需要进一步检查；我建议使用 XMLHttpRequest2 上传方法，例如

```
 var xhr = new XMLHttpRequest();
if (xhr.upload) {
	... attach drag and drop events ...
} 
```

## 文件拖放

所有浏览器(iPhone 和 iPad 上的除外)都支持显示熟悉的“浏览”按钮的文件输入类型。HTML5 中引入了“multiple”属性，我们可以将更改事件处理程序附加到该字段:

```
 document.getElementById("fileselect").addEventListener("change", FileSelectHandler, false); 
```

Chrome 和 Firefox 还允许用户将一个或多个文件拖到选定的元素上。您可以附加事件处理程序，包括“dragover”和“dragleave”(用于更改样式)以及“drop”，用于检测丢失的文件，例如

```
 document.getElementById("filedrag").addEventListener("drop", FileSelectHandler, false); 
```

## 检索文件列表对象

HTML5 文件列表对象是一个类似数组的[文件对象](#fileobject)的集合。文件输入字段通过文件属性(event.target.files)返回文件列表。拖放的文件通过事件的 dataTransfer.files 属性(event.dataTransfer.files)返回 FileList 对象。

因此，我们可以使用单个事件处理程序来检索 FileList 对象:

```
 // cancel event default
e.preventDefault();

// fetch FileList object
var files = e.target.files || e.dataTransfer.files;

// process all File objects
for (var i = 0, file; file = files[i]; i++) {
	...
} 
```

取消默认事件很重要。这可以防止浏览器在文件被放入窗口时试图显示或处理文件。

## 分析文件对象

FileList 集合包含许多 File 对象。提供了三种有用的文件属性:

1.  **。名称**:文件名(不包含路径信息)
2.  **。类型**:MIME 类型，例如图像/jpeg、文本/普通等。
3.  **。size** :文件大小，以字节为单位。

可以在进一步处理或上传之前检查文件类型和大小，例如

```
 // process image files under 300,000 bytes
if (file.type.indexOf("image") == 0 && file.size < 300000) {
	...
} 
```

更多信息，请参考[如何使用 HTML5 和 JavaScript](https://www.sitepoint.com/html5-file-drag-and-drop/) 打开拖放的文件。

## 使用 FileReader 打开文件

HTML5 FileReader 对象允许你用 JavaScript 打开文本或二进制文件。如您所料，readAsText()方法用于检索文本内容，例如

```
 if (file.type.indexOf("text") == 0) {
    var reader = new FileReader();
    reader.onload = function(e) {
		// get file content
		var text = e.target.result;
		...
    }
    reader.readAsText(file);
} 
```

类似地，readAsDataURL()方法将二进制图像数据作为编码数据 URL 进行检索，该编码数据 URL 可以传递给图像 src 属性或画布元素:

```
 if (file.type.indexOf("image") == 0) {
    var reader = new FileReader();
    reader.onload = function(e) {
		document.getElementById("myimage").src = e.target.result;
    }
    reader.readAsDataURL(file);
} 
```

更多信息，请参考[如何使用 HTML5 和 JavaScript](https://www.sitepoint.com/html5-javascript-open-dropped-files) 打开拖放的文件。

## 使用 Ajax 上传文件

当用户停留在页面上时，可以将适当的文件上传到您的服务器。只需将一个文件对象传递给 XMLHttpRequest2 的 send()方法:

```
 var xhr = new XMLHttpRequest();
xhr.open("POST", "receivefile.php", true);
xhr.setRequestHeader("X_FILENAME", file.name);
xhr.send(file); 
```

注意，我们还将文件名作为 HTTP 头发送。这是可选的，但是它允许我们使用 PHP 之类的语言在服务器上使用其原始名称重新创建文件:

```
 file_put_contents(
	'uploads/' . $_SERVER['HTTP_X_FILENAME'],
	file_get_contents('php://input')
); 
```

更多信息请参考[如何使用 HTML5 和 Ajax 异步上传文件](https://www.sitepoint.com/html5-ajax-file-upload)。

## 创建上传进度条

我们还可以将“进度”事件附加到 XMLHttpRequest2 对象:

```
 xhr.upload.addEventListener("progress", ProgressHandler); 
```

处理程序接收一个带有。已加载(传输的字节数)和。total(文件大小)属性。因此，可以计算进度并将其传递给 HTML5 `progress`标签或任何其他元素，例如

```
 function ProgressHandler(e) {
	var complete = Math.round(e.loaded / e.total * 100);
	console.log(complete + "% complete");
} 
```

更多信息，请参考[如何在 HTML5 和 JavaScript](https://www.sitepoint.com/html5-javascript-file-upload-progress-bar/) 中创建图形文件上传进度条。

我希望你喜欢这个系列。文件拖放是改变 web 应用程序可用性的一个重要特性。HTML5 终于让它变得简单了。

## 分享这篇文章