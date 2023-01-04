# 如何使用 HTML5 文件拖放

> 原文：<https://www.sitepoint.com/html5-file-drag-and-drop/>

将文件从桌面拖放到浏览器是 web 应用程序集成的最终目标之一。这是四篇系列文章中的第一篇，介绍了如何:

1.  允许将文件拖放到网页元素上
2.  在 JavaScript 中分析丢弃的文件
3.  在客户端加载和解析文件
4.  使用 XMLHttpRequest2 将文件异步上传到服务器
5.  上传时显示图形进度条
6.  使用渐进式改进来确保你的文件上传表单可以在任何浏览器中工作(对所有 IE6 粉丝来说是个好消息！)
7.  不用库，用普通的 JavaScript 编码。

唷。

## 大，坏的浏览器支持

在我们开始之前，本教程提到了几个先进的 HTML5 技术，所以预计支持是不完整的。代码今天仍然有效，但是 API 可能会改变，浏览器也会进化。

*   Firefox 和 Chrome 的最新版本支持所有功能，运行完美。
*   Opera 可以解析 JavaScript 中的文件，但是不能实现文件丢弃和 XMLHttpRequest2 上传。
*   IE 和 Safari 的桌面版不支持任何 API。
*   苹果已经禁用了 iPhone 和 iPad 版 Safari 上的 HTML 文件上传表单。有人知道为什么吗？

最后，注意我的代码展示了基本概念。几乎没有错误检查，您需要将其用于生产系统。

## HTML 和 CSS

这是我们的标准表单，带有文件输入类型。HTML5 唯一的特性是“多”属性，允许用户选择任意数量的文件。

我们将把文件上传到运行 PHP 的服务器上，但是不管你使用什么技术，代码都是一样的。隐藏的 MAX_FILE_SIZE 值指定了 300，000 字节——这是 PHP 使用的，但我们也会在客户端检查它，以防止上传大量文件。

```
 <form id="upload" action="upload.php" method="POST" enctype="multipart/form-data">

<fieldset>
<legend>HTML File Upload</legend>

<input type="hidden" id="MAX_FILE_SIZE" name="MAX_FILE_SIZE" value="300000" />

<div>
	<label for="fileselect">Files to upload:</label>
	<input type="file" id="fileselect" name="fileselect[]" multiple="multiple" />
	<div id="filedrag">or drop files here</div>
</div>

<div id="submitbutton">
	<button type="submit">Upload Files</button>
</div>

</fieldset>

</form>

<div id="messages">
<p>Status Messages</p>
</div> 
```

#filedrag 元素将被用作我们的文件拖放位置。元素在 CSS 中是隐藏的，但是如果支持拖放，它将在 JavaScript 中被启用:

```
 #filedrag
{
	display: none;
	font-weight: bold;
	text-align: center;
	padding: 1em 0;
	margin: 1em 0;
	color: #555;
	border: 2px dashed #555;
	border-radius: 7px;
	cursor: default;
}

#filedrag.hover
{
	color: #f00;
	border-color: #f00;
	border-style: solid;
	box-shadow: inset 0 3px 4px #888;
} 
```

我们还定义了一个. hover 类，当用户将文件拖动到元素上时，它会改变样式。在这种情况下，浏览器不会应用:hover 样式，但是我们可以在事件发生时用 JavaScript 添加这个类。

## 文件 API

W3C 文件 API 提供了几个对象。我们将使用:

*   **FileList** :表示所选文件的数组。
*   **文件**:代表一个单独的文件。
*   FileReader :一个允许我们在客户端读取文件数据并在 JavaScript 中使用它的接口。

## 附加 JavaScript 事件

是时候接触一些 JavaScript 了。我们没有使用 JavaScript 库，因此，为了节省我们的打字时间，我们将创建两个帮助器函数来按 ID 返回元素并输出状态消息:

```
 // getElementById
function $id(id) {
	return document.getElementById(id);
}

//
// output information
function Output(msg) {
	var m = $id("messages");
	m.innerHTML = msg + m.innerHTML;
} 
```

我们现在将检查文件 API 是否可用，并调用 Init()函数:

```
 // call initialization file
if (window.File && window.FileList && window.FileReader) {
	Init();
}

//
// initialize
function Init() {

	var fileselect = $id("fileselect"),
		filedrag = $id("filedrag"),
		submitbutton = $id("submitbutton");

	// file select
	fileselect.addEventListener("change", FileSelectHandler, false);

	// is XHR2 available?
	var xhr = new XMLHttpRequest();
	if (xhr.upload) {

		// file drop
		filedrag.addEventListener("dragover", FileDragHover, false);
		filedrag.addEventListener("dragleave", FileDragHover, false);
		filedrag.addEventListener("drop", FileSelectHandler, false);
		filedrag.style.display = "block";

		// remove submit button
		submitbutton.style.display = "none";
	}

} 
```

Init()函数:

1.  为文件输入元素设置“更改”事件侦听器。
2.  显示#filedrag 元素。
3.  设置“dragover”和“dragleave”事件侦听器以更改#filedrag 元素的样式。
4.  为#filedrag 元素设置“drop”事件侦听器。
5.  隐藏表单提交按钮—这不是必需的，因为我们将在选择文件时分析和上传文件。

可选地，当支持文件拖动时，您可以隐藏文件输入元素。就我个人而言，我更倾向于提供这两种选择，因为拖放会导致一些可用性问题。

XMLHttpRequest.upload 方法检查可以防止 Opera 中出现问题。浏览器支持 File、FileList 和 FileReader，但不支持拖放事件或 XMLHttpRequest2。因此它可以显示文件信息，但我们不想显示#filedrag 元素或删除 submit 按钮。

## 文件拖放样式更改

很少有人体验过在网页浏览器中拖放文件。事实上，有经验的网络用户可能不认为这是不可能的。因此，我们使用了一个表示“将文件放在这里”的元素。我们还想通过改变文件的样式来指示文件何时被拖动到#filedrag 位置:

```
 // file drag hover
function FileDragHover(e) {
	e.stopPropagation();
	e.preventDefault();
	e.target.className = (e.type == "dragover" ? "hover" : "");
} 
```

## 分析丢弃或选择的文件

我们使用相同的 FileSelectHandler()函数，不管是使用“Browse”选择了一个或多个文件，还是将它们拖动到 files 拖动位置:

```
 // file selection
function FileSelectHandler(e) {

	// cancel event and hover styling
	FileDragHover(e);

	// fetch FileList object
	var files = e.target.files || e.dataTransfer.files;

	// process all File objects
	for (var i = 0, f; f = files[i]; i++) {
		ParseFile(f);
	}

} 
```

功能:

1.  调用 FileDragHover()移除悬停样式并取消浏览器事件。这一点很重要，否则浏览器可能会试图显示该文件。
2.  获取文件列表对象。这将来自文件输入框(e.target.files)或#filedrag 元素(e.dataTransfer.files)。
3.  最后，该函数遍历文件列表中的所有文件对象，并将其作为参数传递给 ParseFile()函数…

```
 function ParseFile(file) {

	Output(
		"<p>File information: <strong>" + file.name +
		"</strong> type: <strong>" + file.type +
		"</strong> size: <strong>" + file.size +
		"</strong> bytes</p>"
	);

} 
```

该函数使用 File 对象提供的三个主要只读属性输出信息:

*   **。名称**:文件名(不包含路径信息)
*   **。类型**:MIME 类型，例如图像/jpeg、文本/普通等。
*   **。size** :文件大小，以字节为单位。

请 [**在 Firefox、Chrome、或 Opera 中查看演示页面**](https://blogs.sitepointstatic.com/examples/tech/filedrag/1/index.html) (不支持&拖拽)。你也可以[下载文件](https://blogs.sitepointstatic.com/examples/tech/filedrag/1/filedrag1.zip)来检查代码。

我们已经走过了很多地方。在我的下一篇文章中，我们将发现如何使用 HTML5 和 JavaScript … 打开拖放的文件

这篇文章也被翻译成[亚美尼亚语](http://www.fatcow.com/edu/html5-drop-hy/)

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。有关于 HTML5 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章