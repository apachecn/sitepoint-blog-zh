# 如何使用 HTML5 打开拖放的文件

> 原文：<https://www.sitepoint.com/html5-javascript-open-dropped-files/>

在我的上一篇文章[如何使用 HTML5 文件拖放&中，我们发现了如何使用 HTML5 文件 API，实现拖放事件，以及检索文件信息。今天，我们将尝试使用 JavaScript 在客户端加载文件。](https://www.sitepoint.com/html5-file-drag-and-drop)

## 为什么用 JavaScript 打开本地文件？

从 HTML 表单上传文件很麻烦。人们经常使用它将数兆字节的照片从相机传输到网络服务器。假设他们找到了文件，上传可能需要几分钟，但却发现照片错误、格式不受支持或文件大小超出允许范围。直到现在，开发者不得不依赖 Flash 或其他插件来提供更好的用户体验。

JavaScript 中的预处理有很多好处:

*   本地文件处理速度很快。
*   可以对文件进行分析，以确保它们的格式正确并且小于特定的大小。
*   上传前可以预览图像等文件。
*   可以在`canvas`元素上裁剪或调整图像大小，然后上传结果文件。

## FileReader 对象

FileReader 构成了 W3C 文件 API 的一部分，并提供了四种方法来从 File 对象中引用的文件异步加载数据:

*   **。readAsText(File f，[encoding])** :将文件 f 读入一个字符串。假设使用 UTF-8 编码，但是可选的编码参数可以指定不同的格式。
*   **。readAsDataURL(File f)** :将文件 f 读入一个编码的[数据 URL](http://en.wikipedia.org/wiki/Data_URI_scheme)
*   **。readAsBinaryString(File f)** :将文件 f 作为二进制字符串读取
*   **。readAsArrayBuffer(File f)** :将文件 f 作为一个 [ArrayBuffer](http://www.khronos.org/registry/typedarray/specs/latest/) 对象读取。

在下面的代码中，我们将使用前两种方法来加载和显示文本和图像文件。

## 在 JavaScript 中异步打开文件

下面是我们最初的 ParseFile()函数，当它被选中或放到#filedrag 元素上时，会被传递一个 File 对象:

```
 // output file information
function ParseFile(file) {

	Output(
		"<p>File information: <strong>" + file.name +
		"</strong> type: <strong>" + file.type +
		"</strong> size: <strong>" + file.size +
		"</strong> bytes</p>"
	);

} 
```

在状态更新之后，我们将检查我们是否有一个文本文件(text/plain，text/html，text/css 等)。)，使用 FileReader.readAsText()方法加载它并显示结果(转义< and >字符后):

```
 // display text
	if (file.type.indexOf("text") == 0) {
		var reader = new FileReader();
		reader.onload = function(e) {
			Output(
				"<p><strong>" + file.name + ":</strong></p><pre>" + 
				e.target.result.replace(/</g, "&lt;").replace(/>/g, "&gt;") +
				"</pre>"
			);
		}
		reader.readAsText(file);
	} 
```

同样，我们可以检查我们是否有一个图像文件(image/jpeg、image/gif、image/png 等)。)，使用 FileReader.readAsDataURL()方法将其加载到一个数据 URL 中，并将结果传递给一个`img`标记的 src 属性:

```
 // display an image
	if (file.type.indexOf("image") == 0) {
		var reader = new FileReader();
		reader.onload = function(e) {
			Output(
				"<p><strong>" + file.name + ":</strong><br />" +
				'<img src="' + e.target.result + '" /></p>'
			);
		}
		reader.readAsDataURL(file);
	} 
```

请 [**在 Firefox、Chrome、或 Opera 中查看演示页面**](https://blogs.sitepointstatic.com/examples/tech/filedrag/2/index.html) (不支持&拖拽)。你也可以[下载文件](https://blogs.sitepointstatic.com/examples/tech/filedrag/2/filedrag2.zip)来检查代码。

虽然这很有用，但最终，我们需要将文件上传到 web 服务器。敬请关注 [*如何使用 HTML5 和 Ajax 异步上传文件* …](https://www.sitepoint.com/html5-ajax-file-upload/)

## 分享这篇文章