# 如何使用 HTML5 和 Ajax 异步上传文件

> 原文：<https://www.sitepoint.com/html5-ajax-file-upload/>

在我之前的帖子中，我们发现了[如何使用 HTML5 文件拖放&和](https://www.sitepoint.com/html5-file-drag-and-drop/)[使用 HTML5 和 JavaScript 打开文件](https://www.sitepoint.com/html5-javascript-open-dropped-files)。现在我们有了一组有效的文件，可以将每个文件上传到服务器。该过程在后台异步发生，因此用户可以在发生时完成其他页面上的任务。

## HTML

让我们再次检查我们的 HTML 表单:

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
```

我们将上传文件到一个 PHP 页面，uploading。当用户点击“上传文件”时，该页面将处理 Ajax 上传请求和标准表单发布。

我们的 JavaScript 将确保只上传小于 300，000 字节(MAX_FILE_SIZE 中指定的值)的 JPG 图像。

## JavaScript

首先，我们需要在 FileSelectHandler()函数中添加一行代码，在选择或删除一个或多个文件时调用该代码。在我们的文件循环中，我们将调用一个额外的函数— UploadFile():

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
		UploadFile(f);
	}

} 
```

文件上传需要 XMLHttpRequest2 对象，该对象目前在 Firefox 和 Chrome 中可用。在我们进行 Ajax 调用之前，我们确保。upload()方法可用，并且我们有一个文件大小小于 MAX_FILE_SIZE 表单值的 JPG:

```
 // upload JPEG files
function UploadFile(file) {

	var xhr = new XMLHttpRequest();
	if (xhr.upload && file.type == "image/jpeg" && file.size <= $id("MAX_FILE_SIZE").value) { 
```

XMLHttpRequest。open()方法被设置为向 upload.php 发送数据，这是我们上传表单的动作属性。此外，我们为文件名设置了一个 HTTP 头，并将文件对象传递给。send()方法:

```
 // start upload
		xhr.open("POST", $id("upload").action, true);
		xhr.setRequestHeader("X_FILENAME", file.name);
		xhr.send(file);

	}

} 
```

## PHP

我们的 PHP 文件 upload.php 现在检查 X_FILENAME HTTP 头，以区分 Ajax 请求和标准表单发布:

```
 <?php
$fn = (isset($_SERVER['HTTP_X_FILENAME']) ? $_SERVER['HTTP_X_FILENAME'] : false); 
```

如果已经设置了文件名，PHP 可以检索发布的数据，并将其输出到“uploads”文件夹中的新文件中。令人惊讶的是，这可以在一行代码中实现:

```
 if ($fn) {

	// AJAX call
	file_put_contents(
		'uploads/' . $fn,
		file_get_contents('php://input')
	);
	echo "$fn uploaded";
	exit();

} 
```

可以使用常用的 PHP $_FILE 函数来处理标准的 HTML 多部分/表单数据帖子:

```
 else {

	// form submit
	$files = $_FILES['fileselect'];

	foreach ($files['error'] as $id => $err) {
		if ($err == UPLOAD_ERR_OK) {
			$fn = $files['name'][$id];
			move_uploaded_file(
				$files['tmp_name'][$id],
				'uploads/' . $fn
			);
			echo "<p>File $fn uploaded.</p>";
		}
	}

} 
```

您可以 [**查看演示页面**](https://blogs.sitepointstatic.com/examples/tech/filedrag/3/index.html) ，但是，请注意，该页面托管在不支持 PHP 的服务器上，不会上传。因此，请[下载文件](https://blogs.sitepointstatic.com/examples/tech/filedrag/3/filedrag3.zip)来检查代码并将其安装在你自己的 PHP 服务器上。

上面的代码可以工作，但是用户不知道文件上传是已经开始、完成还是失败。你需要阅读本系列的最后一期: [*如何在 HTML5 和 JavaScript 中创建文件上传进度条*……](https://www.sitepoint.com/html5-javascript-file-upload-progress-bar/)

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如真实世界的 [HTML5 & CSS3。](https://learnable.com/courses/html5-css3-for-the-real-world-1484?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)

对本文的评论已经关闭。有关于 HTML5 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?52-(X)HTML?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

## 分享这篇文章