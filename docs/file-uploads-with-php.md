# 用 PHP 上传文件

> 原文：<https://www.sitepoint.com/file-uploads-with-php/>

在线相册中的图片、基于 web 的邮件客户端中的电子邮件附件以及提交给在线应用程序进行批处理的数据文件有什么共同之处？它们都依赖于从用户的网络浏览器通过互联网上传文件的能力。事实上，上传文件是我们日常使用的许多网站和基于网络的应用程序的一个重要功能。在这篇文章中，我将向您展示如何使用 PHP 为您的站点添加文件上传支持。

## 要求

处理文件上传并不困难，但是有一些小细节必须正确，否则上传会失败。首先，您需要确保 PHP 被配置为允许上传。检查您的`php.ini`文件，并确认`file_uploads`指令已设置`On`。

```
file_uploads = On

```

上传的文件首先存储在一个临时目录中(不要担心…您的 PHP 脚本可以在之后将文件移动到一个更永久的位置)。默认情况下，初始位置是系统的默认临时目录。您可以使用`php.ini`中的`upload_tmp_dir`指令指定不同的目录。无论如何，您应该验证 PHP 进程是否有适当的权限来写入所使用的目录。

```
upload_tmp_dir = "/tmp"

tboronczyk@zarkov:~$ ls -l / | grep tmp
drwxrwxrwt  13 root root 40960 2011-08-31 00:50 tmp

```

在您确定配置允许服务器接受上传的文件后，您可以将注意力集中在 HTML 细节上。与 HTML 中的大多数其他服务器端交互一样，上传文件使用表单。您的`<form>`元素必须使用 POST 方法，并将`enctype`属性设置为`multipart/form-data`。

```
<form action="upload.php" method="post" enctype="multipart/form-data">
```

## 编写上传过程的脚本

根据您自己的经验和我刚才提到的需求检查，您可能会猜到工作流文件上传的过程。

*   访问者查看一个 HTML 页面，该页面带有一个专门为支持文件上传而编写的表单
*   访问者提供他想要上传的文件并提交表单
*   浏览器对文件进行编码，并将其作为 POST 请求的一部分发送给服务器
*   PHP 接收表单提交，解码文件并保存在服务器上的临时位置
*   负责处理表单 post 的 PHP 脚本验证文件并以某种方式处理它，通常将它从临时位置移动到更永久的位置

添加对文件上传的支持需要您创建一个呈现给用户的 HTML 表单和一个 PHP 脚本来处理服务器上上传的文件。

### 超文本标记语言

HTML 表单提供了用户启动文件上传的界面。记住，`<form>`元素必须将其`method`属性设置为`post`，将其`enctype`属性设置为`multipart/form-data`。file `<input>`元素提供了一个字段，用于指定将要上传的文件。像任何其他表单元素一样，提供 name 属性非常重要，这样就可以在处理表单的 PHP 脚本中引用它。

基本文件上传表单的标记如下所示:

```
<form action="upload.php" method="post" enctype="multipart/form-data"> 
 <input type="file" name="myFile">
 <br>
 <input type="submit" value="Upload">
</form>
```

值得注意的是，不同的浏览器会呈现不同的文件字段。IE、Firefox 和 Opera 将它显示为一个文本字段，旁边有一个标记为“浏览”或“选择”的按钮。Safari 会将其渲染为标签为“选取文件”的按钮这在大多数情况下不是问题，因为用户已经习惯了字段在他们选择的浏览器中的呈现方式，并且知道如何使用它。然而，偶尔你会遇到坚持以某种方式展示的客户或设计师。由于浏览器强加的安全原因，可以应用于文件字段的 CSS 和 JavaScript 的数量极其有限。设计文件字段的样式可能很困难。如果外表对你来说很重要，我推荐你去看看彼得-保罗·科赫的[Styling a input type = " file "](http://www.quirksmode.org/dom/inputfile.html "CSS2/Dom - Styling an input type="file"")。

### 服务器端编程语言（Professional Hypertext Preprocessor 的缩写）

多维`$_FILES`数组提供了关于文件上传的信息。这个数组由 HTML 表单中分配给文件字段的名称进行索引，就像`$_GET`和`$_POST`的索引方式一样。每个文件的数组包含以下索引:

*   `$_FILES["myFile"]["name"]`存储来自客户端的原始文件名
*   `$_FILES["myFile"]["type"]`存储文件的 mime 类型
*   `$_FILES["myFile"]["size"]`存储文件的大小(以字节为单位)
*   `$_FILES["myFile"]["tmp_name"]`存储临时文件的名称
*   $_FILES["myFile"]["error"]存储传输过程中产生的任何错误代码

`move_uploaded_file()`功能将上传的文件从临时位置移动到永久位置。出于这个目的，您应该总是使用`move_uploaded_file()`而不是像`copy()`和`rename()`这样的函数，因为它会执行额外的检查来确保文件确实是由 HTTP POST 请求上传的。

如果您计划用用户提供的原始文件名保存文件，最好确保这样做是安全的。文件名不应包含任何会影响目标路径的字符，如斜杠。该名称也不应该导致文件覆盖同名的现有文件(除非这是您的应用程序的设计目的)。为了确保文件名安全，我用下划线替换了所有不是字母、数字或非常有限的标点符号的字符，然后在以该名称命名的文件已经存在时追加一个递增的数字。

下面是用 PHP 接收和处理文件上传的样子:

```
<?php
define("UPLOAD_DIR", "/srv/www/uploads/");

if (!empty($_FILES["myFile"])) {
    $myFile = $_FILES["myFile"];

    if ($myFile["error"] !== UPLOAD_ERR_OK) {
        echo "<p>An error occurred.</p>";
        exit;
    }

    // ensure a safe filename
    $name = preg_replace("/[^A-Z0-9._-]/i", "_", $myFile["name"]);

    // don't overwrite an existing file
    $i = 0;
    $parts = pathinfo($name);
    while (file_exists(UPLOAD_DIR . $name)) {
        $i++;
        $name = $parts["filename"] . "-" . $i . "." . $parts["extension"];
    }

    // preserve file from temporary directory
    $success = move_uploaded_file($myFile["tmp_name"],
        UPLOAD_DIR . $name);
    if (!$success) { 
        echo "<p>Unable to save file.</p>";
        exit;
    }

    // set proper permissions on the new file
    chmod(UPLOAD_DIR . $name, 0644);
}
```

代码首先确保上传的文件没有任何错误。然后，它确定一个安全的文件名，就像我刚才描述的那样，然后使用`move_uploaded_file()`将文件移动到它的最终目录。最后，调用`chmod()`来确保对新文件设置了合理的访问权限。

## 安全考虑

我们大多数人不会让完全陌生的人在我们的个人电脑上存储随机文件，然而当你在我们的应用程序中允许文件上传时，这正是你在做的事情。您可能希望用户上传自己的照片到个人资料页面，但是如果他尝试上传特制的、充满病毒的可执行文件呢？我想分享一些步骤，你可以采取这些步骤来最小化允许文件上传所固有的安全风险。

一个是验证上传文件的类型是否正确。依赖于`$_FILES["myFile"]["type"]`的值或文件名的扩展名都是不安全的，因为两者都很容易被欺骗。相反，使用类似于`exif_imagetype()`的函数来检查文件的内容，并确定它是否确实是 GIF、JPEG 或其他几种受支持的图像格式之一。如果`exif_imagetype()`不可用(该功能需要启用 Exif 扩展)，那么您可以使用`getimagesize()`。由`getimagesize()`返回的数组将包含被识别的图像类型。

```
<?php
// verify the file is a GIF, JPEG, or PNG
$fileType = exif_imagetype($_FILES["myFile"]["tmp_name"]);
$allowed = array(IMAGETYPE_GIF, IMAGETYPE_JPEG, IMAGETYPE_PNG);
if (!in_array($fileType, $allowed)) {
    // file type is not permitted
    ...
```

对于非图像文件，您可以使用`exec()`来调用 unix `file`实用程序。`file`通过在预期位置查找已知的二进制签名来确定文件的类型。

```
<?php
// verify the file is a PDF
$mime = "application/pdf; charset=binary";
exec("file -bi " . $_FILES["myFile"]["tmp_name"], $out);
if ($out[0] != $mime) {
    // file is not a PDF
    ...
```

您可以采取的另一个步骤是对 POST 请求的总大小和可以上传的文件数量进行硬性限制。为此，在`php.ini`中为`upload_max_size`、`post_max_size`和`max_file_uploads`指令指定一个合适的值。`upload_max_size`指令指定了文件上传的最大大小。除了上传的大小，您还可以使用`post_max_size`指令限制整个 POST 请求的大小。`max_file_uploads`是一个较新的指令(在版本 5.2.12 中添加),它限制了文件上传的数量。这三个指令有助于保护您的站点免受攻击，这些攻击试图通过造成沉重的网络流量或系统负载来破坏其可用性。

```
post_max_size = 8M
upload_max_size = 2M
max_file_uploads = 20

```

降低风险的第三步是用病毒扫描程序扫描上传的文件。在当今这个病毒和恶意软件泛滥的时代，这一点至关重要，尤其是如果您的网站后来允许其他人下载上传的文件，例如基于 web 的电子邮件客户端或(合法的)文件共享网站中的附件。有一个 提供对 [ClamAV](http://www.clamav.net/lang/en/ "php-clamav | Free software downloads at SourceForge.net") 的访问，但是当然你可以调用 ClamAV 的命令行工具，就像我为`file`演示的一样。

```
<?php
exec("clamscan --stdout " . $_FILES["myFile"]["tmp_name"], $out, $return);
if ($return) {
    // file is infected
    ...
```

## 摘要

您已经了解了使用您的站点或基于 web 的应用程序来支持文件上传是多么容易。为了上传成功，HTML 表单必须通过`multipart/form-data`编码的 POST 请求提交，PHP 必须允许使用`file_uploads`指令指定的传输。文件传输完毕后，负责处理上传的脚本使用在`$_FILES`数组中找到的信息将文件从临时目录移动到所需的位置。我还分享了一些额外的预防措施，您可以采取这些措施来保护自己和您的用户免受与允许文件上传相关的一些风险。您看到了如何确保文件名安全、验证文件类型、对上传流量施加硬性限制以及扫描病毒。

对于那些可能感兴趣的人来说，GitHub 上有本文的[补充代码。您可以查看、下载或克隆存储库，并使用代码来更好地理解上传文件的过程。](https://github.com/phpmasterdotcom/FileUploadsWithPHP "phpmasterdotcom/FileUploadsWithPHP")

如果你喜欢读这篇文章，你会爱上[可学的](https://learnable.com?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink)；向大师们学习新技能和技术的地方。会员可以即时访问 SitePoint 的所有电子书和交互式在线课程，如 [Jump Start PHP](https://learnable.com/books/jump-start-php?utm_source=sitepoint&utm_medium=link&utm_campaign=learnablelink) 。

对本文的评论已经关闭。有关于 PHP 的疑问？为什么不在我们的[论坛](https://www.sitepoint.com/forums/forumdisplay.php?34-PHP?utm_source=sitepoint&utm_medium=link&utm_campaign=forumlink)上问呢？

图片 via[VolsKinvols](http://www.shutterstock.com/gallery-450919p1.html "Stock Photos | Shutterstock: Royalty-Free Subscription Stock Photography & Vector Art")/[Shutterstock](http://www.shutterstock.com "Stock Photos and Royalty-Free Images by Subscription")

## 分享这篇文章