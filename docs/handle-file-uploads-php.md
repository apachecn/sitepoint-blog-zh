# 如何用 PHP 处理文件上传

> 原文：<https://www.sitepoint.com/handle-file-uploads-php/>

PHP 程序员面临的一个常见挑战是如何接受访问者上传到你网站的文件。在最近出版的 SitePoint 一书第 12 章的额外摘录中:使用 PHP & MySQL(第四版)构建你自己的数据库驱动的网站，作者凯文·扬克，你将学习如何安全地接受网站访问者上传的文件并存储它们。

这本书的前 [4 章也可以在 sitepoint.com](https://www.sitepoint.com/article/php-mysql-tutorial/)上找到。如果你想离线阅读，你可以[下载 PDF 格式的章节](https://www.sitepoint.com/books/phpmysql4/samplechapters.php)。

我们将从基础开始:让我们编写一个允许用户上传文件的 HTML 表单。HTML 用它的`<input type="file"/>`标签让这变得很容易。但是，默认情况下，只发送用户选择的文件名。为了将文件本身与表单数据一起提交，我们需要将`enctype="multipart/form-data"`添加到`<form>`标签中:

```
<form action="index.php" method="post" 

    enctype="multipart/form-data"> 

  <div><label id="upload">Select file to upload: 

    <input type="file" id="upload" name="upload"/></label></div> 

  <div> 

    <input type="hidden" name="action" value="upload"/> 

    <input type="submit" value="Submit"/> 

  </div> 

</form>
```

正如我们所看到的，一个 PHP 脚本(本例中为`index.php`)将处理上面表单提交的数据。关于上传文件的信息出现在 PHP 自动创建的名为`$_FILES`的数组中。如您所料，数组中一个名为`$_FILES['upload']`(来自`<input/>`标签的 name 属性)的条目将包含关于本例中上传的文件的信息。然而，`$_FILES['upload']`并没有存储上传文件的内容，而是包含了另一个数组。因此，我们使用第二组方括号来选择我们想要的信息:

```
$_FILES['upload']['tmp_name']
```

在系统临时文件目录中提供存储在 web 服务器硬盘上的文件的名称，除非使用`php.ini`文件中的`upload_tmp_dir`设置指定了另一个目录。这个文件只在负责处理表单提交的 PHP 脚本运行时才会被保存。因此，如果您以后想要使用上传的文件(例如，存储它以便在站点上显示)，您需要在其他地方制作一个副本。为此，请使用上一节中介绍的复制功能。

`$_FILES['upload']['name']`

提交文件之前，在客户端计算机上提供文件的名称。如果您制作了临时文件的永久副本，您可能希望使用它的原始名称，而不是上面描述的自动生成的临时文件名。

`$_FILES['upload']['size']`

提供文件的大小(以字节为单位)。

```
$_FILES['upload']['type']
```

提供文件的 MIME 类型(有时称为文件类型或内容类型，一种用于描述文件格式的标识符，例如，`text/plain`、`image/gif`等)。

记住，'`upload`'只是提交文件的`<input/>`标签的`name`属性，所以实际的数组索引将取决于该属性。

您可以使用这些变量来决定是接受还是拒绝上传的文件。例如，在一个图片库中，我们只会对 JPEG 和 GIF 以及 PNG 文件感兴趣。这些文件的 MIME 类型分别为`image/jpeg`、`image/gif`和`image/png`，但是为了适应不同浏览器之间的差异，您应该使用正则表达式来验证上传文件的类型:

```
if (preg_match('/^image/p?jpeg$/i', $_FILES['upload']['type']) or 

    preg_match('/^image/gif$/i', $_FILES['upload']['type']) or 

    preg_match('/^image/(x-)?png$/i', $_FILES['upload']['type'])) 

{ 

  // Handle the file... 

} 

else 

{ 

  $error = 'Please submit a JPEG, GIF, or PNG image file.'; 

  include $_SERVER['DOCUMENT_ROOT'] . '/includes/error.html.php'; 

  exit(); 

}
```

确切的 MIME 类型取决于所使用的浏览器。Internet Explorer 对 JPEG 图像使用`image/pjpeg`，对 PNG 图像使用`image/x-png`，而 Firefox 和其他浏览器分别使用`image/jpeg`和`image/png`。有关正则表达式语法的帮助，请参阅第 8 章，用正则表达式进行内容格式化。

虽然您可以使用类似的技术来禁止过大的文件(通过检查`$_FILES['upload']['size']`变量)，但我建议不要这样做。在检查该值之前，文件已经上传并保存在临时目录中。如果您因为磁盘空间和/或带宽有限而试图拒绝文件，那么大文件仍然可以上传，即使它们几乎会立即被删除，这可能对您来说是个问题。

相反，您可以提前告诉 PHP 您希望接受的最大文件大小。有两种方法可以做到这一点。首先是调整您的`php.ini`文件中的`upload_max_filesize`设置。默认值是 2MB，所以如果您想接受大于这个值的上传，您需要立即更改这个值。第二个限制影响表单提交的总大小，由`php.ini`中的`post_max_size`设置强制执行。它的默认值是 8MB，所以如果你想接受非常大的上传，你也需要修改这个设置。

第二种方法是在表单中包含一个名为`MAX_FILE_SIZE`的隐藏的`<input/>`字段，以及您希望该表单接受的最大文件大小作为其值。出于安全原因，这个值不能超过您的`php.ini`中的`upload_max_filesize`设置，但是它确实为您提供了一种方法来接受不同页面上不同的最大尺寸。例如，下面的表单将允许上传高达 1 千字节(1024 字节)的数据:

```
<form action="upload.php" method="post" 

    enctype="multipart/form-data"> 

  <p><label id="upload">Select file to upload: 

  <input type="hidden" name="MAX_FILE_SIZE" value="1024"/> 

    <input type="file" id="upload" name="upload"/></label></p> 

  <p> 

    <input type="hidden" name="action" value="upload"/> 

    <input type="submit" value="Submit"/> 

  </p> 

</form>
```

请注意，隐藏的`MAX_FILE_SIZE`字段必须在表单中的任何`<input type="file"/>`标签之前，以便 PHP 在接收任何提交的文件之前知道这个限制。还要注意，恶意用户可以很容易地绕过这个限制，他们只需编写自己的没有`MAX_FILE_SIZE`字段的表单。对于大文件上传的自动防故障安全，使用`php.ini`中的`upload_max_filesize`设置。

##### 分配唯一的文件名

正如我上面解释的，要保存一个上传的文件，你需要把它复制到另一个目录。虽然您可以访问每个上传文件的名称及其`$_FILE['upload']['name']`变量，但您不能保证两个同名文件不会被上传。在这种情况下，以其原始名称存储文件可能会导致较新的上传覆盖旧的上传。

出于这个原因，您通常希望采用一种方案，允许您为每个上传的文件分配一个唯一的文件名。使用系统时间(可以使用 PHP time 函数访问)，可以很容易地根据自 1970 年 1 月 1 日以来的秒数产生一个名字。但是如果两个文件恰好在一秒钟之内被上传呢？为了帮助防范这种可能性，我们还将在文件名中使用客户端的 IP 地址(由 PHP 自动存储在`$_SERVER['REMOTE_ADDR']`中)。由于您不太可能在一秒钟内收到来自同一个 IP 地址的两个文件，因此对于大多数目的来说，这是一个可接受的解决方案:

```
// Pick a file extension 

if (preg_match('/^image/p?jpeg$/i', $_FILES['upload']['type'])) 

{ 

  $ext = '.jpg'; 

} 

else if (preg_match('/^image/gif$/i', $_FILES['upload']['type'])) 

{ 

  $ext = '.gif'; 

} 

else if (preg_match('/^image/(x-)?png$/i', 

    $_FILES['upload']['type'])) 

{ 

  $ext = '.png'; 

} 

else 

{ 

  $ext = '.unknown'; 

} 

// The complete path/filename 

$filename = 'C:/uploads/' . time() . $_SERVER['REMOTE_ADDR'] . $ext; 

// Copy the file (if it is deemed safe) 

if (!is_uploaded_file($_FILES['upload']['tmp_name']) or 

    !copy($_FILES['upload']['tmp_name'], $filename)) 

{ 

  $error = "Could not  save file as $filename!"; 

  include $_SERVER['DOCUMENT_ROOT'] . '/includes/error.html.php'; 

  exit(); 

}
```

在上面的代码中需要注意的是使用`is_uploaded_file`函数来检查文件是否“安全”如果作为参数传递的文件名(本例中是`$_FILES['upload']['tmp_name']`)实际上是作为表单提交的一部分上传的，那么这个函数只返回`TRUE`。如果一个恶意用户加载了这个脚本，并手动指定了一个文件名，如`/etc/passwd`(Linux 服务器上存储的系统密码)，并且您没有使用`is_uploaded_file`来检查`$_FILES['upload']`是否确实引用了一个上传的文件，那么您的脚本可能会被用来将您服务器上的敏感文件复制到一个目录中，从这个目录中可以通过 Web 公开访问这些文件！因此，在信任包含上传文件的文件名的 PHP 变量之前，一定要使用`is_uploaded_file`来检查它。

我在上面的代码中使用的第二个技巧是将`is_uploaded_file`和 copy 组合在一起作为 if 语句的条件。如果`is_uploaded_file($_FILES['upload']['tmp_name'])`的结果是`FALSE`(make`!is_uploaded_file($_FILES['upload']['tmp_name'])``TRUE`)，当 PHP 看到 or 运算符将两个函数调用分开时，它会立即知道整个条件将是`TRUE`。为了节省时间，它会避免运行 copy，所以当`is_uploaded_file`返回`FALSE`时，文件不会被复制。另一方面，如果`is_uploaded_file`返回`TRUE`，PHP 继续复制文件。然后`copy`的结果决定是否显示错误信息。类似地，如果我们使用了`and`操作符而不是`or`，条件第一部分的`FALSE`结果将导致 PHP 跳过第二部分。if 语句的这一特性被称为短路评估，也适用于其他条件结构，如`while`和`for`循环。

最后，请注意，在上面的脚本中，我在路径中使用了 UNIX 风格的正斜杠(/)，尽管它是一个 Windows 路径。如果我使用反斜杠，我必须用双反斜杠(\)来替换它们，以避免 PHP 将它们解释为转义字符。然而，PHP 足够聪明，当它在 Windows 系统上运行时，可以将文件路径中的正斜杠转换成反斜杠。因为我们也可以像在非 Windows 系统上一样使用单斜杠(/)，所以在 PHP 中文件路径一般采用正斜杠会使你的脚本更具可移植性。

## 分享这篇文章