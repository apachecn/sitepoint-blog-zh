# 如何在 HTML5 和 JavaScript 中创建图形文件上传进度条

> 原文：<https://www.sitepoint.com/html5-javascript-file-upload-progress-bar/>

在我之前的帖子中，我们发现了[如何使用 HTML5 文件拖放&、](https://www.sitepoint.com/html5-file-drag-and-drop/)[使用 JavaScript 打开文件](https://www.sitepoint.com/html5-javascript-open-dropped-files)和[使用 Ajax 异步上传文件](https://www.sitepoint.com/html5-ajax-file-upload)。在这个系列的最后一部分，我们将介绍这个过程中最激动人心的部分:图形化的进度条！

文件上传进度条提供了重要的用户反馈，但是众所周知它们很难实现。直到现在。Firefox 和 Chrome 都支持 XMLHttpRequest2 对象，该对象提供了一个 progress 事件处理程序。但是首先，让我们考虑我们的进度条将如何实现…

## HTML5 进度标签

新的`progress`标签提供了两个属性:

*   **值**:当前进度值
*   **max** :完成时的值

这个标签在这个演示中会很理想，尽管它在 Chrome 中受支持，但它的[只是在 Firefox 6](https://www.sitepoint.com/firefox-6-whats-new/) 中出现。此外，这两种浏览器都没有提供很多样式属性，所以我放弃了它，改用标准的`p`标签。这被作为子元素添加到 ID 为“progress”的`div`中。

## 设置进度条的样式

我们的`p`标签将在一个 250 像素大小的边框中显示文件名:

```
 #progress p
{
	display: block;
	width: 240px;
	padding: 2px 5px;
	margin: 2px 0;
	border: 1px inset #446;
	border-radius: 5px;
} 
```

对于绿色条本身，我创建了一个图形，其宽度是进度元素的两倍(500 像素)。左边的 250 像素是彩色的，右边的 250 像素是透明的:

![progress bar](img/2480d2be39db3ac8b02502a7c215bb6d.png)

该图形用作进度条的背景图像，位于“X% 0”处，其中 X%表示剩余(未完成)的比例，即

*   进度从“背景-位置:100% 0”开始，即剩余 100%
*   进度结束于“背景-位置:0% 0”，即没有任何剩余
*   “背景-位置:30% 0”表示已完成 70%:
    ![progress bar](img/439d5d1372469e42f94ced2865b7843e.png)

当上传成功或失败时，通过设置类来应用纯色:

```
 #progress p.success
{
	background: #0c0 none 0 0 no-repeat;
}

#progress p.failed
{
	background: #c00 none 0 0 no-repeat;
} 
```

## 用 JavaScript 实现进度条

我们现在可以修改 UploadFile()函数了。当遇到一个有效的 JPG 文件时，我们向#progress 元素添加一个新的`p`标记，并以文本形式添加文件名:

```
 // upload JPEG files
function UploadFile(file) {

	var xhr = new XMLHttpRequest();
	if (xhr.upload && file.type == "image/jpeg" && file.size <= $id("MAX_FILE_SIZE").value) {

		// create progress bar
		var o = $id("progress");
		var progress = o.appendChild(document.createElement("p"));
		progress.appendChild(document.createTextNode("upload " + file.name)); 
```

我们现在需要一个“progress”事件处理函数。这接收具有的对象。上膛了。总属性—计算新的背景位置需要一些数学知识:

```
 // progress bar
		xhr.upload.addEventListener("progress", function(e) {
			var pc = parseInt(100 - (e.loaded / e.total * 100));
			progress.style.backgroundPosition = pc + "% 0";
		}, false); 
```

如果您熟悉 Ajax，您会认出 onreadystatechange 事件处理程序。这将确定上传完成的时间，并相应地设置进度条的样式(如果上传成功，则将类别设置为“成功”):

```
 // file received/failed
		xhr.onreadystatechange = function(e) {
			if (xhr.readyState == 4) {
				progress.className = (xhr.status == 200 ? "success" : "failure");
			}
		}; 
```

最后，我们像以前一样将文件发送到我们的 PHP 服务器:

```
 // start upload
		xhr.open("POST", $id("upload").action, true);
		xhr.setRequestHeader("X-FILENAME", file.name);
		xhr.send(file);

	}

} 
```

我们终于有了一个解决方案，它:

1.  允许将文件拖放到网页元素上
2.  分析并显示客户端上丢弃的文件
3.  将文件异步上传到服务器
4.  在上传过程中显示一个图形进度条
5.  使用渐进式增强来支持大多数浏览器
6.  不需要 JavaScript 库。

请 [**查看演示页面**](https://blogs.sitepointstatic.com/examples/tech/filedrag/3/index.html) ，但是，请注意这是托管在没有 PHP 的服务器上，因此文件上传不会发生。为了测试它，请[下载文件](https://blogs.sitepointstatic.com/examples/tech/filedrag/3/filedrag3.zip)来检查代码并将其托管在你自己的服务器上。

我希望您喜欢这个系列，并且正在考虑文件拖放如何帮助您的 web 应用程序。

如果你喜欢读这篇文章，你会喜欢[可学的](https://learnable.com/)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Learn HTML5](https://learnable.com/courses/learn-html5-201) 。

## 分享这篇文章